---
title: 프로젝트 Acoustics 플러그 인의 알려진 문제
titlesuffix: Azure Cognitive Services
description: 프로젝트 Acoustics용 디자이너 미리 보기를 사용할 경우 다음과 같은 알려진 문제가 발생할 수 있습니다.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 02156f6e39c213764c35e67d8af028489d265835
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616343"
---
# <a name="project-acoustics-known-issues"></a>프로젝트 소음 알려진 문제
프로젝트 Acoustics용 디자이너 미리 보기를 사용할 경우 다음과 같은 알려진 문제가 발생할 수 있습니다.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>장면 이름을 바꾸면 음향 매개 변수가 손실됨

장면 이름을 바꾸면 해당 장면에 속하는 음향 매개 변수를 자동으로 하지 않습니다 모든 새 장면에 전송 합니다. 에서는 여전히 존재 이전 자산 파일에 있지만. 장면 파일 옆에 있는 **Editor** 디렉터리 내에서 **SceneName_AcousticParameters.asset** 파일을 찾습니다. 새 장면 이름을 반영하도록 파일 이름을 바꿉니다.

## <a name="unity-crashes-when-closing-project"></a>프로젝트를 닫을 때 Unity가 중단됨

최신 버전의 Unity(2018.2 이상)에는 프로젝트를 닫을 때 Unity가 중단되는 알려진 버그가 있습니다. 이 문제는 [이 Unity 문제](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)로 추적됩니다.

## <a name="deploying-to-android-from-some-unity-versions"></a>Android에 대 한 몇 가지 Unity 버전에서 배포

Unity의 일부 버전에는 Android에 대 한 오디오 플러그 인을 배포 하는 버그를 있습니다. 영향을 받는 버전을 사용 하지 않는 했는지 [이 버그](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)합니다.

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>'메타데이터 파일 System.Security.dll을 찾을 수 없음' 오류가 발생함

플레이어 설정의 스크립팅 런타임 버전이 **.NET 4.x Equivalent**(.NET 4.x 동급)로 설정되어 있는지 확인하고 Unity를 다시 시작합니다.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Azure에 연결할 때 인증 문제가 있음

Azure 계정에 대해 올바른 자격 증명을 사용했는지, 계정이 준비 시 요청된 노드 유형을 지원하는지, 시스템 클록이 정확한지 한 번 더 확인합니다.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>준비를 취소하면 준비 탭은 "삭제 중" 상태가 됩니다.
프로젝트 소음 성공적으로 완료 또는 취소 작업에 대 한 모든 Azure 리소스를 정리 합니다. 최대 5 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 시도 된 [Unity](unity-quickstart.md) 하거나 [Unreal](unreal-quickstart.md) 콘텐츠 샘플

