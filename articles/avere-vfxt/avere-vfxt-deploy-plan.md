---
title: Avere vFXT 시스템 계획 - Azure
description: Avere vFXT for Azure를 배포하기 전에 수행할 계획에 대해 설명합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 6acc1ffd197ddba4290ff7c0751b259d98a70927
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754385"
---
# <a name="plan-your-avere-vfxt-system"></a>Avere vFXT 시스템 계획

이 문서에서는 필요에 맞게 배치되고 크기가 조정되는 Azure 클러스터에 대한 새 Avere vFXT를 계획하는 방법을 설명합니다.

Azure 마켓플레이스로 이동하거나 VM을 만들기 전에 다음 세부 정보를 고려하십시오.

* 클러스터가 다른 Azure 리소스와 어떻게 상호 작용합니까?
* 클러스터 요소는 개인 네트워크 및 서브넷에 어디에 위치해야 합니까?
* 어떤 유형의 백 엔드 스토리지를 사용할 것이며 클러스터는 어떻게 액세스할 것인가?
* 워크플로를 지원하기 위해 클러스터 노드가 얼마나 강력해야 합니까?

더 알아보려면 계속 읽어 보세요.

## <a name="learn-the-components-of-the-system"></a>시스템의 구성 요소 알아보기

계획을 시작할 때 Azure 시스템에 대한 Avere vFXT의 구성 요소를 이해하는 것이 유용할 수 있습니다.

* 클러스터 노드 - 클러스터는 클러스터 노드로 구성된 세 개 이상의 VM으로 구성됩니다. 노드가 많을수록 시스템에 더 높은 처리량과 더 큰 캐시가 부여됩니다.

* 캐시 - 캐시 용량은 클러스터 노드 간에 균등하게 분할됩니다. 클러스터를 만들 때 노드별 캐시 크기를 설정합니다. 노드 크기가 추가되어 총 캐시 크기가 됩니다.

* 클러스터 컨트롤러 - 클러스터 컨트롤러는 클러스터 노드와 동일한 서브넷 내에 있는 추가 VM입니다. 클러스터를 만들고 지속적인 관리 작업을 위해 컨트롤러가 필요합니다.

* 백 엔드 저장소 - 캐시하려는 데이터는 하드웨어 저장소 시스템 또는 Azure Blob 컨테이너에 장기적으로 저장됩니다. Azure 클러스터에 대한 Avere vFXT를 만든 후 저장소를 추가하거나 Blob 저장소를 사용하는 경우 클러스터를 만드는 동안 컨테이너를 추가하고 구성할 수 있습니다.

* 클라이언트 - 캐시된 파일을 사용하는 클라이언트 컴퓨터는 저장소 시스템에 직접 액세스하는 대신 가상 파일 경로를 사용하여 클러스터에 연결합니다. [(Avere 마운트 vFXT 클러스터에서](avere-vfxt-mount-clients.md)자세히 보기)

## <a name="subscription-resource-group-and-network-infrastructure"></a>구독, 리소스 그룹 및 네트워크 인프라

Avere vFXT for Azure 배포의 요소 위치를 고려합니다. 아래 다이어그램에서는 Avere vFXT for Azure 구성 요소에 대한 가능한 배치를 보여 줍니다.

![하나의 서브넷에 있는 클러스터 컨트롤러와 클러스터 VM을 보여 주는 다이어그램입니다. 서브넷 경계 주위에는 vnet 경계가 있습니다. vnet 내부에는 스토리지 서비스 엔드포인트를 나타내는 육각형이 있으며, vnet 외부의 Blob Storage에 파선 모양 화살표로 연결됩니다.](media/avere-vfxt-components-option.png)

Avere vFXT 클러스터의 네트워크 인프라를 계획할 때 다음 지침을 따르십시오.

