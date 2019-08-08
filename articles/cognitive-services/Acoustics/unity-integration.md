---
title: Project Acoustics Unity 통합 및 배포
titlesuffix: Azure Cognitive Services
description: 이 방법에서는 Project Acoustics Unity 플러그 인을 Unity 프로젝트에 통합 하는 방법을 설명 합니다.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 1a90f6102d35dc1a3bb97c840f2955b54f35bbad
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706636"
---
# <a name="project-acoustics-unity-integration"></a>Project Acoustics Unity 통합
이 방법에서는 Project Acoustics Unity 플러그 인을 Unity 프로젝트에 통합 하는 방법을 설명 합니다.

소프트웨어 요구 사항:
* Windows용 [Unity 2018.2+](https://unity3d.com)
* [Project Acoustics Unity 패키지](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>플러그 인 가져오기
프로젝트에 음향 UnityPackage를 가져옵니다. 
* Unity에서 **Assets(자산) > Import Package(패키지 가져오기) > Custom Package...(사용자 지정 패키지...)** 로 이동합니다.

    ![Unity 패키지 가져오기 메뉴의 스크린샷](media/import-package.png)  

* **ProjectAcoustics.unitypackage** 선택

기존 프로젝트에 플러그 인을 가져오려는 경우 프로젝트의 루트에 C# 컴파일러에 대한 옵션을 지정하는 **mcs.rsp** 파일이 이미 있을 것입니다. 프로젝트 Acoustics 플러그 인과 함께 제공되는 mcs.rsp 파일에 해당 파일의 내용을 병합해야 합니다.

## <a name="enable-the-plugin"></a>플러그 인을 사용하도록 설정
음향 효과 도구 키트의 준비 부분에는 .NET 4.x 스크립팅 런타임 버전이 필요합니다. 패키지를 가져오면 Unity 플레이어 설정이 업데이트됩니다. 이 설정을 적용하려면 Unity를 다시 시작합니다.

![Unity 플레이어 설정 패널의 스크린샷](media/player-settings.png)

![.NET 4.5를 선택한 Unity 플레이어 설정 패널의 스크린샷](media/net45.png)

## <a name="set-up-audio-dsp"></a>오디오 DSP 설정
프로젝트 Acoustics Unity 오디오 엔진 spatializer 프레임 워크에 통합 되는 오디오 런타임 DSP를 포함 합니다. HRTF 기반 및 패닝 spatialization 모두 포함 됩니다. **> > 프로젝트 설정 편집**을 사용 하 여 Unity 오디오 설정을 열고 프로젝트에 대 한 **Spatializer 플러그 인** 으로 **project Acoustics** 를 선택 하 여 프로젝트 Acoustics DSP를 사용 하도록 설정 합니다. **DSP 버퍼 크기가** 최상의 성능으로 설정 되어 있는지 확인 합니다.

![Unity 프로젝트 설정 패널의 스크린샷](media/project-settings.png)  

![Project Acoustics Spatializer가 선택 된 Unity Spatializer 설정 패널의 스크린샷](media/choose-spatializer.png)

그런 다음 오디오 믹서 (**창 > 오디오 믹서**)를 엽니다. 1개의 그룹이 있는 1개 이상의 믹서가 있는지 확인합니다. 없는 경우 **Mixers**(믹서) 오른쪽에 있는 '+' 단추를 클릭합니다. 효과 섹션에서 채널 스트립의 아래쪽을 마우스 오른쪽 단추로 클릭 하 고 **Project Acoustics 혼합기** 효과를 추가 합니다. 한 번에 하나의 프로젝트 Acoustics Mixer만 지원됩니다.

![Unity 오디오 믹서 호스팅 프로젝트 Acoustics 믹서의 스크린샷](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>사운드 소스에서 acoustics 사용
오디오 원본을 만듭니다. AudioSource의 검사기 패널의 맨 아래에 있는 **Spatialize**(공간화) 확인란을 클릭합니다. **Spatial Blend**(공간 혼합)가 전체 3D로 설정되어 있는지 확인합니다.  

![Unity 오디오 원본 패널의 스크린샷](media/audio-source.png)

## <a name="enable-acoustic-design"></a>음향 디자인 사용
**구성 요소 추가** 를 클릭 하 고 **스크립트 > Acoustics 조정**을 선택 하 여 추가 원본 디자인 매개 변수를 사용할 수 있도록 장면의 소리 원본에 스크립트 **AcousticsAdjust** 를 연결 합니다.

![Unity AcousticsAdjust 스크립트의 스크린샷](media/acoustics-adjust.png)

## <a name="next-steps"></a>다음 단계
* [Project Acoustics for Unity를 사용 하 여 장면 굽기](unity-baking.md)
* 클라우드에서 장면을 굽기 [Azure Batch 계정 만들기](create-azure-account.md)
* [Project Acoustics Unity 디자인 프로세스](unity-workflow.md)를 탐색 합니다.

