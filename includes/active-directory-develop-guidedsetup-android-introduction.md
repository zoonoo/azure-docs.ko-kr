---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 9bc8f30d2bbf6a084ad680306da9b1e330d488e3
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988337"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Android 앱에서 사용자 로그인 및 Microsoft Graph 호출

이 자습서에서는 Android 응용 프로그램을 빌드하고 Microsoft ID 플랫폼에 통합하는 방법을 배웁니다. 특히 이 앱은 사용자로 로그인하고 Microsoft Graph API를 호출하는 액세스 토큰을 가져오며 Microsoft Graph API에 대한 기본 요청을 만듭니다.  

이 가이드를 완료했으면 응용 프로그램에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 또는 학교 계정뿐만 아니라 개인 Microsoft 계정(outlook.com, live.com 등)의 로그인을 수락하게 됩니다.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 샘플의 작동 원리](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

이 샘플의 앱에서는 사용자로 로그인하고 사용자를 대신하여 데이터를 가져옵니다.  이 데이터는 인증이 필요하며 Microsoft ID 플랫폼으로 보호되는 원격 API(이 경우 Microsoft Graph API)를 통해 액세스됩니다.

더 구체적으로 살펴보면 다음과 같습니다.

* 앱은 사용자로 로그인하는 웹 페이지를 시작합니다.
* 앱에서 Microsoft Graph API에 대한 액세스 토큰을 발급합니다.
* 액세스 토큰은 웹 API에 대한 HTTP 요청에 포함됩니다.
* Microsoft Graph 응답을 처리합니다.

이 샘플에서는 MSAL(Android용 Microsoft 인증 라이브러리)를 사용하여 인증을 조정하고 지원합니다. MSAL은 토큰을 자동으로 갱신하고 디바이스의 다른 앱 간에 SSO를 제공하며 계정을 관리하고 대부분의 조건부 액세스 사례를 처리합니다.

## <a name="prerequisites"></a>필수 조건

* 이 단계별 설정에서는 Android Studio 3.0을 사용합니다.
* Android 21 이상이 필요합니다(25 이상 권장).
* Android용 MSAL의 이 버전에는 Google Chrome 또는 [사용자 지정] 탭을 사용하는 웹 브라우저가 필요합니다.

## <a name="library"></a>라이브러리

이 가이드에서는 다음 인증 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|MSAL(Microsoft 인증 라이브러리)|
