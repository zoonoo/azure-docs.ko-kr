---
title: Azure는 다중 테 넌 트 응용 프로그램에 대 한 테 넌 트 만들기
description: Azure Active Directory와 통합에 독립 소프트웨어 공급 업체에 대 한 지침
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
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659581"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Azure는 다중 테 넌 트 응용 프로그램에 대 한 테 넌 트 만들기  

다중 테 넌 트 응용 프로그램에 액세스할 수 있도록 응용 프로그램을 등록 하 고 고객 id 페더레이션을 사용 하는 Azure Active Directory 테 넌 트를 만들어야 합니다. 참조 [다중 테 넌 트 응용 프로그램에 대 한 올바른 페더레이션 프로토콜 선택](isv-choose-multi-tenant-federation.md)합니다. 이 테 넌이 트를 사용 하면 고객은 Azure AD 환경의 유사한 환경에서 페더레이션과 응용 프로그램을 테스트할 수 있습니다.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>다중 테 넌 트 응용 프로그램 호스팅 비용

Azure Active Directory는 세 가지 Sku, Free, Basic 및 Premium에서 사용할 수 있습니다. [자세한 기능 비교를 보려면](https://azure.microsoft.com/pricing/details/active-directory/)합니다.

Azure 구독 및 Azure active directory를 무료로 만드는 하 고 기본 기능을 사용할 수 있습니다.

## <a name="create-your-tenant"></a>테 넌 트 만들기

1. 테 넌 트를 만듭니다. 참조 [개발 환경을 설정](../develop/quickstart-create-new-tenant.md)합니다.

2. 사용 하도록 설정 하 고 응용 프로그램에 single sign-on 액세스를 테스트 합니다.

   a. **OIDC 또는 Oath 응용 프로그램에 대 한**하십시오 [응용 프로그램을 등록](../develop/quickstart-register-app.md) 다중 테 넌 트 응용 프로그램으로 합니다. 계정이 모든 조직 디렉터리에 지원 되는 계정 유형에 개인 Microsoft 계정 옵션 선택

   b. **SAML 및 WS-Fed-기반 응용 프로그램에 대 한**에 있습니다 [구성 SAML 기반 single sign on](configure-single-sign-on-non-gallery-applications.md) 일반 SAML 템플릿이 사용 하 여 Azure AD에서 응용 프로그램입니다.

할 수도 있습니다 [다중 테 넌 트를 단일 테 넌 트 응용 프로그램을 변환](../develop/howto-convert-app-to-be-multi-tenant.md) 필요한 경우.

## <a name="next-steps"></a>다음 단계

[응용 프로그램에서 SSO를 통합 합니다.](isv-sso-content.md)
