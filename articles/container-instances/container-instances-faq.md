---
title: 질문과 대답
description: Azure 컨테이너 인스턴스 서비스와 관련하여 자주 묻는 질문에 대한 답변
author: dkkapur
ms.topic: article
ms.date: 01/07/2020
ms.openlocfilehash: 4a3fb4c1818d86f7fe2913790fd9e573c630cbfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247177"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Azure 컨테이너 인스턴스에 대해 자주 묻는 질문

이 문서에서는 Azure 컨테이너 인스턴스에 대해 자주 묻는 질문을 다룹니다.

## <a name="deployment"></a>배포

### <a name="how-large-can-my-container-image-be"></a>컨테이너 이미지는 얼마나 커질 수 있습니까?

Azure 컨테이너 인스턴스에서 배포 가능한 컨테이너 이미지의 최대 크기는 15GB입니다. 배포 하는 순간에 정확한 가용성에 따라 더 큰 이미지를 배포할 수 있습니다., 하지만이 보장 되지 않습니다.

컨테이너 이미지의 크기는 배포하는 데 걸리는 거리에 영향을 미치므로 일반적으로 컨테이너 이미지를 가능한 한 작게 유지하려고 합니다.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>컨테이너 배포 속도를 높이면 어떻게 해야 합니까?

배포 시간의 주요 결정 요인 중 하나는 이미지 크기이므로 크기를 줄이는 방법을 찾습니다. 필요하지 않은 레이어를 제거하거나 이미지의 레이어 크기를 줄입니다(더 밝은 기본 OS 이미지를 선택). 예를 들어, 리눅스 컨테이너를 실행 하는 경우, 전체 우분투 서버 보다는 기본 이미지로 알파인을 사용 하 여 고려. 마찬가지로 Windows 컨테이너의 경우 가능하면 Nano 서버 기본 이미지를 사용합니다. 

또한 Azure 컨테이너 이미지에서 미리 캐시된 이미지 목록을 확인해야 하며, [캐시된 이미지 목록 API를](/rest/api/container-instances/listcachedimages) 통해 사용할 수 있습니다. 미리 캐시된 이미지 중 하나에 대해 이미지 레이어를 전환할 수 있습니다. 

컨테이너 시작 시간 단축에 대한 [자세한 지침을](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) 참조하십시오.

### <a name="what-windows-base-os-images-are-supported"></a>어떤 Windows 기본 OS 이미지가 지원됩니까?

#### <a name="windows-server-2016-base-images"></a>윈도우 서버 2016 기본 이미지

