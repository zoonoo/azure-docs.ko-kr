---
title: Azure Container Instances-질문과 대답
description: Azure Container Instances 서비스와 관련 된 자주 묻는 질문에 대 한 답변
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 5657ac9f10c42c2201641c9af447b7ad2e6a3507
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079017"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Azure Container Instances에 대 한 질문과 대답

이 문서에서는 Azure Container Instances에 대 한 질문과 대답을 다룹니다.

## <a name="deployment"></a>배포

### <a name="how-large-can-my-container-image-be"></a>얼마나 큰 컨테이너 이미지 수 있나요?

Azure Container Instances에 배포 가능한 컨테이너 이미지에 대 한 최대 크기는 15GB입니다. 더 큰 이미지를 배포할 수 있습니다 지금은 정확한 가용성에 따라 배포를 하지만 보장 되지 않습니다.

컨테이너 이미지의 크기를 일반적으로 컨테이너 이미지를 최대한 작게 유지 하려면 배포 하는 데 걸리는 영향을 줍니다.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>내 컨테이너의 배포 속도 수는 방법

크기를 줄이는 방법 중 하나 이므로 배포 시간의 주 결정자 이미지 크기를 찾습니다. 계층에 필요 하지 않거나 크기를 줄일 이미지에서 계층 (밝은 기본 OS 이미지를 선택)를 제거 합니다. 예를 들어 Linux 컨테이너를 실행 하는 경우 사용해 Alpine 전체 Ubuntu Server를 아닌 기본 이미지입니다. 마찬가지로, Windows 컨테이너에 대 한 Nano Server 기본 이미지를 가능 하면 사용. 

Azure 컨테이너 이미지를 통해 사용할 수 있는 사전 캐시 된 이미지의 목록도 확인 해야 합니다 [캐시 된 이미지 나열](/rest/api/container-instances/listcachedimages) API. 사전 캐시 된 이미지 중 하나에 대 한 이미지 레이어를 외부 전환할 수 있습니다. 

자세히 보기 [자세하게](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) 에서 컨테이너 시작 시간을 단축 합니다.

### <a name="what-windows-base-os-images-are-supported"></a>Windows 기본 OS 이미지는 지원 됩니까?

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 기본 이미지

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> 1709 또는 1803 반기 채널 릴리스를 기반으로 하는 Windows 이미지는 지원 되지 않습니다.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 및 클라이언트 기본 이미지 (미리 보기)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>내 컨테이너에 어떤.NET 또는.NET Core 이미지 계층 사용 해야 합니까? 

요구 사항을 충족 하는 가장 작은 이미지를 사용 합니다. Linux의 경우 사용할 수 있습니다는 *런타임 alpine* .NET Core 이미지를.NET Core 2.1의 릴리스 이후 지원 되어 왔습니다. Windows에서 전체.NET Framework를 사용 하는 경우 다음 해야 Windows Server Core 이미지를 사용 하 여 (런타임 전용 이미지와 같은 *4.7.2-windowsservercore-ltsc2016*). 런타임 전용 이미지 작은 있지만.NET SDK를 필요로 하는 워크 로드를 지원 하지 않습니다.

## <a name="availability-and-quotas"></a>가용성 및 할당량

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>내 컨테이너 또는 컨테이너 그룹에 대해 얼마나 많은 코어와 메모리를 할당 해야 하나요?

실제로 워크 로드에 따라 다릅니다. 작게 시작 하 고 컨테이너 방법을 참조 하는 성능을 테스트 합니다. [CPU 및 메모리 리소스 사용 모니터링](container-instances-monitor.md), 다음 코어 또는 컨테이너에 배포 하는 프로세스의 종류에 따라 메모리를 추가 합니다. 

