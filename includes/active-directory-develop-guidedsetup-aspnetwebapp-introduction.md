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
ms.openlocfilehash: 46247d42837f8ac181d33216d2b93d28e2533c09
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482406"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>ASP.NET 웹앱에 Microsoft에 로그인 추가

이 가이드에서는 OpenID Connect를 사용하는 브라우저 기반 애플리케이션에서 ASP.NET MVC 솔루션을 사용하여 Microsoft에 로그인을 구현하는 방법을 보여 줍니다.

이 가이드의 끝부분에서 애플리케이션은 outlook.com, live.com 등과 같은 개인 계정의 로그인을 허용할 수 있습니다. 이러한 계정에는 Azure Active Directory와 통합된 모든 회사 또는 조직의 업무 및 학교 계정도 포함됩니다.

> 이 가이드에는 Visual Studio 2019가 필요합니다.  이 프로그램이 아직 설치되어 있지 않나요?  [체험용 Visual Studio 2019 다운로드](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

사용자가 만든 샘플 애플리케이션은 브라우저를 사용하여 사용자가 로그인 단추를 통해 인증하도록 요청하는 ASP.NET 웹 사이트에 액세스하는 시나리오를 기반으로 합니다. 이 시나리오에서는 웹 페이지를 렌더링하는 작업의 대부분이 서버 쪽에서 발생합니다.

## <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|애플리케이션이 인증에 OpenIdConnect를 사용할 수 있게 해주는 미들웨어입니다.|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|애플리케이션이 쿠키를 사용하여 사용자 세션을 유지하도록 하는 미들웨어|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|OWIN 기반 애플리케이션이 ASP.NET 요청 파이프라인을 사용하여 IIS에서 실행되도록 함|
