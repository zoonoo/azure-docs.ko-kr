---
title: ARO(Azure Red Hat OpenShift) 클러스터에서 사용자 지정 DNS 리소스 구성
description: ARO(Azure Red Hat OpenShift)의 모든 노드에 사용자 지정 DNS 서버를 추가하는 방법을 알아봅니다.
author: bryanro92
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/02/2021
ms.openlocfilehash: 842e0a4d57254c4ec27bdadf2ff168fe2f4c0424
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111442251"
---
# <a name="configure-custom-dns-for-your-azure-red-hat-openshift-aro-cluster"></a>ARO(Azure Red Hat OpenShift) 클러스터에 대해 사용자 지정 DNS 구성

이 문서에서는 ARO(Azure Red Hat OpenShift) 클러스터에서 사용자 지정 DNS 서버를 사용하도록 구성하는 데 필요한 세부 정보를 제공합니다. 여기에는 기본 ARO 배포를 위한 클러스터 요구 사항이 포함되어 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 새 클러스터를 만드는 중이거나 최신 업데이트가 적용된 기존 클러스터가 있다고 가정합니다. ARO 클러스터가 필요한 경우 퍼블릭 클러스터에 대해서는 [ARO 빠른 시작](./tutorial-create-cluster.md)을 참조하고, 프라이빗 클러스터에 대해서는 [프라이빗 클러스터 자습서](./howto-create-private-cluster-4x.md)를 참조하세요. 사용자 지정 DNS 서버를 사용하도록 클러스터를 구성하는 이러한 단계는 프라이빗 및 퍼블릭 클러스터 모두에 대해 동일합니다.

### <a name="confirm-cluster-compatibility-with-custom-dns"></a>사용자 지정 DNS와의 클러스터 호환성 확인

`99-master-aro-dns` 및 `99-worker-aro-dns` `machineconfigs`가 있는지 확인하여 클러스터가 이 기능을 지원할 수 있는지 알아봅니다.

```
oc get machineconfig
```

위의 명령 결과에 다음 machineconfigs가 포함되어 있으면 클러스터는 사용자 지정 DNS를 지원할 수 있습니다.

```
NAME                 GENERATEDBYCONTROLLER                      IGNITIONVERSION   AGE
...
99-master-aro-dns                                               2.2.0             54d
99-worker-aro-dns                                               2.2.0             54d
...
```

## <a name="dns-architecture-overview"></a>DNS 아키텍처 개요

Azure Red Hat OpenShift 클러스터의 각 노드 전원이 켜져 있고 네트워크에 조인되면 DHCP는 IP 주소 및 사용할 DNS 서버와 같은 정보를 사용하여 가상 머신을 구성합니다.

다음은 구성을 가져오는 방법에 대한 프로세스 흐름 개요입니다.

![DNS](media/concepts-networking/custom-dns-pfd.jpg)

가상 네트워크의 기본 DNS 서버 대신 고유한 DNS 서버를 사용할 때 나타날 수 있는 중요한 문제는 DNS 서버에서 제공하는 구성이 손실된다는 것입니다. 가상 머신 이름은 네트워크의 DNS를 통해 더 이상 확인되지 않습니다.

### <a name="update-process-overview"></a>업그레이드 프로세스 개요

클러스터에 대한 사용자 지정 dns 서버를 구성하는 작업은 두 단계로 나눌 수 있습니다.

1. 가상 네트워크 DNS 서버 구성 설정 수정
2. 클러스터의 노드를 다시 시작하여 변경 내용 적용


## <a name="configure-a-custom-dns-server"></a>사용자 지정 DNS 서버 구성

명령줄을 통해 다음 단계를 수행할 수 있지만 이 설명서에서는 포털 웹 인터페이스를 사용하는 방법을 설명합니다.

### <a name="update-dns-configuration-in-virtual-network"></a>가상 네트워크에서 DNS 구성 업데이트

Azure Portal에 로그인하여 업데이트하려는 가상 네트워크로 이동합니다. 가상 네트워크 설정 목록에서 **DNS 서버** 를 선택합니다.

![DNS 선택](media/concepts-networking/vnet-dns-config.png)

DNS 구성 화면이 표시되면 방사형 단추 구성에서 **사용자 지정** 을 선택합니다. DNS 서버에 대한 IP 주소를 입력합니다.

>[!IMPORTANT]
> 사용자 지정 DNS 서버를 지정하도록 선택하면 더 이상 DNS를 통해 가상 네트워크에서 노드 이름을 확인할 수 없게 됩니다. 노드는 IP 주소를 통해서만 연결할 수 있습니다.

![사용자 지정 DNS 서버 지정](media/concepts-networking/vnet-custom-dns.png)

**저장** 을 선택합니다.

>[!NOTE]
> 포털 인터페이스에 표시된 대로 변경 내용을 적용하려면 모든 가상 머신을 다시 부팅해야 합니다.

업데이트에 성공했다는 알림이 표시됩니다.

![DNS 변경 확인](media/concepts-networking/vnet-dns-confirm-saved.png)

### <a name="gracefully-reboot-your-cluster"></a>클러스터를 정상적으로 다시 부팅

