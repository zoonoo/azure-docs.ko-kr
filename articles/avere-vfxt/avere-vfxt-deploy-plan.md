---
title: Avere vFXT 시스템 계획 - Azure
description: Avere vFXT for Azure를 배포하기 전에 수행할 계획에 대해 설명합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: a097110bac7dad630f9a85dd8b20678db0c739cf
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744659"
---
# <a name="plan-your-avere-vfxt-system"></a>Avere vFXT 시스템 계획

이 문서에서는 새 Avere vFXT for Azure 클러스터를 계획하여 요구 사항에 맞게 만든 클러스터가 배치되고 크기가 적절한지 확인하는 방법에 대해 설명합니다. 

Azure Marketplace로 이동하거나 VM을 만들기 전에 클러스터가 Azure의 다른 요소와 상호 작용하는 방법을 고려합니다. 클러스터 리소스가 사설 네트워크 및 서브넷에 배치될 위치를 계획하고 백 엔드 저장소의 위치를 결정합니다. 만든 클러스터 노드가 워크플로를 지원할 수 있을 만큼 강력한지 확인합니다. 

더 알아보려면 계속 읽어 보세요.

## <a name="resource-group-and-network-infrastructure"></a>리소스 그룹 및 네트워크 인프라

Avere vFXT for Azure 배포의 요소 위치를 고려합니다. 아래 다이어그램에서는 Avere vFXT for Azure 구성 요소에 대한 가능한 배치를 보여 줍니다.

![하나의 서브넷에 있는 클러스터 컨트롤러와 클러스터 VM을 보여 주는 다이어그램입니다. 서브넷 경계 주위에는 vnet 경계가 있습니다. vnet 내부에는 스토리지 서비스 엔드포인트를 나타내는 육각형이 있으며, vnet 외부의 Blob Storage에 파선 모양 화살표로 연결됩니다.](media/avere-vfxt-components-option.png)

Avere vFXT 시스템의 네트워크 인프라를 계획하는 경우 다음 지침을 따릅니다.

* 모든 요소는 Avere vFXT 배포를 위해 만든 새 구독을 사용하여 관리해야 합니다. 이점은 다음과 같습니다. 
  * 더욱 간편한 비용 추적 - 리소스, 인프라 및 컴퓨팅 주기의 모든 비용을 구독 하나에서 확인하고 감사할 수 있습니다.
  * 더욱 쉬운 정리 - 프로젝트를 마친 후에는 전체 구독을 제거할 수 있습니다.
  * 편리한 리소스 할당량 분할 - 고성능 컴퓨팅 워크플로에 사용되는 많은 클라이언트를 가동할 때 Avere vFXT 클라이언트와 클러스터를 단일 구독에 격리하여 리소스가 제한될 수 있는 상황에서 기타 중요 워크로드를 보호할 수 있습니다.

* 클라이언트 계산 시스템을 vFXT 클러스터와 가까운 곳에 배치합니다. 백 엔드 저장소는 더 원격적일 수 있습니다.  

* 편의상 vFXT 클러스터와 클러스터 컨트롤러 VM을 동일한 vnet(가상 네트워크) 및 동일한 리소스 그룹에 배치합니다. 또한 동일한 저장소 계정을 사용해야 합니다. 클러스터 컨트롤러는 클러스터를 만들며, 명령줄 클러스터 관리에도 사용할 수 있습니다.  

  > [!NOTE] 
  > 클러스터 만들기 템플릿을 통해 클러스터에 대한 새 리소스 그룹 및 새 스토리지 계정을 만들 수 있습니다. 기존 리소스 그룹을 지정할 수 있지만 비어 있어야 합니다.

* IP 주소가 클라이언트 또는 계산 리소스와 충돌하지 않도록 클러스터는 자체 서브넷에 있어야 합니다. 

## <a name="ip-address-requirements"></a>IP 주소 요구 사항 

클러스터의 서브넷에 클러스터를 지원할 수 있을 만큼 충분한 IP 주소 범위가 있는지 확인합니다. 

