---
title: v2.0 정보 | Azure
description: v2.0 엔드포인트 및 플랫폼에 관해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ecb95f0440751a6cdbf81dbf02c62bed6b5e780b
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286693"
---
# <a name="about-v20"></a>v2.0 정보

v2.0 엔드포인트와 플랫폼은 미리 보기 상태였으며 지속적으로 향상되었습니다. 현재 JavaScript 단일 페이지 애플리케이션(SPA) 시나리오는 기능이 완료되었으므로 MSAL.js를 사용하여 브라우저 기반 응용 프로그램을 빌드하고, 당사에서 GA(General Availability)로 상태를 업데이트할 수 있게 피드백을 제공하도록 사용자를 초대합니다.

> [!NOTE]
> MSAL Android, iOS 및 .NET에는 개발 중인 기능이 있습니다. 해당 기능을 사용하여 애플리케이션을 빌드하고 피드백을 보낼 수 있습니다.

Azure Portal 개발자 환경은 이제 ADAL 또는 MSAL로 빌드된 모든 애플리케이션을 포함하고 사용성을 향상시키기 위해 상당히 업데이트되었습니다.

과거 Azure Active Directory(Azure AD)에서 Microsoft 개인 계정과 회사 계정을 모두 지원하려는 애플리케이션 개발자는 별도의 두 시스템과 통합해야 했습니다. v2.0 엔드포인트와 플랫폼에서는 이 프로세스를 단순화하는 인증 API 버전을 제공합니다. 여기에서는 단일 통합을 사용하여 두 가지 유형의 계정에서 로그인할 수 있습니다. 또한 v2.0 엔드포인트를 사용하는 애플리케이션은 두 가지 계정 유형 중 하나를 사용하여 [Microsoft Graph API](https://developer.microsoft.com/graph)의 REST API를 사용할 수 있습니다.

## <a name="getting-started"></a>시작

다음 목록에서 즐겨 찾는 플랫폼을 선택하여 Microsoft 오픈 소스 라이브러리 및 프레임워크로 애플리케이션을 빌드합니다. OAuth 2.0 및 OpenID Connect 프로토콜을 사용하여 인증 라이브러리를 사용하지 않고도 프로토콜 메시지를 직접 주고받을 수도 있습니다.

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>v2.0 엔드포인트와 플랫폼에 대해 자세히 알아보세요.

Azure AD v2.0 엔드포인트로 수행할 수 있는 작업에 대해 자세히 알아봅니다.

* [Azure AD v2.0 엔드포인트로 빌드할 수 있는 응용 프로그램 유형](v2-app-types.md)을 검색합니다.
* Azure AD v2.0 엔드포인트에 대한 [제한, 제한 사항 및 제약 조건](active-directory-v2-limitations.md)을 이해합니다.
* Azure AD v 2.0 엔드포인트의 개요를 보려면 다음 비디오를 시청하세요.

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>추가 리소스

v2.0에 관한 자세한 정보를 살펴보세요.

* [v2.0 프로토콜 참조](active-directory-v2-protocols.md)
* [액세스 토큰 참조](access-tokens.md)
* [ID 토큰 참조](id-tokens.md)
* [v2.0 인증 라이브러리 참조](reference-v2-libraries.md)
* [v2.0의 권한 및 동의](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)

> [!NOTE]
> Azure Active Directory에서 회사 계정과 학교 계정에만 로그인해야 하는 경우 [개발자용 Azure AD 가이드](v1-overview.md)를 시작합니다. v2.0 엔드포인트는 Microsoft 개인 계정에 명시적으로 로그인해야 하는 개발자가 사용하기 위한 것입니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