* [나노](https://hub.docker.com/_/microsoft-windows-nanoserver)서버 `10.0.14393.x`: ,`sac2016`
* [윈도우 서버](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2016`코어 :`10.0.14393.x`

> [!NOTE]
> 반기 채널 릴리스 1709 또는 1803을 기반으로 하는 Windows 이미지는 지원되지 않습니다.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows 서버 2019 및 클라이언트 기본 이미지(미리 보기)

* [나노](https://hub.docker.com/_/microsoft-windows-nanoserver)서버 `1809`: ,`10.0.17763.x`
* [윈도우 서버](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019`코어 `1809`: ,`10.0.17763.x`
* [윈도우](https://hub.docker.com/_/microsoft-windows) `1809`: ,`10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>컨테이너에 어떤 .NET 또는 .NET Core 이미지 레이어를 사용해야 합니까? 

요구 사항을 충족시키는 가장 작은 이미지를 사용합니다. Linux의 경우 .NET Core 2.1 릴리스 이후 지원된 *런타임 알파인* .NET 코어 이미지를 사용할 수 있습니다. Windows의 경우 전체 .NET 프레임워크를 사용하는 경우 Windows Server Core 이미지(예: 런타임 전용 이미지(예: *4.7.2-windowsservercore-ltsc2016)를*사용해야 합니다. 런타임 전용 이미지는 작지만 .NET SDK가 필요한 워크로드는 지원하지 않습니다.

## <a name="availability-and-quotas"></a>가용성 및 할당량

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>컨테이너 또는 컨테이너 그룹에 할당해야 하는 코어와 메모리는 몇 개입니까?

이것은 실제로 워크로드에 따라 달라집니다. 소규모로 시작하여 성능을 테스트하여 컨테이너가 어떻게 되는지 확인합니다. [CPU 및 메모리 리소스 사용량을 모니터링한](container-instances-monitor.md)다음 컨테이너에 배포하는 프로세스 종류에 따라 코어 또는 메모리를 추가합니다. 

또한 배포할 지역의 [리소스 가용성을](container-instances-region-availability.md#availability---general) 확인하여 컨테이너 그룹당 사용 가능한 CPU 코어 및 메모리의 상한에 대해 확인합니다. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI는 어떤 기본 인프라를 실행합니까?

Azure 컨테이너 인스턴스는 서버가 없는 주문형 컨테이너 서비스를 목표로 하므로 인프라에 대해 걱정하지 않고 컨테이너 개발에 집중하기를 원합니다. 호기심이 많거나 성능비교를 원하는 사람들을 위해 ACI는 주로 F 및 D 시리즈에서 다양한 SCO의 Azure VM 집합에서 실행됩니다. 우리는 서비스를 지속적으로 개발하고 최적화함에 따라 미래에 이러한 변화가 발생할 것으로 기대합니다. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>ACI에 수천 개의 코어를 배포하고 싶습니다 - 할당량을 늘릴 수 있습니까?
 
예 (때로는). 현재 [할당량및](container-instances-quotas.md) 요청에 의해 증가할 수 있는 제한에 대한 할당량 및 제한 문서를 참조하십시오.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>4개 이상의 코어와 16GB이상의 RAM으로 배포할 수 있습니까?

아직 없습니다. 현재 컨테이너 그룹에 대한 최대값입니다. 특정 요구 사항 또는 요청이 있는 Azure 지원에 문의하십시오. 

### <a name="when-will-aci-be-in-a-specific-region"></a>ACI는 언제 특정 지역에 있습니까?

현재 지역 가용성은 [여기에](container-instances-region-availability.md#availability---general)게시됩니다. 특정 지역에 대한 요구 사항이 있는 경우 Azure 지원에 문의하십시오.

## <a name="features-and-scenarios"></a>기능 및 시나리오

### <a name="how-do-i-scale-a-container-group"></a>컨테이너 그룹을 확장하려면 어떻게 해야 합니까?

현재 컨테이너 또는 컨테이너 그룹에는 크기 조정을 사용할 수 없습니다. 더 많은 인스턴스를 실행해야 하는 경우 API를 사용하여 컨테이너 그룹 생성에 대한 요청을 자동화하고 서비스에 더 많이 만듭니다. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>사용자 지정 VNet에서 실행되는 인스턴스에 사용할 수 있는 기능은 무엇입니까?

원하는 [Azure 가상 네트워크에 컨테이너 그룹을 배포하고](container-instances-vnet.md) 개인 IP를 컨테이너 그룹에 위임하여 Azure 리소스에서 VNet 내의 트래픽을 라우팅할 수 있습니다. 컨테이너 그룹을 가상 네트워크에 배포하는 것은 현재 Azure 지역의 하위 집합의 프로덕션 워크로드에 사용할 수 있습니다.

## <a name="pricing"></a>가격 책정

### <a name="when-does-the-meter-start-running"></a>미터는 언제 부터 실행되나요?

컨테이너 그룹 기간은 첫 번째 컨테이너의 이미지(새 배포의 경우)를 가져오기 시작하거나 컨테이너 그룹이 이미 배포된 경우 컨테이너 그룹이 중지될 때까지 다시 시작된 시점부터 계산됩니다. [컨테이너 인스턴스 가격 에서](https://azure.microsoft.com/pricing/details/container-instances/)세부 정보를 참조하십시오.

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>컨테이너가 중지되면 요금이 청구되지 않습니다.

전체 컨테이너 그룹이 중지되면 미터가 실행중지됩니다. 컨테이너 그룹의 컨테이너가 실행 중인 한 컨테이너를 다시 시작하려는 경우 리소스를 보유합니다. 

## <a name="next-steps"></a>다음 단계

* Azure 컨테이너 인스턴스에 대해 [자세히 알아보세요.](container-instances-overview.md)
* Azure 컨테이너 인스턴스에서 [일반적인 문제 해결](container-instances-troubleshooting.md)