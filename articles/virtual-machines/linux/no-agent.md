---
title: 프로 비전 에이전트 없이 Linux 이미지 만들기
description: Azure에서 프로 비전 에이전트 없이 일반화 된 Linux 이미지를 만듭니다.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: d177e7fd7d18b24f9d8fd7f3e6662abe16bba317
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045334"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>프로 비전 에이전트 없이 일반화 된 이미지 만들기

Microsoft Azure은 Linux Vm에 대 한 프로 비전 에이전트를 [walinuxagent](https://github.com/Azure/WALinuxAgent) 또는 [클라우드-init](https://github.com/canonical/cloud-init) (권장) 형식으로 제공 합니다. 하지만 다음과 같은 프로 비전 에이전트로 이러한 응용 프로그램 중 하나를 사용 하지 않으려는 경우가 있습니다.

- Linux 배포판/버전은 클라우드-init/Linux 에이전트를 지원 하지 않습니다.
- 호스트 이름 같은 특정 VM 속성을 설정 해야 합니다.

> [!NOTE] 
>
> 속성을 설정 하거나 어떤 형태의 프로 비전도 수행 하지 않아도 되는 경우 특수 이미지를 만드는 것이 좋습니다.

이 문서에서는 프로 비전 에이전트를 설치 하지 않고 Azure 플랫폼 요구 사항을 충족 하 고 호스트 이름을 설정 하기 위해 VM 이미지를 설정 하는 방법을 보여 줍니다.

## <a name="networking-and-reporting-ready"></a>네트워킹 및 보고 준비 완료

Linux VM이 Azure 구성 요소와 통신 하도록 하려면 DNS 확인 및 경로 관리 뿐만 아니라 가상 네트워크에서 호스트 IP를 검색 하는 데 DHCP 클라이언트가 필요 합니다. 대부분의 배포판은 이러한 유틸리티를 기본 제공 합니다. Linux 배포판 공급 업체에 의해 Azure에서 테스트 된 도구에는 `dhclient` , `network-manager` 및 기타가 포함 `systemd-networkd` 됩니다.

> [!NOTE]
>
> 현재 프로 비전 에이전트 없이 일반화 된 이미지를 만드는 것은 DHCP 사용 Vm만 지원 합니다.

네트워킹이 설정 및 구성 된 후에는 "준비"로 설정 해야 합니다. 이렇게 하면 VM이 성공적으로 프로 비전 되었음을 Azure에 알려줍니다.

> [!IMPORTANT]
>
> Azure에 대 한 준비를 보고 하지 못하면 VM이 다시 부팅 됩니다.

## <a name="demosample"></a>데모/샘플

이 데모에서는 기존 Marketplace 이미지 (이 경우 Debian Buster VM)를 사용 하 여 Linux 에이전트 (walinuxagent)를 제거 하 고, VM이 "준비" 된 것으로 Azure에 보고 하는 가장 기본적인 프로세스를 만드는 방법을 보여 줍니다.

### <a name="create-the-resource-group-and-base-vm"></a>리소스 그룹 및 기본 VM을 만듭니다.

```bash
$ az group create --location eastus --name demo1
```

기본 VM 만들기:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>이미지 프로 비전 에이전트 제거

VM이 프로 비전 되 면 해당 VM에 대해 SSH를 수행 하 고 Linux 에이전트를 제거할 수 있습니다.

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>VM에 필요한 코드 추가

또한 VM 내에서 Azure Linux 에이전트를 제거 했으므로 준비를 보고 하는 메커니즘을 제공 해야 합니다. 

#### <a name="python-script"></a>Python 스크립트

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>일반 단계 (Python을 사용 하지 않음)

VM에 Python이 설치 되어 있지 않거나 사용할 수 없는 경우 다음 단계를 사용 하 여 프로그래밍 방식으로이 스크립트 논리를 재현할 수 있습니다.

1. `ContainerId` `InstanceId` WireServer:에서 응답을 구문 분석 하 여 및를 검색 `curl -X GET -H 'x-ms-version: 2012-11-30' http://$168.63.129.16/machine?comp=goalstate` 합니다.

2. 다음 XML 데이터를 생성 하 여 구문 분석 된 `ContainerId` 와 `InstanceId` 위의 단계에서 삽입 합니다.
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. WireServer에이 데이터를 게시 합니다.`curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>처음 부팅 시 코드 실행 자동화

이 데모에서는 최신 Linux 배포판에서 가장 일반적인 init 시스템용 systemd를 사용 합니다. 따라서이 보고서 준비 메커니즘을 적절 한 시간에 실행 하는 가장 쉽고 기본적인 방법은 systemd 서비스 단위를 만드는 것입니다. 다음 단위 파일을에 추가할 수 있습니다 `/etc/systemd/system` .이 예에서는 단위 파일의 이름을로 지정할 수 있습니다 `azure-provisioning.service` .

```
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

이 systemd 서비스는 기본 프로 비전을 위한 세 가지 작업을 수행 합니다.

1. Azure에 준비 된 보고서 (성공적으로 완료 되었음을 나타냄).
1. IMDS에서이 데이터를 당겨 사용자가 제공한 VM 이름에 따라 VM의 이름을 바꿉니다.
1. 을 사용 하지 않도록 설정 하 여 첫 번째 부팅 에서만 실행 되며 이후에 다시 부팅할 때는 실행 되지 않습니다.

파일 시스템의 단위를 사용 하 여 사용 하도록 설정 하려면 다음을 실행 합니다.

```
$ sudo systemctl enable azure-provisioning.service
```

이제 VM을 일반화 하 고 이미지를 만들 수 있습니다. 

#### <a name="completing-the-preparation-of-the-image"></a>이미지 준비 완료

개발 컴퓨터로 돌아가서 다음을 실행 하 여 기본 VM에서 이미지를 만들 준비를 합니다.

```
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

그리고이 VM에서 이미지를 만듭니다.

```
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

이제 이미지에서 새 VM (또는 여러 Vm)을 만들 준비가 되었습니다.

```
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> `--enable-agent` `false` 이미지에서 만들이 VM에 walinuxagent이 존재 하지 않기 때문에를로 설정 하는 것이 중요 합니다.

이 VM은 성공적으로 프로 비전 되어야 합니다. 새로 프로 비전 VM에 로그인 하면 보고서 준비 된 systemd 서비스의 출력을 볼 수 있습니다.

```
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Support(지원)

사용자 고유의 프로 비전 코드/에이전트를 구현 하는 경우이 코드에 대 한 지원을 소유 하 고 있으므로 Microsoft 지원에서 프로 비전 인터페이스와 관련 된 문제를 조사할 수 있습니다. 이 영역에서 지속적으로 향상 되 고 변경 되 고 있으므로 API 변경의 프로 비전을 위해 클라우드 init 및 Azure Linux 에이전트에서 변경 내용을 모니터링 해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Linux 프로 비전](provisioning.md)을 참조 하세요.
