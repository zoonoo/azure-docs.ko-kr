---
title: Project Acoustics 개요
titlesuffix: Azure Cognitive Services
description: Project Acoustics는 3D 대화형 환경을 위한 음향 엔진으로, 베이킹된 파동 물리학 시뮬레이션과 대화형 디자인 컨트롤을 통합합니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 1fc125322b83a0eb51095fac21dee05c7ffb39c1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313620"
---
# <a name="what-is-project-acoustics"></a>프로젝트 Acoustics란?
Project Acoustics는 3D 대화형 환경을 위한 파동 음향 엔진입니다. 수동으로 영역을 표시하지 않고도 복잡한 장면에서 회절, 포털링(portaling) 및 반향 효과와 같은 파동 효과를 모델링합니다. 게임 엔진과 오디오 미들웨어 통합도 포함됩니다. Project Acoustics의 원칙은 정적 조명과 비슷합니다. 즉, 세부적인 물리학을 오프라인으로 베이킹하여 물리적 기준을 제공하며, 의미 있는 디자인 컨트롤이 포함된 간단한 런타임을 사용하여 미적 목표를 충족합니다.

![음향 복셀을 보여주는 Gears of War 4의 스크린샷](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>대화형 음향에 파동 물리학 사용
광선 기반 음향 방법은 단일 원본-수신기 광선 발사(ray cast)를 사용하여 폐색을 확인하거나 몇 개의 광선으로 로컬 장면 볼륨을 추정하여 반향을 구동할 수 있습니다. 하지만 조약돌이 바위만큼 폐색하므로 이러한 기술은 신뢰할 수 없습니다. 광선은 물체 주변의 소리를 굴절시키는 방식, 즉 회절이라고 하는 현상을 설명하지 않습니다. Project Acoustics의 시뮬레이션은 파동 기반 시뮬레이션을 사용하여 이러한 효과를 캡처합니다. 그 결과는 더 예측 가능하고 신뢰할 수 있습니다.

Project Acoustics의 주요 혁신은 음향 시뮬레이션과 기존의 소리 디자인 개념을 결합하는 것입니다. 시뮬레이션 결과를 폐색, 포털링 및 반향에 대한 기존 오디오 DSP 매개 변수로 변환합니다. 디자이너는 이 변환 프로세스에 대한 컨트롤을 사용합니다. Project Acoustics를 지지하는 핵심 기술에 대한 자세한 내용은 [연구 프로젝트 페이지](https://www.microsoft.com/en-us/research/project/project-triton/)를 참조하세요.

![장면을 통한 웨이브 전파의 수평 2D 슬라이스를 보여주는 애니메이션](media/wave-simulation.gif)

## <a name="setup"></a>설정
[Project Acoustics Unity 통합](unity-integration.md)은 끌어서 놓기이며 Unity 오디오 엔진 플러그 인을 포함합니다. Project Acoustics C# 컨트롤 구성 요소를 각 오디오 개체에 연결하여 Unity 오디오 원본 제어를 강화합니다.

[Project Acoustics Unreal 통합](unreal-integration.md)에는 Unreal용 편집기 및 게임 플러그 인과 Wwise 믹서 플러그 인이 포함되어 있습니다. 사용자 지정 오디오 구성 요소는 라이브 음향 디자인 컨트롤을 통해 Unreal 내에서 친숙한 Wwise 기능을 확장합니다. 또한 디자인 컨트롤은 Wwise의 믹서 플러그 인에서도 표시됩니다.

## <a name="workflow"></a>워크플로
* **사전 베이킹:** 예를 들어 채광(light shaft)을 무시하는 등 음향에 반응하는 기하 도형을 선택하여 베이킹을 설정하는 것으로 시작합니다. 그런 다음, 자동 재료 할당과 탐색 영역 선택을 편집하여 수신기 샘플링을 안내합니다. 반향/포털/방 영역에 대한 수동 표시가 없습니다.
* **베이킹:** 분석 단계는 로컬로 실행하여 위에서 선택한 항목에 따라 복셀화 및 다른 기하학적 분석을 수행합니다. 결과는 장면 설정을 확인할 수 있도록 편집기에서 시각화됩니다. 베이킹이 제출되면 복셀 데이터가 Azure로 전송되고 음향 게임 자산이 반환됩니다.
* **런타임:** 자산이 수준에 로드되면 음향을 해당 수준에서 수신 대기할 준비가 됩니다. 편집기에서 세분화된 원본별 제어를 사용하여 음향을 라이브로 설계합니다. 컨트롤은 수준 스크립팅에서도 구동할 수 있습니다.

## <a name="platforms"></a>플랫폼
현재 Project Acoustics 런타임 플러그 인이 배포될 수 있는 플랫폼은 다음과 같습니다.
*  Windows
* Android
* Xbox One

## <a name="download"></a>다운로드
* [Project Acoustics Unity 플러그 인 및 샘플](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Project Acoustics Unreal & Wwise 플러그 인 및 샘플](https://www.microsoft.com/download/details.aspx?id=58090)
  * Xbox 이진 파일 및 지원이 필요하면 아래 등록 양식을 통해 문의해 주세요.

## <a name="contact-us"></a>문의처
* [Project Acoustics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)
* [Project Acoustics 업데이트를 받기 위한 등록](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>다음 단계
* [Unity 또는 Unreal](unity-quickstart.md)용 [Project Acoustics 빠른 시작](unreal-quickstart.md)을 시도합니다.
* [Project Acoustics의 소리 디자인 원칙](design-process.md)을 살펴봅니다.

