---
title: 빠른 시작-사설 클라우드에서 VMware VM 만들기
description: CloudSimple 사설 클라우드에서 및 VMware VM을 만드는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7d21035fc3e9e80344264b9fde21820162f376d3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816716"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>사설 클라우드에서 VMware 가상 머신 만들기

사설 클라우드에서 가상 컴퓨터를 만들려면 먼저 Azure Portal에서 CloudSimple 포털에 액세스 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

1. **모든 서비스**를 선택합니다.
2. **Cloudsimple 서비스**를 검색 합니다.
3. 사설 클라우드를 만들려는 CloudSimple 서비스를 선택 합니다.
4. **개요** 페이지에서 **Cloudsimple 포털로 이동** 을 클릭 하 여 cloudsimple 포털에 대 한 새 브라우저 탭을 엽니다.  메시지가 표시 되 면 Azure 로그인 자격 증명을 사용 하 여 로그인 합니다.  

    ![CloudSimple 포털 시작](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>VCenter 웹 ui 시작

이제 vCenter를 실행 하 여 가상 컴퓨터 및 정책을 설정할 수 있습니다.

VCenter에 액세스 하려면 CloudSimple 포털에서 시작 합니다. 홈 페이지의 **일반 작업**에서 **Vsphere 클라이언트 시작**을 클릭 합니다.  사설 클라우드를 선택 하 고 사설 클라우드에서 **vSphere 클라이언트 시작** 을 클릭 합니다.

   ![VSphere 클라이언트 시작](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO 또는 vSphere 템플릿 업로드

> [!WARNING]
> ISO 업로드의 경우 vSphere HTML5 클라이언트를 사용 합니다.  Flash 클라이언트를 사용 하면 오류가 발생할 수 있습니다.

1. 가상 컴퓨터를 만들고 로컬 시스템에서 사용할 수 있도록 vCenter에 업로드 하려는 ISO 또는 vSphere 템플릿을 가져옵니다.

2. VCenter에서 **디스크** 아이콘을 클릭 하 고 **vsandatastore 저장소**를 선택 합니다. **파일** , **새 폴더**를 차례로 클릭 합니다.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. ISO 파일을 저장할 폴더를 만듭니다.

4. 새로 만든 폴더로 이동 하 고 **파일 업로드**를 클릭 합니다. 화면의 지시에 따라 ISO를 업로드 합니다.

## <a name="create-a-virtual-machine-in-vcenter"></a>VCenter에서 가상 컴퓨터 만들기

1. VCenter에서 **호스트 및 클러스터** 아이콘을 클릭 합니다.

2. **작업** 을 마우스 오른쪽 단추로 클릭 하 고 **새 가상 컴퓨터**를 선택 합니다.
    
    ![가상 머신 만들기](media/create-vcenter-virtual-machine-01.png)

3. **새 가상 컴퓨터 만들기** 를 선택 하 고 **다음**을 클릭 합니다.

    ![새 가상 컴퓨터 마법사](media/create-vcenter-virtual-machine-02.png)

4. 컴퓨터 이름을로, **작업 VM의** 폴더를 선택 하 고 **다음**을 클릭 합니다.

    ![이름 및 폴더 선택](media/create-vcenter-virtual-machine-03.png)

5. **작업** 계산 리소스를 선택 하 고 **다음**을 클릭 합니다.

    ![계산 리소스 선택](media/create-vcenter-virtual-machine-04.png)

6. **Vsandatastore 저장소** 를 선택 하 고 **다음**을 클릭 합니다.

    ![저장소 선택](media/create-vcenter-virtual-machine-05.png)

7. 기본 ESXi 6.5 호환성 선택 항목을 유지 하 고 **다음**을 클릭 합니다.

    ![호환성 선택](media/create-vcenter-virtual-machine-06.png)

8. 가상 컴퓨터에 대 한 ISO의 게스트 OS를 선택 하 고 **다음**을 클릭 합니다.

    ![게스트 OS 사용자 지정](media/create-vcenter-virtual-machine-07.png)

9. 하드 디스크 및 네트워크 옵션을 선택 합니다. 새 CD/DVD 드라이브의 경우 **데이터 저장소 ISO 파일**을 선택 합니다.  공용 IP 주소에서이 가상 머신으로의 트래픽을 허용 하려면 **vm-1**로 네트워크를 선택 합니다.

    ![하드웨어 사용자 지정 선택](media/create-vcenter-virtual-machine-08.png)

10. 선택 창이 열립니다. 이전에 Iso 및 Templates 폴더에 업로드 한 파일을 선택 하 고 **확인**을 클릭 합니다.

    ![ISO 선택](media/create-vcenter-virtual-machine-10.png)

11. 설정을 검토 하 고 **확인** 을 클릭 하 여 가상 컴퓨터를 만듭니다.

    ![검토 옵션](media/create-vcenter-virtual-machine-11.png)

이제 가상 머신이 워크 로드 계산 리소스에 추가 되 고 사용할 준비가 됩니다. 

![VCenter의 새 가상 머신](media/create-vcenter-virtual-machine-12.png)

이제 기본 설정이 완료 되었습니다. 온-프레미스 가상 머신 인프라를 사용 하는 방법과 유사한 방식으로 사설 클라우드를 사용할 수 있습니다.

다음 섹션에는 사설 클라우드 작업에 대 한 DNS 및 DHCP 서버를 설정 하 고 기본 네트워킹 구성을 수정 하는 방법에 대 한 선택적 정보가 포함 되어 있습니다.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>VCenter에 사용자 및 Id 소스 추가 (선택 사항)

CloudSimple은 사용자 이름 **cloudowner@cloudsimple.local** 으로 기본 vCenter 사용자 계정을 할당 합니다. 시작 하려면 추가 계정 설정이 필요 하지 않습니다.  CloudSimple은 일반적으로 일반적인 작업을 수행 하는 데 필요한 권한을 관리자에 게 할당 합니다.  온-프레미스 active directory 또는 Azure AD를 사설 클라우드의 [추가 id 원본](https://docs.azure.cloudsimple.com/set-vcenter-identity/) 으로 설정 합니다.

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS 및 DHCP 서버 만들기 (선택 사항)

사설 클라우드 환경에서 실행 되는 응용 프로그램 및 작업에는 조회 및 IP 주소 할당을 위한 이름 확인 및 DHCP 서비스가 필요 합니다. 이러한 서비스를 제공 하려면 적절 한 DHCP 및 DNS 인프라가 필요 합니다. 사설 클라우드 환경에서 이러한 서비스를 제공 하도록 vCenter의 가상 머신을 구성할 수 있습니다.

### <a name="prerequisites"></a>전제 조건

* VLAN이 구성 된 분산 포트 그룹

* 온-프레미스 또는 인터넷 기반 DNS 서버로 경로 설정

* 가상 컴퓨터 템플릿 또는 ISO로 가상 컴퓨터 만들기

다음 링크는 Linux 및 Windows에서 DHCP 및 DNS 서버를 설정 하는 방법에 대 한 지침을 제공 합니다.

### <a name="linux-based-dns-server-setup"></a>Linux 기반 DNS 서버 설치

Linux에서는 DNS 서버를 설정 하기 위한 다양 한 패키지를 제공 합니다.  다음은 오픈 소스 바인드 DNS 서버를 설정 하는 지침에 대 한 링크입니다.

[설정 예](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Windows 기반 설치

이러한 Microsoft 문서에서는 Windows server를 DNS 서버 및 DHCP 서버로 설정 하는 방법을 설명 합니다.
<br>
[Windows Server를 DNS 서버로](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[DHCP 서버로 서의 Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>네트워킹 구성 사용자 지정 (선택 사항)

CloudSimple 포털의 네트워크 페이지를 사용 하 여 가상 컴퓨터의 방화벽 테이블 및 공용 IP 주소에 대 한 구성을 지정할 수 있습니다.

### <a name="allocate-public-ips"></a>공용 Ip 할당

1. CloudSimple 포털에서 **네트워크 > 공용 IP** 로 이동 합니다.
2. **공용 IP 할당**을 클릭 합니다.
3. IP 주소 항목을 식별 하는 이름을 입력 합니다.
4. 사설 클라우드의 위치를 선택 합니다.
5. 원하는 경우 슬라이더를 사용 하 여 유휴 시간 제한을 변경 합니다.
6. 공용 IP 주소를 할당 하려는 로컬 IP 주소를 입력 합니다.
7. 원하는 경우 연결 된 DNS 이름을 입력 합니다.
8. **완료**를 클릭합니다.

    ![공용 IP](media/quick-create-pc-public-ip.png)

공용 IP 주소를 할당 하는 작업이 시작 됩니다. 작업 **> 작업** 페이지에서 작업의 상태를 확인할 수 있습니다. 할당이 완료 되 면 공용 Ip 페이지에 새 항목이 표시 됩니다.

이 IP 주소를 매핑해야 하는 가상 머신은 위에 지정 된 로컬 주소를 사용 하 여 구성 해야 합니다. IP 주소를 구성 하는 절차는 가상 컴퓨터 운영 체제에만 적용 됩니다. 올바른 절차는 가상 머신 운영 체제에 대 한 설명서를 참조 하세요.

#### <a name="example"></a>예제

예를 들어 Ubuntu 16.04에 대 한 세부 정보는 다음과 같습니다.

파일 ```/etc/network/interfaces```의 inet 주소 패밀리 구성에 정적 메서드를 추가 합니다. 주소, 네트워크 마스크 및 게이트웨이 값을 변경 합니다. 이 예에서는 eth0 인터페이스, 내부 IP 주소 192.168.24.10, 게이트웨이 주소 192.168.24.1 및 네트워크 마스크 255.255.255.0을 사용 합니다. 

```interfaces``` 파일을 편집합니다.

```
sudo vi /etc/network/interfaces
```

파일에서 ```interfaces``` 다음 섹션을 업데이트 합니다.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

인터페이스를 사용 하지 않도록 설정 합니다.

```
sudo ifdown eth0
```

인터페이스를 다시 사용 하도록 설정 합니다.

```
sudo ifup eth0
```

기본적으로 인터넷에서 들어오는 모든 트래픽이 **거부**됩니다. 다른 포트를 열려면 [방화벽 테이블](https://docs.azure.cloudsimple.com/firewall/)을 만듭니다.

내부 IP 주소를 고정 IP 주소로 구성한 후에는 가상 머신 내에서 인터넷에 연결할 수 있는지 확인 합니다.

```
ping 8.8.8.8
```

공용 IP 주소를 사용 하 여 인터넷에서 가상 머신에 연결할 수 있는지 확인 합니다.

가상 컴퓨터의 모든 방화벽 (iptable) 규칙이 포트 80 인바운드를 차단 하지 않는지 확인 합니다.

```
netstat -an | grep 80
```

포트 80에서 수신 대기 하는 http 서버를 시작 합니다.
       
```
python2.7 -m SimpleHTTPServer 80
```

로 구분하거나 여러

```
python3 -m http.server 80
```

바탕 화면에서 브라우저를 시작 하 고 공용 IP 주소에 대 한 포트 80를 가리켜 가상 머신에서 파일을 찾아봅니다. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>공용 IP에 대 한 기본 CloudSimple 방화벽 규칙

* VPN 트래픽: (From/to) VPN과 모든 워크 로드 네트워크 및 관리 네트워크 간의 모든 트래픽이 허용 됩니다.
* 사설 클라우드 내부 트래픽: (From/to) 워크 로드 네트워크와 관리 네트워크 (위에 표시 됨) 사이에 있는 모든 동-서 트래픽이 허용 됩니다.
* 인터넷 트래픽:
    * 인터넷에서 들어오는 모든 트래픽이 작업 네트워크 및 관리 네트워크에 대해 거부 됩니다.
    * 작업 네트워크 또는 관리 네트워크에서 인터넷으로 나가는 모든 트래픽이 허용 됩니다.

방화벽 규칙 기능을 사용 하 여 트래픽을 보호 하는 방법을 수정할 수도 있습니다. 자세한 내용은 [방화벽 테이블 및 규칙 설정](https://docs.azure.cloudsimple.com/firewall/)을 참조 하세요.

## <a name="install-solutions-optional"></a>솔루션 설치 (선택 사항)
사설 클라우드 vCenter 환경을 최대한 활용 하기 위해 CloudSimple 사설 클라우드에 솔루션을 설치할 수 있습니다. 백업, 재해 복구, 복제 및 기타 기능을 설정 하 여 가상 컴퓨터를 보호할 수 있습니다. 예를 들면 vmware SRM (VMware Site Recovery Manager) 및 Veeam 백업 & 복제가 포함 됩니다.

솔루션을 설치 하려면 제한 된 기간 동안 추가 권한을 요청 해야 합니다. [권한 승격](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware 가상 머신 사용](quickstart-create-vmware-virtual-machine.md)
* [Azure Express 경로를 사용 하 여 온-프레미스 네트워크에 연결](https://docs.azure.cloudsimple.com/on-premises-connection)
* [CloudSimple 네트워크에서 VPN 게이트웨이 설정](https://docs.azure.cloudsimple.com/vpn-gateway)
