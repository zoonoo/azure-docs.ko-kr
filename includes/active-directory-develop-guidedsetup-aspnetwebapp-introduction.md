---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 86113246fab399e8364653198e9a6971317e3f2c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121827"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>ASP.NET 웹앱에 Microsoft에 로그인 추가

이 가이드에서는 OpenID Connect를 사용하는 브라우저 기반 애플리케이션에서 ASP.NET MVC 솔루션을 사용하여 Microsoft에 로그인을 구현하는 방법을 보여 줍니다.

이 가이드를 모두 살펴보면 애플리케이션에서 Azure Active Directory와 통합된 회사 또는 조직의 회사 및 학교 계정뿐만 아니라 개인 계정(outlook.com, live.com 등)의 로그인을 수락할 수 있습니다.

> 이 가이드에는 Visual Studio 2017이 필요합니다.  이 프로그램이 아직 설치되어 있지 않나요?  [Visual Studio 2017 무료 다운로드](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

이 가이드에서 만드는 애플리케이션 예제는 사용자가 브라우저를 사용하여 ASP.NET 웹 사이트에 액세스하면 사용자에게 로그인 단추를 통해 인증하도록 요청하는 시나리오를 기반으로 합니다. 이 시나리오에서는 웹 페이지를 렌더링하는 작업의 대부분이 서버 쪽에서 발생합니다.

## <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|애플리케이션이 인증에 OpenIdConnect를 사용할 수 있게 해주는 미들웨어입니다.|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|애플리케이션이 쿠키를 사용하여 사용자 세션을 유지하도록 하는 미들웨어|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|OWIN 기반 애플리케이션이 ASP.NET 요청 파이프라인을 사용하여 IIS에서 실행되도록 함|
