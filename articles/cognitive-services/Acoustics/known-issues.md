---
title: Acoustics 플러그 인의 알려진 문제 - Cognitive Services
description: 프로젝트 Acoustics용 디자이너 미리 보기를 사용할 경우 다음과 같은 알려진 문제가 발생할 수 있습니다.
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: c19b19cab33ae868f11ded0b7ce87dac99269596
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431993"
---
# <a name="known-issues"></a>알려진 문제
프로젝트 Acoustics용 디자이너 미리 보기를 사용할 경우 다음과 같은 알려진 문제가 발생할 수 있습니다.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>장면 이름을 바꾸면 음향 매개 변수가 손실됨

장면 이름을 바꾸면 해당 장면에 속하는 모든 음향 매개 변수가 새 장면으로 자동 전송되지 않습니다. 그렇지만 이전 자산 파일에 여전히 존재합니다. 장면 파일 옆에 있는 **Editor** 디렉터리 내에서 **SceneName_AcousticParameters.asset** 파일을 찾습니다. 새 장면 이름을 반영하도록 파일 이름을 바꿉니다.

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>Probes(프로브) 탭의 AcousticsData 폴더에 대한 기본 경로는 절대 경로임

공동 작업자 간의 프로젝트를 보다 쉽게 공유하려면 기본적으로 상대 경로여야 합니다. 해결 방법으로, 경로를 프로젝트 디렉터리 기준의 상대 경로로 변경합니다.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>런타임 복셀이 장면 미리 보기 복셀과 크기가 다름

**Probes**(프로브) 탭에서 **Calculate**(계산)를 수행하고 복셀을 확인한 후, 같은 장면에 대해 런타임에 복셀을 준비하고 확인하면 복셀 크기가 달라집니다. 준비 전에 표시되는 복셀은 시뮬레이션에 사용되는 복셀입니다. 런타임 시 표시되는 복셀은 프로브 점 사이의 보간에 사용됩니다. 이로 인해 실제로 열려 있지 않은 포털이 런타임에 열린 것으로 나타나는 불일치가 발생할 수 있습니다.

## <a name="uwp-builds-not-working"></a>UWP 빌드가 작동하지 않음

최신 버전의 Unity(2018.2 이상)에서 UWP 빌드가 성공하지 못합니다. 빌드의 실행 단계가 중지되고, "Unity 확장이 아직 초기화되지 않았습니다." 오류가 발생합니다. 이 문제는 [이 Unity 문제](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d)로 추적됩니다.

## <a name="unity-crashes-when-closing-project"></a>프로젝트를 닫을 때 Unity가 중단됨

최신 버전의 Unity(2018.2 이상)에는 프로젝트를 닫을 때 Unity가 중단되는 알려진 버그가 있습니다. 이 문제는 [이 Unity 문제](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)로 추적됩니다.

## <a name="trouble-deploying-to-android"></a>Android에 배포할 때 문제 발생
Android에서 프로젝트 Acoustics를 사용하려면 빌드 대상을 Android로 변경합니다. Unity의 일부 버전에는 오디오 플러그 인을 배포와 관련된 버그가 있습니다. [이 버그](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)의 영향을 받는 버전을 사용하고 있지 않은지 확인합니다.

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>'메타데이터 파일 System.Security.dll을 찾을 수 없음' 오류가 발생함

플레이어 설정의 스크립팅 런타임 버전이 **.NET 4.x Equivalent**(.NET 4.x 동급)로 설정되어 있는지 확인하고 Unity를 다시 시작합니다.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Azure에 연결할 때 인증 문제가 있음

Azure 계정에 대해 올바른 자격 증명을 사용했는지, 계정이 준비 시 요청된 노드 유형을 지원하는지, 시스템 클록이 정확한지 한 번 더 확인합니다.

## <a name="next-steps"></a>다음 단계
* [Unity 프로젝트에 음향 통합](getting-started.md) 시작