* Azure 배포에 대한 각 Avere vFXT에 대한 새 구독을 만듭니다. 이 구독의 모든 구성 요소를 관리합니다.

  각 배포에 대해 새 구독을 사용할 때의 이점은 다음과 같습니다.
  * 더욱 간편한 비용 추적 - 리소스, 인프라 및 컴퓨팅 주기의 모든 비용을 구독 하나에서 확인하고 감사할 수 있습니다.
  * 더욱 쉬운 정리 - 프로젝트를 마친 후에는 전체 구독을 제거할 수 있습니다.
  * 리소스 할당량의 편리한 분할 - Avere vFXT 클라이언트와 클러스터를 단일 구독으로 격리하여 다른 중요한 워크로드를 가능한 리소스 제한으로부터 보호합니다. 이러한 분리는 고성능 컴퓨팅 워크플로우에 많은 수의 클라이언트를 가져올 때 충돌을 방지합니다.

* 클라이언트 컴퓨팅 시스템을 vFXT 클러스터와 가까운 곳에 배치합니다. 백 엔드 스토리지는 더 원격적일 수 있습니다.  

* vFXT 클러스터와 클러스터 컨트롤러 VM을 함께 찾습니다.

  * 동일한 가상 네트워크에서
  * 동일한 리소스 그룹에서
  * 동일한 저장소 계정 사용
  
  클러스터 만들기 템플릿은 대부분의 경우에 대해 이 구성을 처리합니다.

* 클라이언트 또는 다른 컴퓨팅 리소스와의 IP 주소 충돌을 방지하려면 클러스터가 자체 서브넷에 있어야 합니다.

* 클러스터 만들기 템플릿을 사용하여 리소스 그룹, 가상 네트워크, 서브넷 및 저장소 계정을 포함하여 클러스터에 필요한 대부분의 인프라 리소스를 만듭니다.

  이미 있는 리소스를 사용하려면 이 테이블의 요구 사항을 충족하는지 확인합니다.

  | 리소스 | 기존 사용? | 요구 사항 |
  |----------|-----------|----------|
  | Resource group | 예, 비어 있는 경우 | 비어 있어야 합니다.|
  | 스토리지 계정 | **예,** 클러스터 생성 후 기존 Blob 컨테이너를 연결하는 경우 <br/>  클러스터 를 만드는 동안 새 Blob 컨테이너를 만드는 경우 **아니요** | 기존 Blob 컨테이너는 비어 있어야 합니다. <br/> &nbsp; |
  | 가상 네트워크 | 예 | 새 Azure Blob 컨테이너를 만드는 경우 저장소 서비스 끝점을 포함 해야 합니다. |
  | 서브넷 | 예 | 다른 리소스를 포함할 수 없습니다. |

## <a name="ip-address-requirements"></a>IP 주소 요구 사항

클러스터의 서브넷에 클러스터를 지원할 수 있을 만큼 충분한 IP 주소 범위가 있는지 확인합니다.

Avere vFXT 클러스터에서 사용하는 IP 주소는 다음과 같습니다.

* 하나의 클러스터 관리 IP 주소. 이 주소는 필요에 따라 클러스터의 노드에서 노드로 이동하여 항상 사용할 수 있도록 할 수 있습니다. 이 주소를 사용하여 Avere 제어판 구성 도구에 연결합니다.
* 각 클러스터 노드:
  * 하나 이상의 클라이언트 측 IP 주소. 모든 클라이언트 대면 주소는 필요에 따라 노드 간에 IP 주소를 이동할 수 있는 클러스터의 *vserver에서*관리됩니다.
  * 클러스터 통신을 위한 하나의 IP 주소
  * 하나의 인스턴스 IP 주소(VM에 할당됨)

Azure Blob 저장소를 사용하는 경우 클러스터의 가상 네트워크에서 IP 주소가 필요할 수도 있습니다.  

* Azure Blob Storage 계정에는 5개 이상의 IP 주소가 필요합니다. 클러스터와 동일한 가상 네트워크에서 Blob 저장소를 찾는 경우 이 요구 사항을 염두에 두십시오.
* 클러스터의 가상 네트워크 외부에 있는 Azure Blob 저장소를 사용하는 경우 가상 네트워크 내부에 저장소 서비스 끝점을 만듭니다. 끝점은 IP 주소를 사용하지 않습니다.

클러스터와 다른 리소스 그룹에 네트워크 리소스와 Blob Storage(사용된 경우)를 배치할 수 있는 옵션이 있습니다.

