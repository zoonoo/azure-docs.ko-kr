---
title: Azure Mobile Engagement용 Android SDK 통합
description: Android 앱에서 Azure Mobile Engagement SDK를 통합하는 방법에 대해 설명합니다
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal

---
# Azure Mobile Engagement용 Android SDK 통합
> [!div class="op_single_selector"]
> * [유니버설 Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

이 문서는 Azure Mobile Engagement Android SDK에 사용 가능한 모든 통합 및 구성 옵션에 대해 설명합니다.

## 필수 조건
[!INCLUDE [선행 조건](../../includes/mobile-engagement-android-prereqs.md)]

## 고급 기능
### 보고 기능
이러한 기능을 추가할 수 있습니다.

1. [고급 보고 옵션](mobile-engagement-android-advanced-reporting.md)
2. [위치 보고 옵션](mobile-engagement-android-location-reporting.md)
3. [고급 구성 옵션](mobile-engagement-android-advanced-configuration.md)

### 알림:
[Android 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [GCM과 Mobile Engagement를 통합하는 방법](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [ADM과 Mobile Engagement를 통합하는 방법](mobile-engagement-android-adm-integrate.md)

### 태그 계획 구현:
[Android 앱에서 고급 Mobile Engagement API 태깅을 사용하는 방법](mobile-engagement-android-use-engagement-api.md)

## 릴리스 정보
### 4\.2.3 (08/10/2016)
* 더 이상 WIFI 잠금은 없습니다.
* Init 이전에 getDeviceId를 호출할 경우 교착 상태를 해결합니다.(4.2.0에 유입된 버그)

모든 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-android-release-notes.md)를 참조하세요.

## 업그레이드 절차
이전 버전의 SDK를 응용 프로그램에 이미 통합한 경우에는 [업그레이드 절차](mobile-engagement-android-upgrade-procedure.md)를 참조하세요.

<!---HONumber=AcomDC_0817_2016-->