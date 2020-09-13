---
title: WhoIAM를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 이 자습서에서는 사용자 확인을 위해 Azure AD B2C 인증을 WhoIAM와 통합 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 718ccbaa57ffe9f4ebaf4e8df448b602ba8cc3fa
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293153"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 WhoIAM 구성에 대 한 자습서

이 샘플 자습서에서는 사용자 환경에서 BRIMS ( [WhoIAM](https://www.whoiam.ai/brims/) 브랜드 Identity Management System)를 구성 하 고 Active Directory B2C (Azure AD B2C)와 통합 하는 방법에 대 한 지침을 제공 합니다.

BRIMS은 사용자 환경에 배포 되는 앱 및 서비스 집합입니다. 사용자 기반에 대 한 음성, SMS 및 전자 메일 확인을 제공 합니다. BRIMS는 기존 id 및 액세스 관리 솔루션과 함께 작동 하며 플랫폼에 관계 없이 작동 합니다.

## <a name="prerequisites"></a>전제 조건

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 입니다.

- WhoIAM [평가판 계정](https://www.whoiam.ai/contact-us/).

## <a name="scenario-description"></a>시나리오 설명

WhoIAM 통합에는 다음 구성 요소가 포함 됩니다.

- Azure AD B2C 테 넌 트입니다. 이 서버에 정의 된 사용자 지정 정책을 기반으로 사용자의 자격 증명을 확인 하는 권한 부여 서버입니다. Id 공급자 라고도 합니다.

- 클라이언트 및 해당 구성을 관리 하기 위한 관리 포털입니다.

- 끝점을 통해 다양 한 기능을 노출 하는 API 서비스입니다.  

- Azure Cosmos DB BRIMS 관리 포털 및 API 서비스의 백 엔드 역할을 합니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![WhoIAM와 통합 Azure AD B2C의 아키텍처 다이어그램](media/partner-whoiam/whoiam-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자는 Azure AD B2C를 id 공급자로 사용 하는 앱에 대 한 등록 또는 로그인 요청을 시작 하기 위해 페이지에 도착 합니다.
| 2. | 인증의 일부로 사용자는 전자 메일 또는 전화의 소유권을 확인 하거나 음성을 생체 인식 확인 요소로 사용 하도록 요청 합니다.  
| 3. | Azure AD B2C BRIMS API 서비스에 대 한 호출을 수행 하 고 사용자의 전자 메일 주소, 전화 번호 및 음성 기록을 전달 합니다.
| 4. | BRIMS는 완전히 사용자 지정할 수 있는 메일 및 SMS 템플릿과 같은 미리 정의 된 구성을 사용 하 여 앱 스타일과 일치 하는 방식으로 해당 언어로 사용자를 조작 합니다.
| 5. | 사용자의 id 확인이 완료 되 면 BRIMS는 인증 결과를 나타내는 Azure AD B2C 토큰을 반환 합니다. 그런 다음 Azure AD B2C는 사용자에 게 앱에 대 한 액세스 권한을 부여 하거나 인증 시도에 실패 합니다.  

## <a name="sign-up-with-whoiam"></a>WhoIAM를 사용 하 여 등록

1. [WhoIAM](https://www.whoiam.ai/contact-us/) 에 문의 하 여 BRIMS 계정을 만듭니다.

2. 제공 되는 등록 지침을 사용 하 여 다음과 같은 Azure 서비스를 구성 합니다.

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): 메일 서비스 암호와 같은 암호를 안전 하 게 저장 하는 데 사용 됩니다.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/): BRIMS API 및 관리 포털 서비스를 호스트 하는 데 사용 됩니다.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): 관리 포털에 대 한 관리 사용자를 인증 하는 데 사용 됩니다.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): 설정을 저장 하 고 검색 하는 데 사용 됩니다.

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications) (선택 사항): API 및 관리 포털에 로그인 하는 데 사용 됩니다.

3. Azure 환경에서 BRIMS API 및 BRIMS 관리 포털을 배포 합니다.

4. Azure AD B2C 사용자 지정 정책 샘플은 BRIMS 등록 설명서에서 사용할 수 있습니다. 설명서에 따라 앱을 구성 하 고 사용자 id 확인을 위해 BRIMS 플랫폼을 사용 합니다.  

WhoIAM의 BRIMS에 대 한 자세한 내용은 [제품 설명서](https://www.whoiam.ai/brims/)를 참조 하십시오.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 엽니다. **정책**에서 **Identity Experience Framework**를 선택합니다.

2. 이전에 만든 **Signupsignin**을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 다음을 수행 합니다.

   a. **응용 프로그램**의 경우 등록 된 앱 (JWT)을 선택 합니다.

   b. **회신 url**에 대해 **리디렉션 url**을 선택 합니다.

   다. **사용자 흐름 실행**을 선택합니다.

4. 등록 흐름을 진행 하 고 계정을 만듭니다.

5. BRIMS 서비스는 사용자 특성을 만든 후 흐름 중에 호출 됩니다. 흐름이 불완전 한 경우 사용자가 디렉터리에 저장 되지 않았는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