## <a name="vfxt-node-size"></a>vFXT 노드 크기

클러스터 노드 역할을 하는 VM은 캐시의 요청 처리량 및 스토리지 용량을 결정합니다. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

각 vFXT 노드는 동일합니다. 즉 3개 노드 클러스터를 만들면 동일한 유형과 크기의 VM 3개가 만들어집니다.

| 인스턴스 유형 | vCPU | 메모리  | 로컬 SSD 스토리지  | 최대 데이터 디스크 수 | 캐시되지 않은 디스크 처리량 | NIC(개수) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256GiB | 512GiB  | 32 | 51,200IOPS <br/> 768MBps | 16,000MBps(8)  |

노드당 디스크 캐시의 크기를 구성할 수 있으며, 범위는 1,000-8,000GB입니다. 노드당 4TB는 Standard_E32s_v3 노드에 권장되는 캐시 크기입니다.

이러한 VM에 대한 자세한 내용은 Microsoft Azure 설명서: [메모리 최적화 가상 컴퓨터 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>계정 할당량

사용되는 모든 컴퓨팅 또는 클라이언트 시스템뿐만 아니라 Avere vFXT 클러스터도 실행할 수 있는 용량이 구독에 있는지 확인합니다. 자세한 내용은 [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)을 참조하세요.

## <a name="back-end-data-storage"></a>백 엔드 데이터 스토리지

백 엔드 스토리지 시스템은 모두 클러스터의 캐시에 파일을 공급하고 캐시에서 변경된 데이터를 수신합니다. 작업 집합을 새 Blob 컨테이너또는 기존 저장소 시스템(클라우드 또는 하드웨어)에 장기적으로 저장할지 여부를 결정합니다. 이러한 백 엔드 스토리지 시스템을 *코어 파일러라고*합니다.

### <a name="hardware-core-filers"></a>하드웨어 코어 파일러

클러스터를 만든 후 vFXT 클러스터에 하드웨어 저장소 시스템을 추가합니다. 클러스터의 서브넷에서 스토리지 시스템에 도달할 수 있는 한 온-프레미스 시스템을 비롯한 다양한 인기 있는 하드웨어 시스템을 사용할 수 있습니다.

Avere vFXT 클러스터에 기존 스토리지 시스템을 추가하는 방법에 대한 자세한 지침은 [스토리지 구성](avere-vfxt-add-storage.md)을 참조하세요.

### <a name="cloud-core-filers"></a>클라우드 코어 파일러

Azure 시스템의 Avere vFXT는 백 엔드 저장소에 빈 Blob 컨테이너를 사용할 수 있습니다. 클러스터에 추가할 때 컨테이너는 비어 있어야 합니다 - vFXT 시스템은 기존 데이터를 보존할 필요 없이 개체 저장소를 관리할 수 있어야 합니다.

> [!TIP]
> 백 엔드에 Azure Blob 저장소를 사용하려면 vFXT 클러스터를 만드는 과정에서 새 컨테이너를 만듭니다. 클러스터 만들기 템플릿은 클러스터를 사용할 수 있는 즉시 사용할 수 있도록 새 Blob 컨테이너를 만들고 구성할 수 있습니다. 나중에 컨테이너를 추가하는 것이 더 복잡합니다.
>
> 자세한 내용은 [Avere vFXT for Azure만들기](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)를 참조하세요.

빈 Blob 저장소 컨테이너를 핵심 파일러로 추가한 후 클러스터를 통해 데이터를 복사할 수 있습니다. 병렬 다중 스레드 복사 메커니즘을 사용합니다. 클라이언트 머신과 Avere vFXT 캐시를 사용하여 데이터를 클러스터의 새 컨테이너에 효율적으로 복사하는 방법을 알아보려면 [vFXT 클러스터로 데이터 이동](avere-vfxt-data-ingest.md)을 참조하세요.

## <a name="cluster-access"></a>클러스터 액세스

Avere vFXT for Azure 클러스터는 프라이빗 서브넷에 있으며 공용 IP 주소를 포함하지 않습니다. 클러스터 관리 및 클라이언트 연결을 위해 개인 서브넷에 액세스할 수 있는 방법이 있어야 합니다.

액세스 옵션은 다음과 같습니다.

* 호스트 점프 - 공용 IP 주소를 개인 네트워크 내의 별도의 VM에 할당하고 이를 사용하여 클러스터 노드에 TLS 터널을 만듭니다.

  > [!TIP]
  > 클러스터 컨트롤러에서 공용 IP 주소를 설정하는 경우 해당 주소를 점프 호스트로 사용할 수 있습니다. 자세한 내용은 [클러스터 컨트롤러(점프 호스트)](#cluster-controller-as-jump-host)를 참조하세요.

* VPN(가상 사설망) - Azure와 회사 네트워크의 개인 네트워크 간에 지점 간 또는 사이트 간 VPN을 구성합니다.

* Azure ExpressRoute - ExpressRoute 파트너를 통해 프라이빗 연결을 구성합니다.

이러한 옵션에 대한 자세한 내용은 [인터넷 통신 관련 Azure Virtual Network 설명서](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)를 참조하세요.

### <a name="cluster-controller-as-jump-host"></a>클러스터 컨트롤러(점프 호스트)

클러스터 컨트롤러의 공용 IP 주소를 설정하는 경우에는 해당 주소를 점프 호스트로 사용하여 프라이빗 서브넷 외부에서 Avere vFXT 클러스터에 연결할 수 있습니다. 그러나 컨트롤러에는 클러스터 노드 수정 권한이 있으므로 이와 같이 연결하면 보안상 다소 위험해집니다.

공용 IP 주소가 있는 컨트롤러의 보안을 향상시키기 위해 배포 스크립트는 포트 22에만 인바운드 액세스를 제한하는 네트워크 보안 그룹을 자동으로 만듭니다. 액세스 범위를 IP 원본 주소 범위로 고정(클러스터 액세스에 사용하려는 컴퓨터의 연결만 허용)하면 시스템을 추가로 보호할 수 있습니다.

클러스터를 만들 때 클러스터 컨트롤러에서 공용 IP 주소를 만들지 여부를 결정할 수 있습니다.

* 새 가상 **네트워크** 또는 **새 서브넷을**만드는 경우 클러스터 컨트롤러에 **공용** IP 주소가 할당됩니다.
* 기존 가상 네트워크 및 서브넷을 선택하면 클러스터 컨트롤러에는 **개인** IP 주소만 있습니다.

## <a name="vm-access-roles"></a>VM 액세스 역할

Azure는 [RBAC(역할 기반 액세스 제어)를](../role-based-access-control/index.yml) 사용하여 클러스터 VM이 특정 작업을 수행할 수 있도록 권한을 부여합니다. 예를 들어 클러스터 컨트롤러는 클러스터 노드 VM을 만들고 구성하기 위해 권한 부여가 필요합니다. 클러스터 노드는 다른 클러스터 노드에 IP 주소를 할당하거나 다시 할당할 수 있어야 합니다.

Avere vFXT 가상 컴퓨터에는 두 가지 기본 제공 Azure 역할이 사용됩니다.

* 클러스터 컨트롤러는 기본 제공 역할 [Avere 기여자를](../role-based-access-control/built-in-roles.md#avere-contributor)사용합니다.
* 클러스터 노드는 기본 제공 역할 [Avere 연산자.](../role-based-access-control/built-in-roles.md#avere-operator)

Avere vFXT 구성 요소에 대한 액세스 역할을 사용자 지정해야 하는 경우 고유한 역할을 정의한 다음 생성할 때 VM에 할당해야 합니다. Azure 마켓플레이스에서 배포 템플릿을 사용할 수 없습니다. [시스템에 대한 도움말 받기에](avere-vfxt-open-ticket.md)설명된 대로 Azure 포털에서 티켓을 열어 Microsoft 고객 서비스 및 지원을 참조하십시오.

## <a name="next-steps"></a>다음 단계

[배포 개요는](avere-vfxt-deploy-overview.md) Azure 시스템에 대한 Avere vFXT를 만들고 데이터를 제공할 준비를 하는 데 필요한 단계에 대한 큰 그림을 제공합니다.
