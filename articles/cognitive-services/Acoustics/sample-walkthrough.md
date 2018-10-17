---
title: '예제: 프로젝트 Acoustics'
titlesuffix: Azure Cognitive Services
description: 이 연습에서는 데스크톱 및 VR로의 배포를 포함하여 프로젝트 Acoustics의 Unity 샘플 장면에 대해 설명합니다.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: sample
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: f5ea565e68579dfad601d1037daeb4113e3daa43
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901161"
---
# <a name="unity-sample-walkthrough"></a>Unity 샘플 연습
이것은 프로젝트 Acoustics 샘플의 연습 과정입니다. 프로젝트 Acoustics가 무엇인지에 대한 자세한 내용은 [프로젝트 Acoustics 소개](what-is-acoustics.md)를 참조하세요. 기존 Unity 프로젝트에 프로젝트 Acoustics 패키지를 추가하는 데 도움이 필요한 경우 [시작 가이드](getting-started.md)를 참조하세요.

## <a name="requirements-for-running-the-sample-project"></a>샘플 프로젝트 실행을 위한 요구 사항
* Unity 2018.2+(.NET 4.x 스크립팅 런타임 버전 사용)
* Windows 64비트 Unity 편집기
* 이 샘플은 Windows 데스크톱, UWP 및 Android 대상[HMS(헤드 마운트 디스플레이) 포함]을 지원합니다.
* 준비 프로세스를 위해 Azure Batch 구독 필요

## <a name="sample-project-setup"></a>샘플 프로젝트 설치
**MicrosoftAcoustics.Sample.unitypackage**를 다운로드하고 가져옵니다. 가져올 때, **Spatializer** 및 **Scripting Runtime Version**(스크립팅 런타임 버전)을 포함하는 프로젝트 설정이 플러그 인 요구 사항에 맞게 업데이트됩니다. 이 작업이 완료되면 **AcousticsGeometry.cs**에서 Unity 콘솔에 Scripting Runtime Version(스크립팅 런타임 버전)이 **.NET 4.x Equivalent**(.NET 4.x 동급)로 변경된 것에 대한 오류가 표시됩니다. 이 설정 변경은 패키지 가져오기의 일부로 수행되지만 Unity를 다시 시작해야 적용됩니다. 지금 Unity를 다시 시작합니다.

## <a name="running-the-sample"></a>샘플 실행
샘플에는 데모 장면 **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**가 포함됩니다. 이 장면에는 세 가지 소리 원본이 있습니다. 기본적으로 하나의 소리 원본만 재생되며 다른 두 원본은 일시 중지됩니다. 소리 원본은 **계층 구조**의 **소리 원본**에 있습니다. 일반 탐색 스크립트를 쉽게 만들도록 하기 위해 Main Camera는 CameraHolder 개체의 자식으로 지정됩니다. 

![계층 구조 뷰](media/SampleHierarchyView.png)

이 장면은 미리 준비되었으며, **계층 구조**에서 **MicrosoftAcoustics** prefab에 연결된 ACE 파일이 있습니다. 

Unity 편집기에서 재생 단추를 클릭하여 장면에 어떤 소리가 나는지 들어봅니다. 바탕 화면에서 W, A, S, D 및 마우스를 사용하여 이동합니다. 장면에 음향이 어떻게 포함 또는 포함되지 않는지 비교하려면 **R** 단추를 눌러 오버레이 텍스트가 빨간색으로 변하면 “음향 : 사용 안 함”이라고 말합니다. 더 많은 컨트롤에 대한 키보드 바로 바기를 보려면 **F1** 키를 누릅니다. 모든 컨트롤은 마우스 오른쪽 단추를 클릭하여 수행할 작업을 선택하고 왼쪽 단추를 클릭하여 작업을 수행하도록 하여 사용할 수 있습니다.

## <a name="targeting-other-platforms"></a>다른 플랫폼을 대상으로 지정
샘플에는 Windows 데스크톱, UWP, Windows Mixed Reality, Android 및 Oculus Go에서 실행하기 위한 설정이 포함됩니다. 기본적으로 이 프로젝트는 Windows 데스크톱용으로 구성되어 있습니다. VR 플랫폼을 대상으로 지정하려면 플레이어 설정(**편집 > 프로젝트 설정 > 플레이어**)으로 이동한 후 **XR 설정**을 찾고 **Virtual Reality Supported**(가상 현실 지원) 확인란을 선택합니다.

![VR 사용](media/VRSupport.png)  

VR 헤드셋을 PC에 연결합니다. **파일 > 빌드 설정**으로 이동한 후 **빌드 및 실행**을 클릭하여 VR 헤드셋에 샘플을 배포합니다. 헤드셋의 동작 컨트롤러를 사용하여 장면을 탐색하거나, 키보드의 W, A, S, D를 사용합니다.    
Android 및 Oculus Go를 대상으로 지정하려면 **빌드 설정** 메뉴에서 Android를 선택합니다. **대상 전환**을 클릭하고 **빌드 및 실행**을 클릭합니다. 이렇게 하면 연결된 Android 장치에 샘플 장면이 배포됩니다. Android에 대한 Unity 개발 관련 내용은 [Unity 설명서](https://docs.unity3d.com/Manual/android-GettingStarted.html)를 참조하세요.

![Android를 대상으로 지정](media/TargetAndroid.png)  

## <a name="next-steps"></a>다음 단계
* 자체 준비를 위해 [Azure 계정 만들기](create-azure-account.md)
* [디자인 프로세스](design-process.md) 알아보기