확인도 해야 합니다 [리소스 가용성](container-instances-region-availability.md#availability---general) CPU 코어 및 컨테이너 그룹당 사용 가능한 메모리의 상한에에서 배포 하는 영역에 대 한 합니다. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI에서 기본 인프라 실행 됩니까?

Azure Container Instances 하므로 원하는 컨테이너를 개발에 집중 하 고 인프라에 대해 신경 쓸 필요 없이 서버 리스 주문형 컨테이너 서비스 수 하려고 했습니다. 궁금한 점이 있으면 수 있는 또는 하려는 성능에 대 한 비교를 위해 ACI 실행 다양 한 Sku의 Azure Vm 집합에 대해 F 및 D 시리즈에서 주로 합니다. 계속 개발 및 서비스 최적화에 따라 나중에 변경이 예정입니다. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>천 ACI 코어를 배포 하려는-내 할당량을 늘려 구할 수 있습니까?
 
예 (경우에 따라)입니다. 참조 된 [할당량 및 제한](container-instances-quotas.md) 문서를 현재 할당량 및 제한 하는 요청에 따라 늘릴 수 있습니다.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>4 개 코어와 16GB의 RAM 사용 하 여 배포할 수 있습니까?

아직 없습니다. 현재 이들은 컨테이너 그룹에 대 한 최대값입니다. 특정 요구 사항이 나 요청을 사용 하 여 Azure 지원에 문의 합니다. 

### <a name="when-will-aci-be-in-a-specific-region"></a>ACI 되는 특정 지역의?

현재 지역 가용성은 게시 [여기](container-instances-region-availability.md#availability---general)에서 최신 정보를 통해 사용할 수는 [목록 기능](/rest/api/container-instances/listcapabilities) API. 특정 지역에 대 한 요구 사항이 있는 경우 Azure 지원에 문의 합니다.

## <a name="features-and-scenarios"></a>기능 및 시나리오

### <a name="how-do-i-scale-a-container-group"></a>컨테이너 그룹 확장은 어떻게 하나요?

현재 크기 조정을 사용할 수 없는 경우 컨테이너 또는 컨테이너 그룹 더 많은 인스턴스를 실행 해야 할 경우 서비스에 컨테이너 그룹 생성에 대 한 더 많은 요청을 만들고 자동화 API를 사용 합니다. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>사용자 지정 VNet에서 실행 중인 인스턴스에 사용할 수 있는 기능은 무엇입니까?

사용자가 선택한 Azure virtual network의 컨테이너 그룹을 배포 하 고 Azure 리소스에서 VNet 내에서 트래픽을 라우팅하는 컨테이너 그룹에 대 한 개인 Ip를 위임할 수 있습니다. 가상 네트워크에 컨테이너 그룹의 배포는 현재 미리 보기 및이 기능의 몇 가지 측면은 일반 공급 (GA) 전에 변경 될 수 있습니다. 참조 [미리 보기 제한 사항](container-instances-vnet.md#preview-limitations) 에 대 한 정보를 업데이트 합니다.

## <a name="pricing"></a>가격

### <a name="when-does-the-meter-start-running"></a>경우는 측정기 실행을 시작?

컨테이너 그룹 할당 시간 (새 배포)에 대 한 첫 번째 컨테이너의 이미지를 끌어오려면 먼저 컨테이너 그룹을 다시 시작 (이미 배포한 경우) 컨테이너 그룹 중지 될 때까지 또는 시간에서 계산 됩니다. 세부 정보를 참조 하세요 [Container Instances 가격](https://azure.microsoft.com/pricing/details/container-instances/)합니다.

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>내 컨테이너 중지 되 면 요금이 부과 되지 않도록?

미터 전체 컨테이너 그룹에 중지 되 면 실행을 중지 합니다. 컨테이너 그룹의 컨테이너를 실행 하기만 실시 리소스 컨테이너를 다시 시작 하려는 경우. 

## <a name="next-steps"></a>다음 단계

* [자세한](container-instances-overview.md) Azure Container Instances에 대 한 합니다.
* [일반적인 문제를 해결](container-instances-troubleshooting.md) Azure Container Instances에서.