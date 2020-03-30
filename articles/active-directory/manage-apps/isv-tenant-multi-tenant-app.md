---
title: 다중 테넌트 응용 프로그램에 대한 Azure 테넌트 만들기
description: Azure Active Directory와의 통합에 대한 독립 소프트웨어 공급업체에 대한 지침
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812621"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>다중 테넌트 응용 프로그램에 대한 Azure 테넌트 만들기  

다중 테넌트 응용 프로그램에 대한 액세스를 제공하려면 응용 프로그램을 등록하고 고객의 ID 페더레이션을 사용하도록 설정하려면 Azure Active Directory 테넌트를 만들어야 합니다. [다중 테넌트 응용 프로그램에 적합한 페더레이션 프로토콜 선택을](isv-choose-multi-tenant-federation.md)참조하십시오. 이 테넌트를 사용하면 고객 Azure AD 환경과 유사한 환경에서 응용 프로그램 및 페더레이션을 테스트할 수 있습니다.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>다중 테넌트 응용 프로그램 호스팅 비용

Azure Active Directory는 여러 버전에서 사용할 수 있습니다. [자세한 기능 비교를 참조하십시오.](https://azure.microsoft.com/pricing/details/active-directory/)

Azure 구독 및 Azure 활성 디렉터리를 무료로 만들고 기본 기능을 사용할 수 있습니다.

## <a name="create-your-tenant"></a>테넌트 만들기

1. 테넌트를 만듭니다. [개발 환경 설정을](../develop/quickstart-create-new-tenant.md)참조하십시오.

2. 응용 프로그램에 대한 단일 사인온 액세스 활성화 및 테스트,

   a. **OIDC 또는 선서 응용 프로그램의 경우**응용 프로그램을 다중 테넌트 응용 프로그램으로 [등록합니다.](../develop/quickstart-register-app.md) 지원되는 계정 유형에서 조직 디렉터리 및 개인 Microsoft 계정 옵션에서 거래처 선택

   b. **SAML 및 WS Fed 기반 응용 프로그램의 경우**Azure AD의 일반 SAML 템플릿을 사용하여 [SAML 기반 단일 사인온](configure-single-sign-on-non-gallery-applications.md) 응용 프로그램을 구성합니다.

필요한 경우 [단일 테넌트 응용 프로그램을 다중 테넌트로 변환할](../develop/howto-convert-app-to-be-multi-tenant.md) 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[애플리케이션에 SSO 통합](isv-sso-content.md)