Avere vFXT 클러스터에서 사용하는 IP 주소는 다음과 같습니다.

* 하나의 클러스터 관리 IP 주소. 이 주소는 클러스터의 노드 간에 이동할 수 있지만 항상 사용할 수 있으므로 Avere 제어판 구성 도구에 연결할 수 있습니다.
* 각 클러스터 노드:
  * 하나 이상의 클라이언트 측 IP 주소. (모든 클라이언트 측 주소는 클러스터의 *vserver*에서 관리되므로 필요에 따라 노드 간에 이동할 수 있습니다.)
  * 클러스터 통신을 위한 하나의 IP 주소
  * 하나의 인스턴스 IP 주소(VM에 할당됨)

Azure Blob Storage를 사용하는 경우 클러스터 vnet의 IP 주소도 필요할 수 있습니다.  

* Azure Blob Storage 계정에는 5개 이상의 IP 주소가 필요합니다. 클러스터와 동일한 vnet에 Blob Storage를 배치하는 경우 이 요구 사항을 명심하세요.
* 클러스터의 가상 네트워크 외부에 있는 Azure Blob Storage를 사용하는 경우 vnet 내에 스토리지 서비스 엔드포인트를 만들어야 합니다. 이 엔드포인트는 IP 주소를 사용하지 않습니다.

클러스터와 다른 리소스 그룹에 네트워크 리소스와 Blob Storage(사용된 경우)를 배치할 수 있는 옵션이 있습니다.

## <a name="vfxt-node-sizes"></a>vFXT 노드 크기 

클러스터 노드 역할을 하는 VM은 캐시의 요청 처리량 및 저장소 용량을 결정합니다. 메모리, 프로세서 및 로컬 저장소 특성이 서로 다른 두 가지 인스턴스 유형 중에서 선택할 수 있습니다. 

각 vFXT 노드는 동일합니다. 즉 3개 노드 클러스터를 만들면 동일한 유형과 크기의 VM 3개가 만들어집니다. 

| 인스턴스 유형 | vCPU | 메모리  | 로컬 SSD 저장소  | 최대 데이터 디스크 수 | 캐시되지 않은 디스크 처리량 | NIC(개수) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D16s_v3 | 16  | 64GiB  | 128GiB  | 32 | 25,600IOPS <br/> 384MBps | 8,000MBps(8) |
| Standard_E32s_v3 | 32  | 256GiB | 512GiB  | 32 | 51,200IOPS <br/> 768MBps | 16,000MBps(8)  |

노드당 디스크 캐시의 크기를 구성할 수 있으며, 범위는 1,000-8,000GB입니다. 노드당 1TB는 Standard_D16s_v3 노드에 권장되는 캐시 크기이며, 노드당 4TB는 Standard_E32s_v3 노드에 권장됩니다.

이러한 VM에 대한 자세한 내용은 다음 Microsoft Azure 문서를 참조하세요.

