---
title: Azure Service Fabric 네트워킹 모범 사례
description: Azure Service Fabric을 사용하여 네트워크 연결을 관리하기 위한 규칙 및 디자인 고려 사항입니다.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: de6a80f73a97a5732f00e10107fff2330038210a
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122019"
---
# <a name="networking"></a>네트워킹

Azure Service Fabric 클러스터를 만들고 관리할 때는 노드와 애플리케이션용 네트워크 연결을 제공합니다. 네트워킹 리소스에는 IP 주소 범위, 가상 네트워크, 부하 분산 장치 및 네트워크 보안 그룹이 포함됩니다. 이 문서에서는 이러한 리소스와 관련된 모범 사례에 대해 알아봅니다.

Azure [Service Fabric 네트워킹 패턴](./service-fabric-patterns-networking.md)을 검토하여 기존 가상 네트워크 또는 서브넷, 고정 공용 IP 주소, 내부 전용 부하 분산 디바이스 또는 내부 및 외부 부하 분산 디바이스와 같은 기능을 사용하는 클러스터를 만드는 방법을 알아보세요.

## <a name="infrastructure-networking"></a>인프라 네트워킹
Resource Manager 템플릿에서 *enableAcceleratedNetworking* 을 선언하면 가속화된 네트워킹을 통해 가상 머신 성능을 최대화할 수 있습니다. 아래에는 가속화된 네트워킹을 사용하도록 설정하는 Virtual Machine Scale Set NetworkInterfaceConfigurations의 코드 조각이 나와 있습니다.

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
[가속화된 네트워킹을 사용하는 Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 및 [가속화된 네트워킹을 사용하는 Windows](../virtual-network/create-vm-accelerated-networking-powershell.md)에서 Service Fabric 클러스터를 프로비전할 수 있습니다.

가속화된 네트워킹은 D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2, Ms/Mms를 지원합니다. Service Fabric Windows 클러스터의 경우 2019년 1월 23일에 Standard_DS8_v3 SKU를 사용하여, Service Fabric Linux 클러스터의 경우 2019년 1월 29일에 Standard_DS12_v2를 사용하여 가속화된 네트워킹을 테스트한 결과 정상 작동이 확인되었습니다. 가속화된 네트워킹에는 4개 이상의 vCPU가 필요합니다. 

기존 Service Fabric 클러스터에서 가속화된 네트워킹을 사용하도록 설정하려면 먼저 [Virtual Machine Scale Set를 추가하여 Service Fabric 클러스터를 확장](./virtual-machine-scale-set-scale-node-type-scale-out.md)해 다음 작업을 수행해야 합니다.
1. 가속화된 네트워킹이 사용하도록 설정된 NodeType 프로비전
2. 가속화된 네트워킹이 사용하도록 설정되어 있는 프로비전된 NodeType으로 서비스와 상태 마이그레이션

가속화된 네트워킹을 현재 위치에서 사용하도록 설정하면 가동 중지 시간이 발생하므로, 기존 클러스터에서 가속화된 네트워킹을 사용하려면 인프라를 확장해야 합니다. [기존 NIC에서 가속화된 네트워킹을 사용하도록 설정하기 전에 가용성 세트의 모든 가상 머신을 중지하고 할당을 취소](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)해야 하기 때문입니다.

## <a name="cluster-networking"></a>클러스터 네트워킹

* [Service Fabric 네트워킹 패턴](./service-fabric-patterns-networking.md)에 요약되어 있는 단계를 수행하면 기존 가상 네트워크에 Service Fabric 클러스터를 배포할 수 있습니다.

* 노드 형식에 대해 클러스터에 대한 인바운드 및 아웃바운드 트래픽을 제한하기 위한 NSG(네트워크 보안 그룹)가 권장됩니다. NSG에서 필요한 포트가 열려 있는지 확인합니다. 

* Service Fabric 시스템 서비스를 포함하는 주 노드 형식은 외부 부하 분산 장치를 통해 표시하지 않아도 되며 [내부 부하 분산 장치](./service-fabric-patterns-networking.md#internal-only-load-balancer)를 통해 표시할 수 있습니다.

* 클러스터에는 [고정 공용 IP 주소](./service-fabric-patterns-networking.md#static-public-ip-address-1)를 사용합니다.

## <a name="network-security-rules"></a>네트워크 보안 규칙

여기서 기본 규칙은 Azure 관리형 Service Fabric 클러스터의 보안 잠금에 대한 최솟값입니다. 다음 포트를 열거나 IP/URL을 승인하는 데 실패하면 클러스터가 적절하게 작동하지 않으며 지원되지 않을 수 있습니다. 해당 규칙 집합을 사용하는 경우에는 [자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 사용해야 합니다. 그러지 않으면 추가 포트를 열어야 합니다.

### <a name="inbound"></a>인바운드 
|우선 순위   |Name               |포트        |프로토콜  |원본             |대상       |작업   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |인터넷           |VirtualNetwork    |허용
|3910       |클라이언트             |19000       |TCP       |인터넷           |VirtualNetwork    |허용
|3920       |클러스터            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |허용
|3930       |Ephemeral          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |허용
|3940       |애플리케이션        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |허용
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |허용
|3960       |RDP                |3389-3488   |TCP       |인터넷           |VirtualNetwork    |거부
|3970       |SSH                |22          |TCP       |인터넷           |VirtualNetwork    |거부
|3980       |사용자 지정 엔드포인트    |80          |TCP       |인터넷           |VirtualNetwork    |허용
|4100       |인바운드 차단      |443         |모두       |모두                |모두               |Allow

인바운드 보안 규칙에 대한 자세한 내용:

* **Azure**. 해당 포트는 Service Fabric Explorer에서 클러스터를 찾아보고 관리하는 데 사용되며 Service Fabric 리소스 공급자가 Azure 관리 포털에 표시하기 위해 클러스터에 대한 정보를 쿼리하는 데도 사용됩니다. Service Fabric 리소스 공급자에서 해당 포트에 액세스할 수 없는 경우 Azure Portal에 '노드를 찾을 수 없음' 또는 'UpgradeServiceNotReachable'과 같은 메시지가 표시되고 노드 및 애플리케이션 목록이 빈 상태로 표시됩니다. 즉, Azure 관리 포털에서 클러스터를 표시하려는 경우 부하 분산 디바이스는 공용 IP 주소를 노출해야 하고 NSG는 들어오는 19080 트래픽을 허용해야 합니다.  

* **클라이언트**. REST/PowerShell/CLI와 같은 API에 대한 클라이언트 연결 엔드포인트입니다. 

* **클러스터**. 노드 간 통신에 사용됩니다. 차단되어서는 안 됩니다.

* **임시**. Service Fabric에서는 이러한 포트 중 일부를 애플리케이션 포트로 사용하고, 나머지 포트는 OS에서 사용할 수 있습니다. 또한 이 범위가 OS에 있는 기존 범위에 매핑되므로 어떤 목적이든 여기에 있는 샘플에 지정된 범위를 사용할 수 있습니다. 시작 포트와 끝 포트 간의 차이가 255 이상인지 확인합니다. 이 범위가 OS와 공유되므로 이 차이가 너무 낮으면 충돌이 발생할 수 있습니다. 구성된 동적 포트 범위를 보려면 *netsh int ipv4에서 동적 포트 tcp 보기* 를 실행합니다. 이러한 포트는 Linux 클러스터에 필요하지 않습니다.

* **애플리케이션**. 애플리케이션 포트 범위는 애플리케이션의 엔드포인트 요구 사항을 충족할 수 있을 만큼 충분히 커야 합니다. 이 범위는 컴퓨터의 동적 포트 범위, 즉 구성에 설정된 ephemeralPorts 범위에서 제외해야 합니다. Service Fabric에서는 새 포트가 필요할 때마다 이러한 포트를 사용하여 노드의 해당 포트에 대한 방화벽을 엽니다.

* **SMB**. 선택 사항으로 런타임 버전 7.1 이상에서는 기본적으로 SMB를 더 이상 사용하지 않습니다. SMB 프로토콜은 ImageStore 서비스에서 두 가지 시나리오에 사용됩니다. 해당 포트는 ImageStore 노드에서 패키지를 다운로드하고 복제본 간에 복제하는 데 필요합니다. 

* **RDP**. 선택 사항으로 인터넷 또는 VirtualNetwork for jumpbox 시나리오에 RDP가 필요한 경우입니다. 

* **SSH**. 선택 사항으로 SSH가 인터넷 또는 VirtualNetwork for jumpbox 시나리오에 필요한 경우입니다.

* **사용자 지정 엔드포인트**. 인터넷 액세스 가능 엔드포인트를 사용하도록 설정하는 애플리케이션의 예입니다.

### <a name="outbound"></a>아웃바운드

|우선 순위   |Name               |포트        |프로토콜  |원본             |대상       |작업   
|---        |---                |---         |---       |---                |---               |---
|3900       |네트워크            |모두         |TCP       |VirtualNetwork     |VirtualNetwork    |허용
|3910       |리소스 공급자  |443         |TCP       |VirtualNetwork     |ServiceFabric     |허용
|3920       |업그레이드            |443         |TCP       |VirtualNetwork     |인터넷          |허용
|3950       |아웃바운드 차단     |모두         |모두       |모두                |모두               |거부

아웃바운드 보안 규칙에 대한 자세한 내용:

* **네트워크**: 서브넷 및 다른 가상 네트워크에 대한 통신 채널입니다.

* **리소스 공급자**. Service Fabric 리소스 공급자가 모든 ARM 배포를 실행하기 위해 UpgradeService를 통해 연결합니다.

* **업그레이드**. download.microsoft.com 주소를 사용하는 업그레이드 서비스는 설치, 이미지로 다시 설치 및 런타임 업그레이드에 필요합니다. 해당 서비스는 동적 IP 주소와 함께 작동합니다. “내부 전용” 부하 분산 디바이스의 시나리오에서는 포트 443에 대한 아웃바운드 트래픽을 허용하는 규칙을 사용하여 추가 외부 부하 분산 디바이스를 템플릿에 추가해야 합니다. 선택 사항으로 해당 포트는 성공적인 설정 후 차단할 수 있지만, 이러한 경우 업그레이드 패키지를 노드에 배포하거나 짧은 기간 동안 포트를 열어야 하며 나중에 수동으로 업그레이드해야 합니다.

[ 흐름 로그](../network-watcher/network-watcher-nsg-flow-logging-overview.md) 및 [트래픽 분석](../network-watcher/traffic-analytics.md)과 함께 Azure Firewall을 사용하여 보안 잠금 문제를 추적합니다. [NSG를 사용한 Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) ARM 템플릿은 시작하기 좋은 예제입니다. 


## <a name="application-networking"></a>애플리케이션 네트워킹

* Windows 컨테이너 워크로드를 실행하려는 경우 [오픈 네트워킹 모드](./service-fabric-networking-modes.md#set-up-open-networking-mode)를 사용하면 서비스 간 통신을 더 쉽게 수행할 수 있습니다.

* [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) 또는 [Service Fabric 역방향 프록시](./service-fabric-reverseproxy.md) 등의 역방향 프록시를 사용하여 80, 443 등의 일반적인 애플리케이션 포트를 표시합니다.

* Azure 클라우드 스토리지에서 기본 계층을 끌어올 수 없는 에어 갭이 있는 컴퓨터에서 호스트되는 Windows 컨테이너의 경우 Docker 디먼의 [--allow-nondistributable-artifacts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) 플래그를 사용하여 외부 계층 동작을 재정의합니다.

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
