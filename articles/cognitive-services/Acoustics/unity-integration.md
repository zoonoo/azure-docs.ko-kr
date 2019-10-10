---
title: Project Acoustics Unity 통합 및 배포
titlesuffix: Azure Cognitive Services
description: 이 문서에서는 Project Acoustics Unity 플러그 인을 Unity 프로젝트에 통합 하는 방법을 설명 합니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243008"
---
# <a name="project-acoustics-unity-integration"></a>Project Acoustics Unity 통합
이 문서에서는 Project Acoustics Unity 플러그 인을 Unity 프로젝트에 통합 하는 방법을 설명 합니다.

소프트웨어 요구 사항:
* Windows용 [Unity 2018.2+](https://unity3d.com)
* [Project Acoustics Unity 패키지](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>플러그 인 가져오기
1. Acoustics UnityPackage를 프로젝트로 가져옵니다. 
 Unity에서 **자산** > **가져오기 패키지** > **사용자 지정 패키지**로 이동 합니다.

    ![Unity 패키지 가져오기 메뉴](media/import-package.png)  

1. **ProjectAcoustics. unitypackage**를 선택 합니다.

1. **가져오기** 단추를 선택 하 여 Unity 패키지를 프로젝트에 통합 합니다.

    ![Unity 패키지 가져오기 대화 상자](media/import-dialog.png)  

플러그 인을 기존 프로젝트로 가져오는 경우 프로젝트 루트에 이미 *mcs .rsp* 파일이 있을 수 있습니다. 이 파일은 C# 컴파일러에 대 한 옵션을 지정 합니다. 프로젝트 Acoustics 플러그 인과 함께 제공 되는 mcs .rsp 파일을 사용 하 여 해당 파일의 내용을 병합 합니다.

## <a name="enable-the-plug-in"></a>플러그 인 사용
Acoustics toolkit의 굽기 부분에는 .NET 4가 필요 합니다. *x* 스크립팅 런타임 버전입니다. 패키지 가져오기는 Unity 플레이어 설정을 업데이트 합니다. 이 설정을 적용하려면 Unity를 다시 시작합니다.

![Unity 플레이어 설정 패널](media/player-settings.png)

![.NET 4.5이 선택 된 Unity 플레이어 설정 패널](media/net45.png)

## <a name="set-up-audio-dsp"></a>오디오 DSP 설정
프로젝트 Acoustics Unity 오디오 엔진 spatializer 프레임 워크에 통합 되는 오디오 런타임 DSP를 포함 합니다. HRTF 기반 및 패닝 spatialization 모두 포함 됩니다. Acoustics DSP 프로젝트를 사용 하도록 설정 하려면  > **프로젝트 설정** **편집** > **오디오** 로 이동 하 여 Unity 오디오 설정을 엽니다. 프로젝트의 **Spatializer 플러그 인** **프로젝트 Acoustics** 를 선택 합니다. **DSP 버퍼 크기가** *최상의 성능*으로 설정 되어 있는지 확인 합니다.

![Unity 프로젝트 설정 메뉴](media/project-settings.png)  

![Project Acoustics Spatializer가 선택 된 Unity Spatializer 설정 패널](media/choose-spatializer.png)

다음으로 오디오 믹서 (**창** > **오디오 믹서**)를 엽니다. 그룹이 하나 이상 있는 믹서가 하나 이상 있는지 확인 합니다. 없는 경우 **Mixers**오른쪽에 있는 **+** 단추를 선택 합니다. 효과 섹션에서 채널 스트립 맨 아래를 마우스 오른쪽 단추로 클릭하고 **Microsoft Acoustics Mixer** 효과를 추가합니다. 한 번에 하나의 프로젝트 Acoustics 믹서만 지원 됩니다.

![프로젝트 Acoustics 믹서를 호스트 하는 Unity 오디오 믹서](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>사운드 소스에서 acoustics 사용
오디오 원본 만들기: 오디오 소스 검사기 패널의 아래쪽에 있는 **Spatialize** 확인란을 선택 합니다. **공간 Blend** 가 전체 *3d*로 설정 되었는지 확인 합니다.  

![Unity 오디오 소스 패널](media/audio-source.png)

## <a name="enable-acoustic-design"></a>음향 디자인 사용
*AcousticsAdjust* 스크립트를 장면의 소리 원본에 연결 하 여 추가 원본 디자인 매개 변수를 사용 하도록 설정 합니다. **구성 요소 추가** 를 선택 하 고 **스크립트** > **Acoustics 조정**을 선택 합니다.

![Unity AcousticsAdjust 스크립트](media/acoustics-adjust.png)

## <a name="next-steps"></a>다음 단계
* [Project Acoustics For Unity를 사용 하 여 장면을 굽기](unity-baking.md).
* 클라우드에서 장면을 굽기 [Azure Batch 계정을 만듭니다](create-azure-account.md) .
* [Project Acoustics Unity 디자인 프로세스](unity-workflow.md)를 탐색 합니다.
