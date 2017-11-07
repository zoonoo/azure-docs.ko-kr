---
title: "Azure AD v2 Android 시작 - 소개 | Microsoft Docs"
description: "Android 앱이 액세스 토큰을 얻고 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 Microsoft Graph API를 한 개 이상 호출할 수 있는 방법"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Android 앱에서 Microsoft Graph API 호출

이 가이드에서는 네이티브 Android 응용 프로그램이 액세스 토큰을 얻고 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 Microsoft Graph API를 한 개 이상 호출할 수 있는 방법에 대해 설명합니다.

이 가이드를 모두 살펴보면 응용 프로그램에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 및 학교 계정뿐만 아니라 개인 계정(outlook.com, live.com 등)을 사용해 보호되는 API를 호출할 수 있습니다.  

### <a name="how-this-sample-works"></a>이 샘플의 작동 원리
![이 샘플의 작동 원리](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

이 가이드에서 만든 샘플은 Azure Active Directory v2 끝점(이 경우에는 Microsoft Graph API)에서 토큰을 수락하는 Web API를 쿼리하는 데 Android 응용 프로그램이 사용되는 시나리오를 기반으로 합니다. 이 시나리오에서는 토큰은 권한 부여 헤더를 통해 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL(Microsoft 인증 라이브러리)에서 처리합니다.

### <a name="pre-requisites"></a>필수 조건
* 이 안내식 설정은 Android Studio를 중심으로 설명하지만 다른 Android 응용 프로그램 개발 환경에도 적용됩니다. 
* Android SDK 21 이상이 필요합니다(SDK 25 권장).
* Android용 MSAL(Microsoft 인증 라이브러리)의 이 릴리스에는 Google Chrome 또는 사용자 지정 탭을 사용하는 웹 브라우저가 필요합니다.

> 참고: Google Chrome은 Visual Studio Emulator for Android에 포함되어 있지 않습니다. 여기 제시된 코드는 Google Chrome이 설치된 API 25가 포함된 에뮬레이터 또는 API 21 이상이 포함된 이미지에서 테스트하는 것이 좋습니다.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>보호된 Web API에 액세스하기 위해 토큰 획득을 처리하는 방법

사용자 인증 후 샘플 응용 프로그램은 Microsoft Azure Active Directory v2로 보호되는 Microsoft Graph API 또는 Web API를 쿼리하는 데 사용할 수 있는 토큰을 수신합니다.

예를 들어, 사용자의 프로필을 읽거나, 사용자의 일정에 액세스하거나, 메일을 보내기 위해 특정 리소스에 대한 액세스를 허용하려면 Microsoft Graph과 같은 API에는 액세스 토큰이 필요합니다. 응용 프로그램에서는 MSAL을 통해 액세스 토큰을 요청하고 API 범위를 지정해 이러한 리소스에 액세스할 수 있습니다. 그런 다음 액세스 토큰은 보호되는 리소스에 대한 모든 호출에 사용될 수 있도록 HTTP 인증 헤더에 추가됩니다. 

MSAL은 사용자를 대신해 액세스 토큰 캐싱 및 새로 고침을 관리하므로 응용 프로그램에서 이러한 작업을 수행할 필요가 없습니다.

### <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|MSAL(Microsoft 인증 라이브러리)|
