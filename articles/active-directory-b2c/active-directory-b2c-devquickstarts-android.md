---
title: "Azure Active Directory B2C: Android 응용 프로그램에서 Web API 호출 | Microsoft Docs"
description: "이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 Node.js Web API를 호출하는 Android &quot;할 일 목록&quot; 앱을 만드는 방법을 보여 줍니다. Android 앱 및 Web API는 Azure Active Directory B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다."
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d
ms.lasthandoff: 02/01/2017


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: Android 응용 프로그램에서 웹 API 호출
> [!WARNING]
> [여기](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi)에서 찾을 수 있는 업데이트된 Android 코드 샘플을 게시했습니다.  이 샘플은 기본 시나리오에서 Azure AD B2C와의 호환성이 테스트된 타사 라이브러리를 사용합니다.  이 라이브러리는 시스템 브라우저보다는 포함된 웹 보기를 활용합니다.  Google은 2017년 4월 20일에, 포함된 웹 보기를 사용한 Google 계정 로그인을 더 이상 지원하지 않을 것임을 [를 발표했습니다](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). 이 기간이 되면 Google 계정을 지원하려는 사용자는 라이브러리를 업데이트해야 합니다.  

>Microsoft는 타사 라이브러리에 대한 수정 사항을 제공하지 않으며 이러한 라이브러리의 검토를 완료하지 않았습니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다. 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)를 참조하세요.
>
>


