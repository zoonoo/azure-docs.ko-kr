---
title: WhoIAM을 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 이 자습서에서는 사용자 확인을 위해 WhoIAM을 사용한 Azure AD B2C 인증 통합 방법을 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d1b2c7513562e951e1098cf327780387ddf6a495
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953545"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 WhoIAM을 구성하기 위한 자습서

이 샘플 자습서에서는 사용자 환경에서 [WHOIAM](https://www.whoiam.ai/brims/) BRIMS(브랜드 ID 관리 시스템)를 구성하고 Azure AD B2C(Active Directory B2C)와 통합하는 방법에 대한 지침을 제공합니다.

BRIMS는 사용자 환경에 배포되는 앱 및 서비스 집합입니다. 사용자 기반의 음성, SMS 및 이메일 확인을 제공합니다. BRIMS는 기존 ID 및 액세스 관리 솔루션과 함께 작동하며, 플랫폼 지식이 없이도 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결된 [Azure AD B2C 테넌트](./tutorial-create-tenant.md)입니다.

- WhoIAM [평가판 계정](https://www.whoiam.ai/contact-us/).

## <a name="scenario-description"></a>시나리오 설명

WhoIAM 통합에는 다음 구성 요소가 포함됩니다.

- Azure AD B2C 테넌트. 이는 정의된 사용자 지정 정책에 따라 사용자의 자격 증명을 확인하는 권한 부여 서버입니다. ID 공급자라고도 합니다.

- 클라이언트 및 해당 구성을 관리하기 위한 관리 포털입니다.

- 엔드포인트를 통해 다양한 기능을 노출하는 API 서비스입니다.  

- Azure Cosmos DB, 이는 BRIMS 관리 포털 및 API 서비스 모두에 대한 백 엔드 역할을 합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![WhoIAM과의 Azure AD B2C 통합 아키텍처 다이어그램.](media/partner-whoiam/whoiam-architecture-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자는 ID 공급자로 Azure AD B2C를 사용하는 앱에 대한 등록 또는 로그인 요청을 시작하기 위해 어떠한 페이지에 도착합니다.
| 2. | 인증의 일환으로 사용자는 이메일 또는 전화의 소유권을 확인하거나 음성을 생체 인식 확인 요소를 사용하도록 요청합니다.  
| 3. | Azure AD B2C는 BRIMS API 서비스를 호출해서, 사용자의 이메일 주소, 전화 번호 및 음성 녹음을 전달합니다.
| 4. | BRIMS는 완전히 사용자 지정할 수 있는 이메일 및 SMS 템플릿과 같은 미리 정의된 구성을 사용하여, 앱 스타일과 일치하는 방식으로 각각의 해당 언어로 사용자와 소통합니다.
| 5. | 사용자의 ID 확인이 완료되면, BRIMS는 Azure AD B2C로 토큰을 반환하여 확인 결과를 나타냅니다. 그런 다음 Azure AD B2C는 해당 앱에 대한 액세스 권한을 사용자에게 부여하거나 인증 시도가 실패하게 합니다.  

## <a name="sign-up-with-whoiam"></a>WhoIAM으로 등록

1. [WhoIAM에](https://www.whoiam.ai/contact-us/) 문의해서 BRIMS 계정을 만듭니다.

2. 제공되는 등록 지침을 사용하여 다음과 같은 Azure 서비스를 구성합니다.

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): 메일 서비스 암호와 같은 암호를 안전 하 게 저장하는 데 사용됩니다.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/): BRIMS API 및 관리 포털 서비스를 호스트하는 데 사용됩니다.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): 관리 포털의 관리 사용자를 인증하는 데 사용됩니다.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): 설정을 저장하고 검색하는 데 사용됩니다.

    - [Application Insights](../azure-monitor/app/app-insights-overview.md)(선택 사항): API 및 관리자 포털 모두에 로그인하는 데 사용됩니다.

3. Azure 환경에서 BRIMS API 및 BRIMS 관리 포털을 배포합니다.

4. Azure AD B2C 사용자 지정 정책 샘플은 BRIMS 등록 설명서에서 사용할 수 있습니다. 설명서에 따라 앱을 구성하고, 사용자 ID 확인 시 BRIMS 플랫폼을 사용합니다.  

WhoIAM의 BRIMS에 대한 자세한 내용은 [제품 설명서](https://www.whoiam.ai/brims/)를 참조 하십시오.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 엽니다. **정책** 에서 **Identity Experience Framework** 를 선택합니다.

2. 이전에 만든 **SignUpSignIn** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 다음을 수행합니다.

   a. **애플리케이션** 의 경우, 등록된 앱을 선택합니다(JWT는 샘플).

   b. **회신 URL** 의 경우, **리디렉션 URL** 을 선택합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 진행하고 계정을 만듭니다.

5. 사용자 특성을 만든 뒤에 흐름 동안 BRIMS 서비스가 호출됩니다. 흐름이 불완전한 경우 사용자가 디렉터리에 저장되지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./custom-policy-get-started.md?tabs=applications)