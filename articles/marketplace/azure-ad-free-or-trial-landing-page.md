---
title: 상용 marketplace에서 무료 또는 평가판 SaaS 제품에 대 한 방문 페이지 빌드
description: 무료 또는 평가판 SaaS 제품에 대 한 방문 페이지를 빌드하는 방법에 대해 알아봅니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: dfa5d77077b8827bed1cbd8c7a46a5dbf361f139
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125719"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>상용 marketplace에서 무료 또는 평가판 SaaS 제품에 대 한 방문 페이지 빌드

이 문서에서는 Microsoft 상업적 marketplace에서 판매 될 무료 또는 평가판 SaaS 앱에 대 한 방문 페이지를 빌드하는 과정을 안내 합니다.

## <a name="overview"></a>개요

방문 페이지는 SaaS (software as a service) 제품에 대 한 "로비"로 생각할 수 있습니다. 고객이 앱을 가져오도록 선택한 후에는 상업적 marketplace에서 방문 페이지로 이동 하 여 SaaS 응용 프로그램에 대 한 구독을 활성화 하 고 구성 합니다. SaaS (software as a service) 제품을 만들 때 파트너 센터에서 [Microsoft를 통해 판매할](plan-saas-offer.md#listing-options)지 여부를 선택할 수 있습니다. Microsoft에서 판매 하지 않고 Microsoft 상업적 marketplace 에서만 제품을 나열 하려는 경우 잠재 고객이 제품을 조작할 수 있는 방법을 지정할 수 있습니다. **지금 가져오기 (무료)** 또는 **무료 평가판** 목록 옵션을 사용 하도록 설정 하면 사용자가 무료 구독 또는 평가판에 액세스할 수 있는 방문 페이지 URL을 지정 해야 합니다.

방문 페이지의 목적은 사용자가 무료 평가판 또는 무료 구독을 활성화할 수 있도록 사용자를 수신 하는 것입니다. Azure Active Directory (Azure AD) 및 Microsoft Graph를 사용 하 여 사용자에 대해 SSO (Single Sign-On)를 사용 하도록 설정 하 고 이름, 전자 메일 주소 및 조직을 포함 하 여 무료 평가판 또는 무료 구독을 활성화 하는 데 사용할 수 있는 사용자에 대 한 중요 한 세부 정보를 얻을 수 있습니다.

구독을 활성화 하는 데 필요한 정보는 Azure AD 및 Microsoft Graph에서 제한적이 고 제공 되므로, 기본 동의가 필요한 정보를 요청할 필요가 없습니다. 응용 프로그램에 대 한 추가 동의가 필요한 사용자 세부 정보가 필요한 경우 구독 활성화가 완료 된 후이 정보를 요청 해야 합니다. 이렇게 하면 사용자에 대해 원활한을 활성화 하 고 중단 위험을 줄일 수 있습니다.

방문 페이지는 일반적으로 다음 정보 및 목록 옵션을 포함 합니다.

- 무료 평가판 또는 무료 구독의 이름 및 세부 정보를 표시 합니다. 예를 들어 평가판의 사용 제한 또는 기간을 지정 합니다.
- 성과 이름, 조직 및 전자 메일을 포함 하 여 사용자의 계정 세부 정보를 표시 합니다.
- 사용자에 게 다른 계정 정보를 확인 하거나 대체 하 라는 메시지를 표시 합니다.
- 활성화 후 다음 단계에 대해 사용자에 게 안내 합니다. 예를 들어 환영 전자 메일을 받거나 구독을 관리 하거나 지원을 받거나 문서를 읽을 수 있습니다.

이 문서의 다음 섹션에서는 방문 페이지를 빌드하는 과정을 안내 합니다.

1. 방문 페이지에 대 한 [AZURE AD 앱 등록을 만듭니다](#create-an-azure-ad-app-registration) .
2. [코드 샘플을 앱의 시작 지점으로 사용](#use-a-code-sample-as-a-starting-point) 합니다.
3. 요청과 함께 전송 된, 로그인 후에 Azure AD에서 받은 [ID 토큰으로 인코딩된 클레임에서 정보를 읽습니다](#read-information-from-claims-encoded-in-the-id-token).
4. [MICROSOFT GRAPH API를 사용](#use-the-microsoft-graph-api) 하 여 필요에 따라 추가 정보를 수집 합니다.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD 앱 등록 만들기

상업적 marketplace는 Azure AD와 완전히 통합 됩니다. 사용자는 [AZURE AD 계정 또는 Microsoft 계정 (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology)로 인증 된 marketplace에 도착 합니다. 목록 전용 제안을 통해 무료 평가판 구독을 확보 한 후 사용자는 상업적 marketplace에서 방문 페이지 URL로 이동 하 여 SaaS 응용 프로그램에 대 한 구독을 활성화 하 고 관리 합니다. 사용자가 Azure AD SSO를 사용 하 여 응용 프로그램에 로그인 하도록 허용 해야 합니다. 방문 페이지 URL은 제품의 [기술 구성](plan-saas-offer.md#technical-information) 페이지에 지정 되어 있습니다.

Id를 사용 하는 첫 번째 단계는 방문 페이지가 Azure AD 응용 프로그램으로 등록 되었는지 확인 하는 것입니다. 응용 프로그램을 등록 하면 Azure AD를 사용 하 여 사용자를 인증 하 고 사용자 리소스에 대 한 액세스를 요청할 수 있습니다. 응용 프로그램의 정의로 간주할 수 있으며,이를 통해 서비스는 앱의 설정에 따라 응용 프로그램에 토큰을 발급 하는 방법을 알 수 있습니다.

### <a name="register-a-new-application-using-the-azure-portal"></a>Azure Portal을 사용하여 새 애플리케이션 등록

시작 하려면 [새 응용 프로그램을 등록](../active-directory/develop/quickstart-register-app.md)하는 방법에 대 한 지침을 따르세요. 다른 회사의 사용자가 앱을 방문할 수 있도록 하려면 응용 프로그램을 사용할 수 있는 사람을 요청할 때 **모든 조직 디렉터리 (모든 AZURE AD 디렉터리-다중 테 넌 트) 및 개인 Microsoft 계정 (예: Skype 또는 Xbox)의 계정을** 선택 해야 합니다.

Microsoft Graph API를 쿼리하려면 [웹 api에 액세스 하도록 새 응용 프로그램을 구성](../active-directory/develop/quickstart-configure-app-access-web-apis.md)합니다. 이 응용 프로그램에 대 한 API 사용 권한을 선택 하는 경우 **사용자** 의 기본값은 사용자에 대 한 기본 정보를 수집 하 여 온 보 딩 프로세스를 원활 하 고 자동으로 설정 하는 데 충분 합니다. 모든 관리자가 아닌 사용자가 방문 페이지를 방문 하지 못하도록 차단 하므로 관리자 **동의가 필요 하다** 고 표시 된 API 권한은 요청 하지 마세요.

등록 또는 프로 비전 프로세스의 일부로 상승 된 권한이 필요한 경우 marketplace에서 전송 되는 모든 사용자가 처음 방문 페이지와 상호 작용할 수 있도록 Azure AD의 [증분 승인](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) 기능을 사용 하는 것이 좋습니다.

## <a name="use-a-code-sample-as-a-starting-point"></a>코드 샘플을 시작 지점으로 사용

Microsoft는 Azure AD 로그인을 사용 하는 간단한 웹 사이트를 구현 하는 몇 가지 샘플 앱을 제공 했습니다. 응용 프로그램이 Azure AD에 등록 된 후 **빠른** 시작 블레이드는 일반적인 응용 프로그램 유형 및 개발 스택 목록을 제공 합니다 (그림 1). 사용자 환경과 일치 하는 항목을 선택 하 고 다운로드 및 설정 지침을 따릅니다.

**_그림 1: Azure Portal _의 빠른 시작 블레이드_*

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Azure Portal의 빠른 시작 블레이드를 보여 줍니다.":::

코드를 다운로드 하 고 개발 환경을 설정한 후 이전 절차에서 기록한 응용 프로그램 ID, 테 넌 트 ID 및 클라이언트 암호를 반영 하도록 앱의 구성 설정을 변경 합니다. 정확한 단계는 사용 중인 샘플에 따라 달라 집니다.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>ID 토큰에서 인코딩된 클레임에서 정보를 읽습니다.

[Openid connect Connect](../active-directory/develop/v2-protocols-oidc.md) 흐름의 일부로 Azure AD는 사용자가 방문 페이지로 전송 될 때 요청에 [ID 토큰](../active-directory/develop/id-tokens.md) 을 추가 합니다. 이 토큰에는이 표에 표시 된 정보를 비롯 하 여 활성화 프로세스에 유용할 수 있는 여러 가지 기본 정보가 포함 되어 있습니다.

| 값 | 설명 |
| ------------ | ------------- |
| aud | 이 토큰의 대상입니다. 이 경우 응용 프로그램 ID와 일치 하 고 유효성을 검사 해야 합니다. |
| preferred_username | 방문 사용자의 기본 사용자 이름입니다. 전자 메일 주소, 전화 번호 또는 기타 식별자 일 수 있습니다. |
| 이메일 | 사용자의 전자 메일 주소입니다. 이 필드는 비어 있을 수 있습니다. |
| name | 토큰의 주체를 식별 하는 사람이 읽을 수 있는 값입니다. 이 경우 사용자 이름이 됩니다. |
| oid | 응용 프로그램 간에 사용자를 고유 하 게 식별 하는 Microsoft id 시스템의 식별자입니다. Microsoft Graph는 지정 된 사용자 계정에 대 한 ID 속성으로이 값을 반환 합니다. |
| tid | 사용자가 있는 Azure AD 테 넌 트를 나타내는 식별자입니다. MSA id의 경우이는 항상 `9188040d-6c67-4c5b-b112-36a304b66dad` 입니다. 자세한 내용은 다음 섹션의 Microsoft Graph API 사용을 참조 하세요. |
| sub | 이 특정 응용 프로그램에서 사용자를 고유 하 게 식별 하는 식별자입니다. |
|||

## <a name="use-the-microsoft-graph-api"></a>Microsoft Graph API 사용

ID 토큰에는 사용자를 식별 하기 위한 기본 정보가 포함 되어 있지만 활성화 프로세스에는 온 보 딩 프로세스를 완료 하는 데 필요한 추가 정보 (예: 사용자 회사)가 필요할 수 있습니다. [MICROSOFT GRAPH API](/graph/use-the-api) 를 사용 하 여이 정보를 요청 하면 사용자가이 정보를 다시 입력 하지 않도록 방지할 수 있습니다. 표준 _ *사용자. 읽기* * 권한은 기본적으로 다음 정보를 포함 합니다.

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

사용자의 회사 이름 또는 사용자의 위치 (국가)와 같은 추가 속성을 선택 하 여 요청에 포함할 수 있습니다. 자세한 내용은 [사용자 리소스 종류에 대 한 속성](/graph/api/resources/user?view=graph-rest-1.0#properties)을 참조 하세요.

Azure AD에 등록 된 대부분의 앱은 회사의 Azure AD 테 넌 트에서 사용자 정보를 읽을 수 있도록 위임 된 권한을 부여 합니다. 해당 정보를 Microsoft Graph 하는 모든 요청에는 인증으로 액세스 토큰이 수반 되어야 합니다. 액세스 토큰을 생성 하는 특정 단계는 사용 중인 기술 스택에 따라 다르지만 샘플 코드에는 예제가 포함 됩니다. 자세한 내용은 [사용자를 대신 하 여 액세스 권한 가져오기](/graph/auth-v2-user)를 참조 하세요.

> [!NOTE]
> MSA 테 넌 트의 계정 (테 넌 트 ID `9188040d-6c67-4c5b-b112-36a304b66dad` )은 ID 토큰을 사용 하 여 이미 수집 된 것 보다 많은 정보를 반환 하지 않습니다. 따라서 이러한 계정에 대 한 Graph API 호출을 건너뛸 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [상업적 marketplace에서 SaaS 제품을 만드는 방법](create-new-saas-offer.md)