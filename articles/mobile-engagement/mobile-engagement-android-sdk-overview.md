---
title: Azure Mobile Engagement용 Android SDK 통합
description: Android 앱에서 Azure Mobile Engagement SDK를 통합하는 방법에 대해 설명합니다
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: d02bcfc7521df9c18edc534e679e597ad2328e6f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Azure Mobile Engagement용 Android SDK 통합
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [유니버설 Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

이 문서는 Azure Mobile Engagement Android SDK에 사용 가능한 모든 통합 및 구성 옵션에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>고급 기능
### <a name="reporting-features"></a>보고 기능
이러한 기능을 추가할 수 있습니다.

1. [고급 보고 옵션](mobile-engagement-android-advanced-reporting.md)
2. [위치 보고 옵션](mobile-engagement-android-location-reporting.md)
3. [고급 구성 옵션](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>알림:
[Android 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [GCM과 Mobile Engagement를 통합하는 방법](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [ADM과 Mobile Engagement를 통합하는 방법](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>태그 계획 구현:
[Android 앱에서 고급 Mobile Engagement API 태깅을 사용하는 방법](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>릴리스 정보

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* `EngagementApplication` 클래스에서도 `EngagementAgentUtils.isInDedicatedEngagementProcess`를 호출할 때 드물게 발생할 수 있는 충돌을 해결합니다.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Android 8 지원(이전 버전의 SDK는 Android 8에서 작동하지 않음)
* 지원 라이브러리에 대한 종속성이 더 이상 없습니다.
* `EngagementFragmentActivity` 클래스를 제거합니다.
* Android 8의 [백그라운드 실행 제한](https://developer.android.com/preview/features/background.html)으로 인해 사용자가 장치를 조작할 때까지 백그라운드의 로그가 지연될 수 있습니다. 장치가 절전 모드인 경우 이로 인해 푸시 캠페인 **배달됨** 및 **시스템 알림 표시됨** 통계가 지연될 수 있습니다(알림은 계속 표시되며, 문제없이 실시간으로 울리고 진동함).
* [백그라운드 위치 제한](https://developer.android.com/preview/features/background-location-limits.html)으로 인해 Android 8에서는 백그라운드의 실시간 위치가 자주 업데이트되지 않습니다.

모든 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-android-release-notes.md)를 참조하세요.

## <a name="upgrade-procedures"></a>업그레이드 절차
이전 버전의 SDK를 응용 프로그램에 이미 통합한 경우에는 [업그레이드 절차](mobile-engagement-android-upgrade-procedure.md)를 참조하세요.