* [범용 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [메모리 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>계정 할당량

사용되는 모든 컴퓨팅 또는 클라이언트 시스템뿐만 아니라 Avere vFXT 클러스터도 실행할 수 있는 용량이 구독에 있는지 확인합니다. 자세한 내용은 [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)을 참조하세요.

## <a name="back-end-data-storage"></a>백 엔드 데이터 저장소

Avere vFXT 클러스터가 캐시에 없는 데이터를 저장해야 하는 위치를 결정합니다. 즉, 작업 세트를 장기적으로 새 Blob 컨테이너에 저장할지 아니면 기존 클라우드 또는 하드웨어 스토리지 시스템에 저장할지를 결정합니다. 

백 엔드에 Azure Blob Storage를 사용하려면 vFXT 클러스터를 만드는 중에 새 컨테이너를 만들어야 합니다. 이 옵션은 클러스터가 준비되는 즉시 사용할 수 있도록 새 컨테이너를 만들고 구성합니다. 

자세한 내용은 [Avere vFXT for Azure만들기](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)를 참조하세요.

> [!NOTE]
> 빈 Blob Storage 컨테이너만 Avere vFXT 시스템용 코어 파일러로 사용할 수 있습니다. vFXT는 기존 데이터를 유지하지 않고도 해당 개체 저장소를 관리할 수 있어야 합니다. 
>
> 클라이언트 머신과 Avere vFXT 캐시를 사용하여 데이터를 클러스터의 새 컨테이너에 효율적으로 복사하는 방법을 알아보려면 [vFXT 클러스터로 데이터 이동](avere-vfxt-data-ingest.md)을 참조하세요.

기존의 온-프레미스 저장소 시스템을 사용하려면 vFXT 클러스터를 만든 후에 해당 시스템을 이 vFXT 클러스터에 추가해야 합니다. Avere vFXT 클러스터에 기존 저장소 시스템을 추가하는 방법에 대한 자세한 지침은 [저장소 구성](avere-vfxt-add-storage.md)을 참조하세요.

## <a name="cluster-access"></a>클러스터 액세스 

Avere vFXT for Azure 클러스터는 개인 서브넷에 있으며 공용 IP 주소를 포함하지 않습니다. 따라서 클러스터 관리 및 클라이언트 관리를 위해 개인 서브넷에 액세스할 수 있는 방법이 필요합니다. 

액세스 옵션은 다음과 같습니다.

* 점프 호스트 - 개인 네트워크 내에 있는 별도의 VM에 공용 IP 주소를 할당한 다음 클러스터 노드로의 SSL 터널을 만드는 데 사용합니다. 

  > [!TIP]
  > 클러스터 컨트롤러에서 공용 IP 주소를 설정하는 경우 해당 주소를 점프 호스트로 사용할 수 있습니다. 자세한 내용은 [클러스터 컨트롤러(점프 호스트)](#cluster-controller-as-jump-host)를 참조하세요.

* VPN(가상 사설망) - 사설망으로의 지점 및 사이트 간 VPN 또는 사이트 간 VPN을 구성합니다.

* Azure ExpressRoute - ExpressRoute 파트너를 통해 개인 연결을 구성합니다. 

이러한 옵션에 대한 자세한 내용은 [인터넷 통신 관련 Azure Virtual Network 설명서](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)를 참조하세요.

### <a name="cluster-controller-as-jump-host"></a>클러스터 컨트롤러(점프 호스트)

클러스터 컨트롤러의 공용 IP 주소를 설정하는 경우에는 해당 주소를 점프 호스트로 사용하여 개인 서브넷 외부에서 Avere vFXT 클러스터에 연결할 수 있습니다. 그러나 컨트롤러에는 클러스터 노드 수정 권한이 있으므로 이와 같이 연결하면 보안상 다소 위험해집니다.  

공용 IP 주소 사용 시의 보안을 개선하려면 네트워크 보안 그룹을 사용해 포트 22를 통한 인바운드 액세스만 허용합니다. 또는 액세스 범위를 IP 원본 주소 범위로 고정(클러스터 액세스에 사용하려는 머신의 연결만 허용)하여 시스템을 추가로 보호할 수 있습니다.

클러스터를 만들 때 클러스터 컨트롤러에서 공용 IP 주소를 만들지 여부를 결정할 수 있습니다. 

* 새 가상 네트워크나 서브넷을 만드는 경우 클러스터 컨트롤러에 공용 IP 주소가 할당됩니다.
* 기존 가상 네트워크 및 서브넷을 선택하는 경우에는 클러스터 컨트롤러에 개인 IP 주소만 포함됩니다. 

## <a name="next-step-understand-the-deployment-process"></a>다음 단계: 배포 프로세스 이해

[배포 개요](avere-vfxt-deploy-overview.md)에서는 Avere vFXT for Azure 시스템을 만들고 데이터를 제공하도록 준비하는 데 필요한 모든 단계를 보여 줍니다.  