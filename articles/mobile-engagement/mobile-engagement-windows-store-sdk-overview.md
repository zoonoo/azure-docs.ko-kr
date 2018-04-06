---
title: Azure Mobile Engagement Windows 유니버설 SDK 통합 | Microsoft Docs
description: Azure Mobile Engagement의 Windows 유니버설 SDK 통합
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: 8c513d5b05afaee4c49d0d2e96a0dcda78761421
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Azure Mobile Engagement의 Windows 유니버설 SDK 통합
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서는 Azure Mobile Engagement Windows 유니버설 SDK에 사용 가능한 모든 통합 및 구성 옵션에 관해 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 먼저 [15분 자습서](mobile-engagement-windows-store-dotnet-get-started.md)를 완료해야 합니다.

## <a name="advanced-features"></a>고급 기능
### <a name="reporting-features"></a>보고 기능
이러한 기능을 추가할 수 있습니다.

1. [고급 보고 옵션](mobile-engagement-windows-store-advanced-reporting.md)
2. [고급 구성 옵션](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>공지
[Windows 유니버설 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>태그 계획 구현:
[Windows 유니버설 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>릴리스 정보
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* 안정성 향상

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>업그레이드 절차
이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 전체 [업그레이드 절차](mobile-engagement-windows-store-upgrade-procedure.md)를 참조하세요. 예를 들어 0.10.1에서 0.11.0으로 마이그레이션하는 경우에는 먼저 "0.9.0에서 0.10.1로 마이그레이션" 절차를 수행한 후에 "0.10.1에서 0.11.0으로 마이그레이션" 절차를 수행해야 합니다.

### <a name="from-330-to-340"></a>3.3.0에서 3.4.0으로
#### <a name="test-logs"></a>테스트 로그
이제 SDK에서 생성된 콘솔 로그를 사용/사용 안 함/필터링할 수 있습니다. 사용자 지정하려면 속성 `EngagementAgent.Instance.TestLogEnabled`를 `EngagementTestLogLevel` 열거형에서 사용 가능한 값 중 하나로 업데이트합니다. 예를 들어 다음과 같습니다.

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>리소스
도달률 오버레이가 향상되었습니다. SDK NuGet 패키지 리소스의 일부입니다.

새 버전의 SDK로 업그레이드하는 동안 리소스의 오버레이 폴더에서 기존 파일을 보관할 것인지 보관하지 않을 것인지를 선택할 수 있습니다.

* 이전 오버레이가 작동 중이거나 `WebView` 요소를 수동으로 통합하는 경우 기존 파일을 보관하도록 결정하여 계속 작동하도록 할 수 있습니다.
* 새 오버레이로 업데이트하려는 경우 리소스의 전체 `overlay` 폴더를 SDK 패키지의 새 폴더로 바꿉니다(UWP 앱: 업그레이드 후 새 오버레이 폴더를 %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources에서 가져올 수 있음).

> [!WARNING]
> 새 오버레이를 사용하면 이전 버전에서 설정한 모든 사용자 지정이 덮어 쓰입니다.
> 
> 

### <a name="upgrade-from-older-versions"></a>이전 버전에서 업그레이드
[Upgrade Procedures](mobile-engagement-windows-store-upgrade-procedure.md)