이러한 단계를 수행하려면 클러스터에 대한 유효한 kubeconfig가 있어야 합니다. kubeconfig를 가져오는 방법에 대한 자세한 내용은 [이 자습서](./tutorial-connect-cluster.md)를 참조하세요.

다음 코드 조각은 마스터 및 작업자 노드에 대해 noop `machineconfig`를 만듭니다. 이렇게 하면 작업자 또는 마스터 노드에 대해 롤링 다시 부팅을 시작할 수 있습니다. MCO(Machine Config Operator)에 대한 자세한 내용은 [소스 코드](https://github.com/openshift/machine-config-operator) 또는 [MCO에 대한 OpenShift 문서](https://docs.openshift.com/container-platform/4.6/architecture/control-plane.html)를 참조하세요.
#### <a name="machineconfig-definitions"></a>MachineConfig 정의

다음과 같이 작업자가 다시 시작됩니다.

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 25-machineconfig-worker-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-noop-worker-restart.txt
```

다음과 같이 마스터가 다시 시작됩니다.

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: master
  name: 25-machineconfig-master-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-master-noop-restart.txt
```

#### <a name="reboot-worker-nodes"></a>작업자 노드 다시 부팅

작업자 다시 시작 파일을 만들면 이 예제에서는 파일 `worker-restarts.yml`을 호출한 후 적용합니다.

```
[user@bastion ~]$ vim worker-restarts.yml
[user@bastion ~]$ oc apply -f worker-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-worker-reboot created
```

MCO는 워크로드를 이동하고 각 노드를 한 번에 하나씩 다시 부팅합니다. 작업자가 다시 온라인 상태가 되면 동일한 절차에 따라 마스터 노드를 다시 부팅합니다. 노드를 쿼리하여 작업자의 상태를 확인하고 모두 `Ready` 상태인지 확인할 수 있습니다.

>[!NOTE]
> 클러스터의 워크로드 크기에 따라 각 노드가 다시 부팅되는 데 몇 분 정도 걸릴 수 있습니다.


예제 작업자 노드가 완전히 준비되지는 않았습니다.

```
NAME                                  STATUS                     ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready                      worker   5h35m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready,SchedulingDisabled   worker   5h34m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready                      worker   5h35m   v1.19.0+a5a0987
```

노드가 다시 부팅되면 NotReady 상태로 변경되는 것을 볼 수 있습니다.

```
dns-docs-tm45t-worker-eastus2-ln2kq   NotReady,SchedulingDisabled   worker   5h38m   v1.19.0+a5a0987
```

완전 준비 완료:

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready    master   5h45m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   5h41m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   5h41m   v1.19.0+a5a0987
```

#### <a name="reboot-master-nodes"></a>마스터 노드 다시 부팅

이제 마스터 노드에 대해 동일한 프로세스를 반복합니다.

```
[user@bastion ~]$ vim master-restarts.yml
[user@bastion ~]$ oc apply -f master-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-master-reboot created
```

모든 노드가 `Ready` 상태로 되돌아갔는지 확인합니다.

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE    VERSION
dns-docs-tm45t-master-0               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   6h3m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   6h2m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   6h3m   v1.19.0+a5a0987
```

#### <a name="confirm-changes-on-a-node-optional"></a>노드의 변경 내용 확인(선택 사항)

노드에서 새 DNS 서버의 유효성을 검사하려면 `oc debug` pod를 사용합니다.

```
[user@bastion ~]$ oc debug node/dns-docs-tm45t-worker-eastus2-ln2kq
Starting pod/dns-docs-tm45t-worker-eastus2-ln2kq-debug ...
To use host binaries, run `chroot /host`
chroot Pod IP: 10.0.2.6
If you don't see a command prompt, try pressing enter.
sh-4.4# chroot /host
sh-4.4# uptime
 18:40:16 up 1 min,  0 users,  load average: 0.82, 0.32, 0.12
sh-4.4# cat /etc/resolv.conf.dnsmasq
# Generated by NetworkManager
search reddog.microsoft.com
nameserver 192.168.0.1
```
## <a name="modifying-the-custom-dns-server"></a>사용자 지정 DNS 서버 수정

사용자 지정 DNS가 이미 있는 클러스터에서 사용자 지정 DNS를 수정하는 절차는 동일한 [프로세스](#update-process-overview)를 따릅니다.

### <a name="modify-dns"></a>DNS 수정

[여기](#update-dns-configuration-in-virtual-network)에 설명된 절차에 따라 가상 네트워크의 DNS 구성을 업데이트합니다.

### <a name="reboot-nodes"></a>노드 다시 부팅

`machineconfig`를 만드는 대신 처음에 만든 `machineconfig`를 삭제합니다. 작업자 노드부터 시작합니다.

```
oc delete machineconfig 25-machineconfig-worker-reboot
```

출력은 다음과 같습니다.
```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-worker-reboot" deleted
```

모든 작업자 노드가 다시 부팅될 때까지 기다립니다. 것은 위의 [작업자 노드 다시 부팅](#reboot-worker-nodes)과 유사합니다.

이제 마스터 노드를 다시 부팅합니다.

```
oc delete machineconfig 25-machineconfig-master-reboot
```

출력은 다음과 같습니다.

```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-master-reboot" deleted
```

모든 마스터 노드가 다시 부팅되고 준비 상태로 돌아갈 때까지 기다립니다.