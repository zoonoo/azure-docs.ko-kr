---
title: 상업적 marketplace에서 불가능 SaaS 제품에 대 한 방문 페이지 빌드
description: 불가능 SaaS 제품에 대 한 방문 페이지를 빌드하는 방법에 대해 알아봅니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 1ff366e24adb82a0d7d4660d4afaffa0bbca0b3c
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328477"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>상업적 marketplace에서 불가능 SaaS 제품에 대 한 방문 페이지 빌드

이 문서에서는 Microsoft 상업적 marketplace에서 판매 될 불가능 SaaS 앱에 대 한 방문 페이지를 빌드하는 과정을 안내 합니다.

## <a name="overview"></a>개요

방문 페이지는 SaaS (software as a service) 제품에 대 한 "로비"로 생각할 수 있습니다. 구매자가 제품을 구독 하면 상업적 marketplace에서 해당 제품을 방문 페이지로 보내 SaaS 응용 프로그램에 대 한 구독을 활성화 하 고 구성 합니다. 구매자가 구매한 항목을 확인 하 고 계정 정보를 확인할 수 있는 주문 확인 단계로 생각 합니다. Azure Active Directory (Azure AD) 및 Microsoft Graph를 사용 하 여 구매자에 대해 SSO (Single Sign-On)를 사용 하도록 설정 하 고 이름, 전자 메일 주소 및 조직을 포함 하 여 구독을 확인 하 고 활성화 하는 데 사용할 수 있는 구매자에 대 한 중요 한 세부 정보를 얻을 수 있습니다.

구독을 활성화 하는 데 필요한 정보는 Azure AD 및 Microsoft Graph에서 제한적이 고 제공 되므로, 기본 동의가 필요한 정보를 요청할 필요가 없습니다. 응용 프로그램에 대 한 추가 동의가 필요한 사용자 세부 정보가 필요한 경우 구독 활성화가 완료 된 후이 정보를 요청 해야 합니다. 따라서 구매자에 대해 원활한을 활성화 하 고 중단 위험을 줄일 수 있습니다.

방문 페이지는 일반적으로 다음을 포함 합니다.

- 청구 조건 뿐만 아니라 구매한 제품 및 계획의 이름을 표시 합니다.
- 성과 이름, 조직 및 전자 메일을 포함 하 여 구매자의 계정 세부 정보를 표시 합니다.
- 구매자에 게 다른 계정 정보를 확인 하거나 대체 하도록 요청 합니다.
- 활성화 후 다음 단계에서 구매자를 안내 합니다. 예를 들어 환영 전자 메일을 받거나 구독을 관리 하거나 지원을 받거나 문서를 읽을 수 있습니다.

> [!NOTE]
> 구독을 활성화 한 후 관리 하는 경우에도 구매자가 방문 페이지로 이동 합니다. 구매자의 구독이 활성화 된 후에는 SSO를 사용 하 여 사용자가 로그인 할 수 있도록 해야 합니다. 사용자를 계정 프로필 또는 구성 페이지로 안내 하는 것이 좋습니다.

다음 섹션에서는 방문 페이지를 빌드하는 과정을 안내 합니다.

