---
title: 프로젝트 음향 플러그인 알려진 문제
titlesuffix: Azure Cognitive Services
description: 프로젝트 음향에서 다음과 같은 알려진 문제가 발생할 수 있습니다.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243035"
---
# <a name="project-acoustics-known-issues"></a>프로젝트 음향 알려진 문제
이 문서에서는 프로젝트 음향을 사용할 때 발생할 수 있는 문제에 대해 설명합니다.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>장면 이름을 바꾸면 음향 매개 변수가 손실됨

장면의 이름을 바꾸면 해당 장면에 속한 모든 음향 매개변수가 자동으로 새 장면으로 전송되지 않습니다. 그러나 이전 자산 파일에는 여전히 존재합니다. 장면 파일 옆에 있는 *편집기* 디렉토리에서 *[SceneName]_AcousticParameters.asset* 파일을 찾습니다. 새 장면 이름을 반영하도록 파일 이름을 바꿉니다.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>일부 Unity 버전에서 안드로이드에 배포 버그

일부 버전의 Unity에는 Android에 오디오 플러그인을 배포하는 방법에 [버그가](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) 있습니다. 이 버그의 영향을 받는 버전을 사용하고 있지 않은지 확인합니다.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"메타데이터 파일을 찾을 수 없습니다 System.Security.dll" 오류

**플레이어** 설정의 **스크립팅 런타임 버전이** *.NET 4.x 등가*버전인지 확인하고 Unity를 다시 시작합니다.

## <a name="authentication-problems-when-connecting-to-azure"></a>Azure에 연결할 때 인증 문제

다음을 확인합니다.
- Azure 계정에 올바른 자격 증명을 사용했습니다.
- 계정은 베이크에서 요청한 노드 유형을 지원합니다.
- 시스템 시계가 올바르게 설정되었습니다.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>취소한 후에도 베이크 탭에 "삭제"가 계속 표시됩니다.
프로젝트 음향은 성공적으로 완료 또는 취소한 후 작업에 대한 모든 Azure 리소스를 정리합니다. 이 프로세스는 최대 5분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [유니티](unity-quickstart.md) 또는 [언리얼](unreal-quickstart.md) 샘플 콘텐츠를 사용해 보십시오.
