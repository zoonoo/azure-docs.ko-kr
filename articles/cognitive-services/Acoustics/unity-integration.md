---
title: 소음 Unity 통합 및 배포 프로젝트
titlesuffix: Azure Cognitive Services
description: 이 방법에서는 Unity 프로젝트에 플러그 인을 프로젝트 소음 Unity의 통합을 설명합니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 8605fe2ea86f875b5c5fd8ea451a287442dcd51b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922499"
---
# <a name="project-acoustics-unity-integration"></a>프로젝트 소음 Unity 통합
이 방법에서는 Unity 프로젝트에 플러그 인을 프로젝트 소음 Unity의 통합을 설명합니다.

소프트웨어 요구 사항:
* Windows용 [Unity 2018.2+](https://unity3d.com)
* [프로젝트 소음 Unity 패키지](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>플러그 인 가져오기
프로젝트에 음향 UnityPackage를 가져옵니다. 
* Unity에서 **Assets(자산) > Import Package(패키지 가져오기) > Custom Package...(사용자 지정 패키지...)** 로 이동합니다.

    ![스크린 샷의 Unity 패키지 가져오기 메뉴](media/import-package.png)  

* **ProjectAcoustics.unitypackage** 선택

기존 프로젝트에 플러그 인을 가져오려는 경우 프로젝트의 루트에 C# 컴파일러에 대한 옵션을 지정하는 **mcs.rsp** 파일이 이미 있을 것입니다. 프로젝트 Acoustics 플러그 인과 함께 제공되는 mcs.rsp 파일에 해당 파일의 내용을 병합해야 합니다.

## <a name="enable-the-plugin"></a>플러그 인을 사용하도록 설정
음향 효과 도구 키트의 준비 부분에는 .NET 4.x 스크립팅 런타임 버전이 필요합니다. 패키지를 가져오면 Unity 플레이어 설정이 업데이트됩니다. 이 설정을 적용하려면 Unity를 다시 시작합니다.

![Unity 플레이어 설정 패널의 스크린샷](media/player-settings.png)

![.NET 4.5를 선택한 Unity 플레이어 설정 패널의 스크린샷](media/net45.png)

## <a name="set-up-audio-dsp"></a>오디오 DSP 설정
프로젝트 소음 오디오 런타임 DSP Unity 오디오 엔진 입체 음향 프레임 워크에 통합 되는 포함 되어 있습니다. HRTF 기반 및 이동 공간화를 포함합니다. 사용 하 여 Unity 오디오 설정을 열어 프로젝트 소음 DSP을 사용 하도록 설정 **편집 > 프로젝트 설정 > 오디오**를 선택한 다음 **프로젝트 소음** 으로 **입체 음향 플러그 인** 프로젝트에 대 한 합니다. 있는지 확인 합니다 **DSP 버퍼 크기** 최상의 성능으로 설정 됩니다.

![Unity 프로젝트 설정 스크린 샷 패널](media/project-settings.png)  

![선택한 프로젝트 소음 입체 음향을 사용 하 여 Unity 스크린 샷 입체 음향 설정 패널](media/choose-spatializer.png)

다음 오디오 Mixer를 엽니다 (**창 > 오디오 Mixer**). 1개의 그룹이 있는 1개 이상의 믹서가 있는지 확인합니다. 없는 경우 **Mixers**(믹서) 오른쪽에 있는 '+' 단추를 클릭합니다. 결과 섹션에서 채널 줄의 맨 아래를 마우스 오른쪽 단추로 클릭 하 고 추가 합니다 **프로젝트 소음 Mixer** 적용 합니다. 한 번에 하나의 프로젝트 Acoustics Mixer만 지원됩니다.

![스크린 샷의 Unity 오디오 Mixer 프로젝트 소음 mixer를 호스팅](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>소음 사운드 원본에서 사용 하도록 설정
오디오 원본을 만듭니다. AudioSource의 검사기 패널의 맨 아래에 있는 **Spatialize**(공간화) 확인란을 클릭합니다. **Spatial Blend**(공간 혼합)가 전체 3D로 설정되어 있는지 확인합니다.  

![Unity 오디오 원본 스크린 샷 패널](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Acoustic 디자인을 사용 하도록 설정
스크립트를 첨부할 **AcousticsAdjust** 사운드 원본를 클릭 하 여 추가 원본 디자인 매개 변수를 사용 하도록 설정 하려면 장면에 **구성 요소 추가** 선택 하 고 **스크립트 > 소음 조정** :

![Unity AcousticsAdjust 스크립트의 스크린샷](media/acoustics-adjust.png)

## <a name="next-steps"></a>다음 단계
* [Unity에 대 한 프로젝트 소음을 사용 하 여 장면에 적용](unity-baking.md)
* [Azure Batch 계정을 만들고](create-azure-account.md) 클라우드에서 장면 통합
* 탐색 합니다 [소음 Unity 프로젝트 디자인 프로세스](unity-workflow.md)합니다.