1. 방문 페이지에 대 한 [AZURE AD 앱 등록을 만듭니다](#create-an-azure-ad-app-registration) .
2. [코드 샘플을 앱의 시작 지점으로 사용](#use-a-code-sample-as-a-starting-point) 합니다.
3. 상용 marketplace에서 URL에 추가한 [marketplace 구매 식별 토큰을 확인](#resolve-the-marketplace-purchase-identification-token) 합니다.
4. 로그인 후 Azure AD에서 받은 [ID 토큰으로 인코딩된 클레임에서](#read-information-from-claims-encoded-in-the-id-token)요청을 통해 보낸 정보를 읽습니다.
5. [MICROSOFT GRAPH API를 사용](#use-the-microsoft-graph-api) 하 여 필요에 따라 추가 정보를 수집 합니다.
6. [두 AZURE AD 앱을 사용 하 여 프로덕션의 보안을 향상 시킵니다](#use-two-azure-ad-apps-to-improve-security-in-production).

## <a name="create-an-azure-ad-app-registration"></a>Azure AD 앱 등록 만들기

상업적 marketplace는 Azure AD와 완전히 통합 됩니다. 구매자는 [AZURE AD 계정 또는 Microsoft 계정 (MSA)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology)로 인증 된 marketplace에 도착 합니다. 구매 후 구매자는 상업적 marketplace에서 방문 페이지 URL로 이동 하 여 SaaS 응용 프로그램의 구독을 활성화 하 고 관리 합니다. 구매자가 Azure AD SSO를 사용 하 여 응용 프로그램에 로그인 하도록 허용 해야 합니다. 방문 페이지 URL은 제품의 [기술 구성](partner-center-portal/offer-creation-checklist.md#technical-configuration-page) 페이지에 지정 되어 있습니다.

Id를 사용 하는 첫 번째 단계는 방문 페이지가 Azure AD 응용 프로그램으로 등록 되었는지 확인 하는 것입니다. 응용 프로그램을 등록 하면 Azure AD를 사용 하 여 사용자를 인증 하 고 사용자 리소스에 대 한 액세스를 요청할 수 있습니다. 응용 프로그램의 정의로 간주할 수 있으며,이를 통해 서비스는 앱의 설정에 따라 응용 프로그램에 토큰을 발급 하는 방법을 알 수 있습니다.

### <a name="register-a-new-application-using-the-azure-portal"></a>Azure Portal을 사용하여 새 애플리케이션 등록

시작 하려면 [새 응용 프로그램을 등록](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)하는 방법에 대 한 지침을 따르세요. 다른 회사의 사용자가 앱을 방문할 수 있도록 하려면 응용 프로그램을 사용할 수 있는 사람을 요청할 때 다중 테 넌 트 옵션 중 하나를 선택 해야 합니다.

Microsoft Graph API를 쿼리하려면 [웹 api에 액세스 하도록 새 응용 프로그램을 구성](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis)합니다. 이 응용 프로그램에 대 한 API 사용 권한을 선택 하는 경우에는 기본적으로 사용자의 기본 정보를 수집 하 여 온 보 딩 프로세스를 원활 하 고 자동으로 설정할 수 있습니다 **.** 모든 관리자가 아닌 사용자가 방문 페이지를 방문 하지 못하도록 차단 하므로 관리자 **동의가 필요 하다**고 표시 된 API 권한은 요청 하지 마세요.

등록 또는 프로 비전 프로세스의 일환으로 상승 된 권한이 필요한 경우 marketplace에서 보낸 모든 구매자가 처음 방문 페이지와 상호 작용할 수 있도록 Azure AD의 [증분 승인](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis) 기능을 사용 하는 것이 좋습니다.

## <a name="use-a-code-sample-as-a-starting-point"></a>코드 샘플을 시작 지점으로 사용

Azure AD 로그인을 사용 하는 간단한 웹 사이트를 구현 하는 몇 가지 샘플 앱을 제공 했습니다. 응용 프로그램이 Azure AD에 등록 된 후 **빠른** 시작 블레이드는 그림 1에 표시 된 것 처럼 일반적인 응용 프로그램 유형 및 개발 스택 목록을 제공 합니다. 사용자 환경과 일치 하는 항목을 선택 하 고 다운로드 및 설정 지침을 따릅니다.

***그림 1: Azure Portal의 빠른 시작 블레이드***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Azure Portal의 빠른 시작 블레이드를 보여 줍니다.":::

코드를 다운로드 하 고 개발 환경을 설정한 후 이전 절차에서 기록한 응용 프로그램 ID, 테 넌 트 ID 및 클라이언트 암호를 반영 하도록 앱의 구성 설정을 변경 합니다. 정확한 단계는 사용 중인 샘플에 따라 달라 집니다.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>두 Azure AD 앱을 사용 하 여 프로덕션 환경에서 보안 강화

이 문서에서는 상업적 marketplace SaaS 제품의 방문 페이지를 구현 하는 데 필요한 아키텍처의 간소화 된 버전을 제공 합니다. 프로덕션에서 페이지를 실행 하는 경우 다른 보안 응용 프로그램을 통해서만 SaaS 처리 Api와 통신 하 여 보안을 개선 하는 것이 좋습니다. 이렇게 하려면 다음과 같은 두 개의 새 응용 프로그램을 만들어야 합니다.

- 먼저 SaaS 처리 Api에 연결 하는 기능이 없는 경우를 제외 하 고이 시점까지 설명 된 다중 테 넌 트 방문 페이지 응용 프로그램입니다. 이 기능은 아래에 설명 된 대로 다른 응용 프로그램으로 오프 로드 됩니다.
- 둘째로, SaaS 처리 Api와의 통신을 소유 하는 응용 프로그램입니다. 이 응용 프로그램은 단일 테 넌 트 여야 하며, 조직 에서만 사용 해야 하며,이 앱 에서만 Api에 대 한 액세스를 제한 하도록 액세스 제어 목록을 설정할 수 있습니다.

이렇게 하면 [문제](https://docs.microsoft.com/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) 를 해결 하는 원칙을 관찰 하는 시나리오에서 솔루션을 사용할 수 있습니다. 예를 들어 방문 페이지는 등록 된 첫 번째 Azure AD 앱을 사용 하 여 사용자를 로그인 합니다. 사용자가 로그인 한 후에는 방문 페이지에서 두 번째 Azure AD를 사용 하 여 SaaS 처리 API를 호출 하 고 확인 작업을 호출 하는 액세스 토큰을 요청 합니다.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Marketplace 구매 식별 토큰을 확인 합니다.

구매자가 방문 페이지로 보내지면 URL 매개 변수에 토큰이 추가 됩니다. 이 토큰은 Azure AD에서 발급 한 토큰 및 서비스 간 인증에 사용 되는 액세스 토큰과 다르며 [SaaS 처리 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 를 확인 하 여 구독의 세부 정보를 가져오는 입력으로 사용 됩니다. SaaS 처리 Api에 대 한 모든 호출과 마찬가지로 서비스 간 요청은 서비스 간 인증에 대 한 앱의 Azure AD 응용 프로그램 ID 사용자를 기반으로 하는 액세스 토큰으로 인증 됩니다.

> [!NOTE]
> 대부분의 경우 두 번째 단일 테 넌 트 응용 프로그램에서이 호출을 수행 하는 것이 좋습니다. 이 문서의 뒷부분에서 [두 AZURE AD 앱을 사용 하 여 프로덕션 환경에서 보안 강화를](#use-two-azure-ad-apps-to-improve-security-in-production) 참조 하세요.

### <a name="request-an-access-token"></a>액세스 토큰 요청

SaaS 처리 Api를 사용 하 여 응용 프로그램을 인증 하려면 Azure AD OAuth 끝점을 호출 하 여 생성할 수 있는 액세스 토큰이 필요 합니다. [게시자의 인증 토큰을 가져오는 방법을](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)참조 하세요.

### <a name="call-the-resolve-endpoint"></a>Resolve 끝점 호출

SaaS 처리 Api는 marketplace 토큰의 유효성을 확인 하 고이 테이블에 표시 된 값을 포함 하 여 구독에 대 한 정보를 반환 하기 위해 호출할 수 있는 [resolve](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 끝점을 구현 합니다.

| 값 | 설명 |
| ------------ | ------------- |
| Id | 이 구독의 고유 식별자 (GUID)입니다. 향후 SaaS 처리 Api에 대 한 호출에서이 값이 필요 합니다. |
| subscriptionName | 제품이 파트너 센터에 추가 될 때 설정 된 구독의 이름입니다. |
| offerId | 특정 제안에 대 한 식별자입니다 (제품이 추가 된 경우 설정). |
| planId | 제품에 대 한 특정 요금제에 대 한 식별자입니다 (제품이 추가 된 경우 설정). |
| 수량 | 구매 중 구매자의 수량 입력. |
|||

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>ID 토큰에서 인코딩된 클레임에서 정보를 읽습니다.

[Openid connect Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 흐름의 일부로 Azure AD는 구매자가 방문 페이지로 전송 될 때 요청에 [ID 토큰](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) 을 추가 합니다. 이 토큰에는이 표에 표시 된 정보를 비롯 하 여 활성화 프로세스에 유용할 수 있는 여러 가지 기본 정보가 포함 되어 있습니다.

| 값 | 설명 |
| ------------ | ------------- |
| aud | 이 토큰의 대상입니다. 이 경우 응용 프로그램 ID와 일치 하 고 유효성을 검사 해야 합니다. |
| preferred_username | 방문 사용자의 기본 사용자 이름입니다. 전자 메일 주소, 전화 번호 또는 기타 식별자 일 수 있습니다. |
| 이메일 | 사용자의 전자 메일 주소입니다. 이 필드는 비어 있을 수 있습니다. |
| name | 토큰의 주체를 식별 하는 사람이 읽을 수 있는 값입니다. 이 경우 구매자 이름이 됩니다. |
| oid | 응용 프로그램 간에 사용자를 고유 하 게 식별 하는 Microsoft id 시스템의 식별자입니다. Microsoft Graph는 지정 된 사용자 계정에 대 한 ID 속성으로이 값을 반환 합니다. |
| tid | 구매자가 속하는 Azure AD 테 넌 트를 나타내는 식별자입니다. MSA id의 경우이는 항상 ``9188040d-6c67-4c5b-b112-36a304b66dad`` 입니다. 자세한 내용은 다음 섹션의 Microsoft Graph API 사용을 참조 하세요. |
| sub | 이 특정 응용 프로그램에서 사용자를 고유 하 게 식별 하는 식별자입니다. |
|||

## <a name="use-the-microsoft-graph-api"></a>Microsoft Graph API 사용

ID 토큰에는 구매자를 식별 하기 위한 기본 정보가 포함 되어 있지만 정품 인증 프로세스를 완료 하려면 구매자 회사와 같은 추가 정보가 필요할 수 있습니다. [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api) 를 사용 하 여이 정보를 요청 하면 사용자가이 정보를 다시 입력 하지 않도록 방지할 수 있습니다. 표준 **사용자. 읽기** 권한에는 기본적으로 다음 정보가 포함 됩니다.

| 값 | 설명 |
| ------------ | ------------- |
| displayName | 사용자의 주소록에 표시 되는 이름입니다. |
| givenName | 사용자의 이름입니다. |
| jobTitle | 사용자의 직함입니다. |
| mail | 사용자의 SMTP 주소입니다. |
| MobilePhone | 사용자의 기본 휴대 전화 번호입니다. |
| preferredLanguage | 사용자의 기본 설정 언어에 대 한 ISO 639-1 코드입니다. |
| surname | 사용자의 성입니다. |
|||

사용자의 회사 이름 또는 사용자의 위치 (국가)와 같은 추가 속성을 선택 하 여 요청에 포함할 수 있습니다. 자세한 내용은 [사용자 리소스 형식에 대 한 속성](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties) 을 참조 하세요.

Azure AD에 등록 된 대부분의 앱은 회사의 Azure AD 테 넌 트에서 사용자 정보를 읽을 수 있도록 위임 된 권한을 부여 합니다. 해당 정보에 대 한 Microsoft Graph 요청에는 인증을 위해 액세스 토큰이 수반 되어야 합니다. 액세스 토큰을 생성 하는 특정 단계는 사용 중인 기술 스택에 따라 다르지만 샘플 코드에는 예제가 포함 됩니다. 자세한 내용은 [사용자를 대신 하 여 액세스 권한 가져오기](https://docs.microsoft.com/graph/auth-v2-user)를 참조 하세요.

> [!NOTE]
> MSA 테 넌 트의 계정 (테 넌 트 ID ``9188040d-6c67-4c5b-b112-36a304b66dad`` )은 ID 토큰을 사용 하 여 이미 수집 된 것 보다 많은 정보를 반환 하지 않습니다. 따라서 이러한 계정에 대 한 Graph API 호출을 건너뛸 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [상업적 marketplace에서 SaaS 제품 만들기](./partner-center-portal/create-new-saas-offer.md)
