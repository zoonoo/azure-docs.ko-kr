---
title: 애플리케이션 동의 작동 원리 | Microsoft Docs
description: Azure AD 동의 프레임워크 작동 원리를 이해하여 Azure AD에서 애플리케이션을 개발할 때 이러한 프레임워크를 사용하는 방법 알아보기
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: da22c7a62d39b348e2114fd208d2aa8dd9711e0d
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099229"
---
# <a name="how-application-consent-works"></a>애플리케이션 동의 작동 원리

이 문서는 Azure AD 동의 프레임워크의 작동 방식을 이해하여 애플리케이션을 보다 효과적으로 개발할 수 있도록 지원하기 위한 것입니다.

## <a name="recommended-documents"></a>권장되는 문서

- [동의를 통해 리소스 소유자가 리소스에 대한 애플리케이션 액세스를 제어하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent) 이해
- [Azure AD 동의 프레임워크가 동의를 구현하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework)에 대한 단계별 개요 확인
- 좀 더 깊이 있는 이해를 위해 [다중 테넌트 애플리케이션이 동의 프레임워크를 사용하여](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) "user" 및 "admin" 동의를 구현하고 좀 더 수준 높은 다중 계층 애플리케이션 패턴을 지원하는 방법 알아보기
- 좀 더 깊이 있는 이해를 위해 [인증 코드 권한 부여 흐름 동안 OAuth 2.0 프로토콜 계층에서 동의가 지원되는 방식](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) 알아보기

## <a name="next-steps"></a>다음 단계
[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
