---
title: "Azure AD v2 iOS 시작 - 소개 | Microsoft Docs"
description: "iOS(Swift) 응용 프로그램이 Azure Active Directory v2 끝점으로 보호되는 액세스 토큰을 필요로 하는 API를 호출하는 방식"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 948693c8501ecc46a1508e5ea085846d0910783e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>iOS 앱에서 Microsoft Graph API 호출

이 가이드에서는 네이티브 iOS(Swift) 응용 프로그램이 액세스 토큰을 얻고 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 Microsoft Graph API를 한 개 이상 호출할 수 있는 방법에 대해 설명합니다.

이 가이드를 모두 살펴보면 응용 프로그램에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 및 학교 계정뿐만 아니라 개인 계정(outlook.com, live.com 등)을 사용해 보호되는 API를 호출할 수 있습니다.

> ### <a name="pre-requisites"></a>필수 조건
> - 이 가이드에는 XCode 8.x가 필요합니다. [여기에서](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 다운로드 URL") XCode를 다운로드할 수 있습니다.
> - 패키지 관리를 위한 [Carthage](https://github.com/Carthage/Carthage)

### <a name="how-this-guide-works"></a>이 가이드의 작동 방식

![이 가이드의 작동 방식](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

이 가이드에서 만든 샘플 응용 프로그램은 iOS 응용 프로그램이 Azure Active Directory v2 끝점에서 토큰을 수락하는 Microsoft Graph API 또는 Web API를 쿼리하도록 합니다. 이 시나리오에서는 토큰은 권한 부여 헤더를 통해 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL(Microsoft 인증 라이브러리)에서 처리합니다.


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>보호되는 Web API에 액세스하기 위한 토큰 획득 처리

사용자 인증 후 샘플 응용 프로그램은 Microsoft Azure Active Directory v2로 보호되는 Microsoft Graph API 또는 Web API를 쿼리하는 데 사용할 수 있는 토큰을 수신합니다.

예를 들어, 사용자의 프로필을 읽거나, 사용자의 일정에 액세스하거나, 메일을 보내기 위해 특정 리소스에 대한 액세스를 허용하려면 Microsoft Graph과 같은 API에는 액세스 토큰이 필요합니다. 응용 프로그램에서는 API 범위를 지정하여 MSAL을 통해 액세스 토큰을 요청할 수 있습니다. 그런 다음 액세스 토큰은 보호되는 리소스에 대한 모든 호출에 사용될 수 있도록 HTTP 인증 헤더에 추가됩니다.

MSAL은 사용자를 대신해 액세스 토큰 캐싱 및 새로 고침을 관리하므로 응용 프로그램에서 이러한 작업을 수행할 필요가 없습니다.


### <a name="nuget-packages"></a>NuGet 패키지

이 가이드에서는 다음 NuGet 패키지를 사용합니다.

|라이브러리|설명|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS용 Microsoft 인증 라이브러리 미리 보기|

