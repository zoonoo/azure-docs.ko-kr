---
title: 프로젝트 Acoustics란?
titlesuffix: Azure Cognitive Services
description: 프로젝트 Acoustics Unity 플러그인은 VR 및 기존 화면을 대상으로 하는 프로젝트를 위한 폐색, 반향 및 공간화를 제공합니다.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 6a3c97ead40cfb24604edac0624e38a9b0713fc0
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901027"
---
# <a name="what-is-project-acoustics"></a>프로젝트 Acoustics란?
프로젝트 Acoustics Unity 플러그인은 VR 및 기존 화면을 대상으로 하는 프로젝트를 위한 폐색, 반향 및 공간화를 제공합니다. 물리학 기반 시뮬레이션 위에 디자이너의 의도를 입히는 게임 음향을 디자인하는 방법을 제공합니다.

## <a name="why-use-acoustics-in-virtual-environments"></a>가상 환경에서 음향을 사용하는 이유
인간은 음성-시각적 신호를 사용하여 주변 세계를 이해합니다. 가상 세계에서 공간 오디오를 음향과 결합하면 사용자의 몰입도가 향상됩니다. 여기에 설명된 음향 도구는 가상 세계를 분석하여 현실적인 음향 시뮬레이션을 만들고, 시뮬레이션 후 디자인 프로세스를 지원합니다. 분석에는 세계의 각 표면에 대한 기하학 및 재질이 포함됩니다. 시뮬레이션에는 도착 방향(포털링), 반향 전원, 감소 시간, 폐색 등의 매개 변수와 장애물 효과가 포함됩니다.

## <a name="how-does-this-approach-to-acoustics-work"></a>음향에 대한 이 접근 방법은 어떤 원리로 작동하나요?
이 시스템은 가상 세계의 오프라인 계산을 사용하며, 따라서 분석이 런타임에 수행된 경우 좀 더 복잡한 시뮬레이션이 가능합니다. 오프라인 계산의 결과로 음향 매개 변수의 조회 테이블이 생성됩니다. 디자이너는 런타임에 매개 변수에 적용되는 규칙을 지정합니다. 이러한 규칙을 조정하면 감정의 강도를 높이는 초현실적인 효과 또는 배경 오디오 사운드를 위한 비현실적인 장면을 얻을 수 있습니다.

## <a name="design-process-comparison"></a>디자인 프로세스 비교
프로젝트 Acoustics 플러그 인은 Unity 장면에서 음향에 대한 새 디자인 프로세스를 지원합니다. 이 새 디자인 프로세스를 설명하기 위해, 현재 음향에 가장 많이 사용되는 접근 방법 중 하나와 비교해 보겠습니다.

### <a name="typical-approach-to-acoustics-today"></a>현재 음향에 대한 일반적인 접근 방법
현재는 음향에 대한 일반적인 접근 방법으로, 반향 볼륨 그립니다.

![디자인 뷰](media/reverbZonesAltSPace2.png)

그 후 각 영역의 매개 변수를 조정합니다.

![디자인 뷰](media/TooManyReverbParameters.png)

마지막으로, 장면 전체에서 올바른 폐색 및 장애물 필터링을 얻기 위한 광선 추적 논리와 포털링을 위한 경로 검색 논리를 추가합니다. 이 코드는 런타임 비용을 추가할 수 있습니다. 또한 모서리 주변이 매끄럽지 않다는 문제가 있으며 불규칙한 모양의 장면에서 에지 사례가 있습니다.

### <a name="an-alternative-approach-with-physics-based-design"></a>물리학 기반 디자인을 사용하는 대체 방법
프로젝트 Acoustics의 Unity 플러그 인에서 제공하는 접근 방식을 사용할 경우 고정적인 장면의 도형 및 재질을 입력합니다. 장면이 복셀화되고 프로세스에서 광선 추적을 사용하지 않기 때문에 간소화된 또는 워터타이트 음향 메시를 입력할 필요가 없습니다. 또한 반드시 반향 볼륨을 사용하여 장면을 표시할 필요도 없습니다. 플러그 인은 장면을 클라우드에 업로드하고, 물리학 기반 파형 시뮬레이션을 사용합니다. 그 결과는 프로젝트에 조회 테이블로 통합되며, 미학 또는 게임 플레이 효과에 맞게 수정할 수 있습니다.

![디자인 뷰](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>요구 사항
* 음향 베이크를 위한 Unity 2018.2 이상, 사운드 디자인 및 배포를 위한 Unity 5.2 이상
* Windows 64비트 Unity 편집기
* 음향 베이크를 위한 Azure Batch 구독
* Unity 스크립팅 런타임을 '.NET 4.x에 해당하는 항목'으로 설정해야 합니다.

## <a name="platform-support"></a>플랫폼 지원
* Windows 데스크톱(x86 및 AMD64)
* Windows UWP(x86, AMD64 및 ARM)
* Android(x86 및 ARM64)

## <a name="download"></a>다운로드
Acoustics 플러그 인 평가에 관심이 있는 분은 [여기](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)서 디자이너 미리 보기에 등록하세요.

## <a name="next-steps"></a>다음 단계
* [디자인 프로세스](design-process.md) 자세히 알아보기
* [Unity 프로젝트에 음향 통합](getting-started.md) 시작

