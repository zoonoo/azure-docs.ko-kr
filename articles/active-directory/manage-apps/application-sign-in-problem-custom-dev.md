---
title: 사용자 지정 개발 애플리케이션에 로그인하는 문제 | Microsoft Docs
description: 응용 프로그램에 로그인 할 수 없습니다를 발생 시킬 수 있는 일반적인 오류를 개발한 경우 Azure AD를 사용 하 여
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825227"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>사용자 지정 개발 응용 프로그램에 로그인 하는 문제

앱에 로그인하지 못하게 될 수 있는 여러 가지 오류가 있습니다. 이 문제가 발생하는 가장 큰 이유는 앱이 잘못 구성되었기 때문입니다.

## <a name="errors-related-to--misconfigured-apps"></a>잘못 구성된 앱과 관련된 오류

* 포털의 구성이 앱의 구성과 일치하는지 확인합니다. 특히, 클라이언트/응용 프로그램 ID, 회신 URL, 클라이언트 비밀/키 및 앱 ID URI를 비교합니다.

* 구성한 리소스만을 요청하기 위해 **필요한 리소스** 탭에서 구성된 권한이 있는 코드에서 액세스를 요청하는 리소스를 비교합니다.

* 유사한 오류 또는 문제는 [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure AD 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[동의 및 Azure AD와 앱 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Azure AD v2.0 수렴형 앱에 대한 동의 및 권한 부여](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
