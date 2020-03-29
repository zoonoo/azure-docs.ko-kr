---
title: 프로젝트 음향 통합 및 배포
titlesuffix: Azure Cognitive Services
description: 이 문서에서는 프로젝트 어쿠스틱 유니티 플러그인을 Unity 프로젝트에 통합하는 방법에 대해 설명합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243008"
---
# <a name="project-acoustics-unity-integration"></a>프로젝트 음향 통합
이 문서에서는 프로젝트 어쿠스틱 유니티 플러그인을 Unity 프로젝트에 통합하는 방법에 대해 설명합니다.

소프트웨어 요구 사항:
* Windows용 [Unity 2018.2+](https://unity3d.com)
* [프로젝트 음향 유니티 패키지](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>플러그인 가져오기
1. 어쿠스틱 유니티 패키지를 프로젝트에 가져옵니다. 
 Unity에서 **자산** > **가져오기 패키지** > **사용자 지정 패키지로**이동합니다.

    ![유니티 가져오기 패키지 메뉴](media/import-package.png)  

1. **ProjectAcoustics.unitypackage**를 선택합니다.

1. **가져오기** 단추를 선택하여 Unity 패키지를 프로젝트에 통합합니다.

    ![Unity 패키지 가져오기 대화 상자](media/import-dialog.png)  

플러그인을 기존 프로젝트로 가져오는 경우 프로젝트에 이미 프로젝트 루트에 *mcs.rsp* 파일이 있을 수 있습니다. 이 파일은 C# 컴파일러에 대한 옵션을 지정합니다. 프로젝트 음향 플러그인과 함께 제공되는 mcs.rsp 파일과 해당 파일의 내용을 병합합니다.

## <a name="enable-the-plug-in"></a>플러그인 사용
음향 도구 키트의 베이크 부분에는 .NET 4가 필요합니다. *x* 스크립팅 런타임 버전. 패키지 가져오기는 Unity 플레이어 설정을 업데이트합니다. 이 설정을 적용하려면 Unity를 다시 시작합니다.

![유니티 플레이어 설정 패널](media/player-settings.png)

![.NET 4.5가 선택된 Unity 플레이어 설정 패널](media/net45.png)

## <a name="set-up-audio-dsp"></a>오디오 DSP 설정
프로젝트 어쿠스틱에는 Unity 오디오 엔진 공간화 프레임워크에 통합되는 오디오 런타임 DSP가 포함되어 있습니다. HRTF 기반 및 패닝 공간화가 모두 포함됩니다. 프로젝트 음향 DSP를 사용하려면**프로젝트 설정** > **오디오** **편집으로** > 이동하여 Unity 오디오 설정을 엽니다. 프로젝트에 대한 **공간화 플러그인으로** **프로젝트 음향을** 선택합니다. **DSP 버퍼 크기가** 최상의 *성능으로*설정되어 있는지 확인합니다.

![Unity 프로젝트 설정 메뉴](media/project-settings.png)  

![프로젝트 음향 공간화 기가 선택된 유니티 공간화 설정 패널](media/choose-spatializer.png)

다음으로 오디오 믹서(창**Window** > **오디오 믹서)를 엽니다.** 한 그룹과 함께 적어도 하나의 믹서가 있는지 확인합니다. 미주 시 조합원우의 **+** 오른쪽에 있는 버튼을 선택합니다. **Mixers** 효과 섹션에서 채널 스트립 맨 아래를 마우스 오른쪽 단추로 클릭하고 **Microsoft Acoustics Mixer** 효과를 추가합니다. 한 번에 하나의 프로젝트 음향 믹서만 지원됩니다.

![프로젝트 음향 믹서를 호스팅하는 유니티 오디오 믹서](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>음원에서 음향 지원
오디오 소스 만들기: AudioSource 검사기 패널 하단의 **공간화** 확인란을 선택합니다. **공간 블렌드가** 전체 *3D로*설정되어 있는지 확인합니다.  

![유니티 오디오 소스 패널](media/audio-source.png)

## <a name="enable-acoustic-design"></a>음향 설계 활성화
*AcousticsAdjust 스크립트를* 장면의 음원에 연결하여 추가 소스 디자인 매개변수를 활성화합니다: **구성 요소 추가를** 선택하고 스크립트**음향 조정을** **선택합니다.** > 

![Unity AcousticsAdjust 스크립트](media/acoustics-adjust.png)

## <a name="next-steps"></a>다음 단계
* [유니티를 위한 프로젝트 음향으로 장면을 구워보세요.](unity-baking.md)
* [Azure Batch 계정을 만들어](create-azure-account.md) 클라우드에서 장면을 베이크합니다.
* 프로젝트 [음향 유니티 설계 프로세스를](unity-workflow.md)살펴보십시오.
