---
title: Project Acoustics 플러그 인 알려진 문제
titlesuffix: Azure Cognitive Services
description: Project Acoustics에서 다음과 같은 알려진 문제가 발생할 수 있습니다.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243035"
---
# <a name="project-acoustics-known-issues"></a>Project Acoustics의 알려진 문제
이 문서에서는 Project Acoustics를 사용 하는 경우 발생할 수 있는 문제에 대해 설명 합니다.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>장면 이름을 바꾸면 음향 매개 변수가 손실됨

장면의 이름을 바꾸면 해당 장면에 속하는 모든 음향 매개 변수가 자동으로 새 장면으로 전송 되지 않습니다. 그러나 이러한 파일은 여전히 이전 자산 파일에 존재 합니다. 장면 파일 옆의 *편집기* 디렉터리에서 *[SceneName] _AcousticParameters* 파일을 찾습니다. 새 장면 이름을 반영 하도록 파일의 이름을 바꿉니다.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>일부 Unity 버전에서 Android로의 버그 배포

일부 버전의 Unity에는 Android에 오디오 플러그 인을 배포 하는 방법에 대 한 [버그가](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) 있습니다. 이 버그의 영향을 받는 버전을 사용 하지 않는지 확인 합니다.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"메타 데이터 파일 시스템을 찾을 수 없습니다." 오류가 발생 했습니다.

**플레이어** 설정의 **Scripting Runtime 버전이** .net 4.x와 *동등한*지 확인 하 고 Unity를 다시 시작 합니다.

## <a name="authentication-problems-when-connecting-to-azure"></a>Azure에 연결할 때의 인증 문제

다음을 확인 합니다.
- Azure 계정에 대 한 올바른 자격 증명을 사용 했습니다.
- 사용자 계정은 굽기에서 요청한 노드 유형을 지원 합니다.
- 시스템 클록이 올바르게 설정 되었습니다.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>취소 한 후에도 굽기 탭에 "삭제"가 표시 됩니다.
Project Acoustics는 성공적으로 완료 되거나 취소 된 후에 작업에 대 한 모든 Azure 리소스를 정리 합니다. 이 프로세스는 최대 5 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Unity](unity-quickstart.md) 또는 [unreal](unreal-quickstart.md) 샘플 콘텐츠를 사용해 보세요.
