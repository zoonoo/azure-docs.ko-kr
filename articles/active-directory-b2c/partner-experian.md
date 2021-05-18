---
title: Experian을 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 사기 방지를 위해 사용자 특성을 기반으로 하여 ID 확인 및 교정을 위해 Experian과 Azure AD B2C 인증을 통합하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 84e6f76bcae55ed905e11c46df66478908529970
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258030"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 Experian을 구성하는 방법에 대한 자습서

이 샘플 자습서에서는 Azure AD B2C를 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)과 통합하는 방법에 대한 지침을 제공합니다. Experian은 [여기](https://www.experian.com/)에서 찾을 수 있는 다양한 솔루션을 제공합니다.

이 샘플에서는 Experian의 통합 디지털 ID 및 사기 위험 플랫폼 **CrossCore** 가 사용됩니다. CrossCore는 사용자 ID를 확인하는 데 사용되는 ID 확인 서비스입니다. 등록 흐름 중에 사용자가 제공하는 여러 정보를 기반으로 위험 분석을 수행합니다. CrossCore는 사용자가 계속 로그인할 수 있도록 허용할지 여부를 결정하는 데 사용됩니다. CrossCore 위험 분석에 사용할 수 있는 특성은 다음과 같습니다.

- 메일
- IP 주소
- 이름
- Middle Name
- Surname
- 주소
- City
- 시/도
- 우편 번호
- 국가/지역
- 전화 번호

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 사항이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결된 [Azure AD B2C 테넌트](./tutorial-create-tenant.md)

## <a name="scenario-description"></a>시나리오 설명

Experian 통합에는 다음 구성 요소가 포함됩니다.

- Azure AD B2C - 사용자의 자격 증명(ID 공급자라고도 함) 확인을 담당하는 권한 부여 서버입니다.

- Experian – Experian 서비스는 사용자가 제공하는 입력을 사용하여 사용자의 ID를 확인합니다.

- 사용자 지정 Rest API – 이 API는 Azure AD B2C와 Experian 서비스 간의 통합을 구현합니다.

다음 다이어그램은 아키텍처 구현을 보여 줍니다.

![experian-architecture-diagram 스크린샷](media/partner-experian/experian-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지를 엽니다. 사용자가 등록을 선택하여 새 계정을 만들고 페이지에 정보를 입력합니다. Azure AD B2C가 사용자 특성을 수집합니다.
| 2. | Azure AD B2C는 중간 계층 API를 호출하고 사용자 특성을 전달합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집하고 Experian API에서 사용할 수 있는 형식으로 변환합니다. 그런 다음 Experian으로 보냅니다.
| 4. | Experian은 정보를 사용하고 이를 처리하여 위험 분석을 기반으로 사용자 ID의 유효성을 검사합니다. 그런 다음 중간 계층 API에 결과를 반환합니다.
| 5. | 중간 계층 API는 정보를 처리하고 Azure AD B2C에 올바른 JSON 형식으로 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시되면 사용자에게 오류 메시지가 표시됩니다. 성공 응답이 표시되면 사용자가 인증되고 디렉터리에 기록됩니다.

## <a name="onboard-with-experian"></a>Experian으로 온보딩

1. Experian 계정을 만들려면 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)에 문의하세요.

2. 계정이 만들어지면 API 구성에 필요한 정보를 받게 됩니다. 다음 섹션은 이 프로세스를 설명합니다.

## <a name="configure-azure-ad-b2c-with-experian"></a>Experian을 사용하여 Azure AD B2C 구성

### <a name="part-1---deploy-the-api"></a>1부 - API 배포

제공된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln)를 Azure 서비스에 배포합니다. 이러한 [지침](/visualstudio/deployment/quickstart-deploy-to-azure)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.

>[!NOTE]
>필요한 설정을 사용하여 Azure AD를 구성하려면 배포된 서비스의 URL이 필요합니다.

### <a name="part-2---deploy-the-client-certificate"></a>2부 - 클라이언트 인증서 배포

Experian API 호출은 클라이언트 인증서로 보호됩니다. 이 클라이언트 인증서는 Experian에서 제공됩니다. 이 [문서](../app-service/environment/certificates.md#private-client-certificate)에 설명된 지침에 따라 인증서를 Azure App Service에 업로드해야 합니다. 이 샘플 정책은 프로세스에서 다음 주요 단계를 사용합니다.

- 인증서 업로드

- 인증서의 지문을 사용하여 `WEBSITE_LOAD_ROOT_CERTIFICATES` 키를 설정합니다.

### <a name="part-3---configure-the-api"></a>3부 - API 구성

애플리케이션 설정은 [Azure의 App Service에서 구성](../app-service/configure-common.md#configure-app-settings)할 수 있습니다. 이 방법을 사용하면 설정을 리포지토리로 체크 인하지 않고도 안전하게 구성할 수 있습니다. Rest API에 대한 다음 설정을 제공해야 합니다.

| 애플리케이션 설정 | 원본 | 메모 |
| :-------- | :------------| :-----------|
|CrossCoreConfig:TenantId | Experian 계정 구성 |     |
|CrossCoreConfig:OrgCode | Experian 계정 구성 |     |
|CrossCore:ApiEndpoint |Experian 계정 구성|  |
|CrossCore:ClientReference | Experian 계정 구성 | |
| CrossCore:ModelCode |Experian 계정 구성|
| CrossCore:OrgCode | Experian 계정 구성 |
| CrossCore:SignatureKey  | Experian 계정 구성 |
| CrossCore:TenantId  | Experian 계정 구성 |
| CrossCore:CertificateThumbprint | Experian 인증서 |
| BasicAuth:ApiUsername | API 사용자 이름 정의 | ExtId 구성에서 사용됨 |
| BasicAuth:ApiPassword | API 암호 정의 | ExtId 구성에서 사용됨

### <a name="part-4---create-api-policy-keys"></a>4부 - API 정책 키 만들기

이 [문서](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys)를 참조하여 두 개의 정책 키를 만듭니다. 하나는 API 사용자 이름, 하나는 HTTP 기본 인증에 대해 위에서 정의한 API 암호입니다.

>[!NOTE]
>나중에 정책을 구성하기 위한 키가 필요합니다.

### <a name="part-5---replace-the-configuration-values"></a>5부 - 구성 값 바꾸기

제공된 [사용자 지정 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)에서 다음 자리 표시자를 찾아 인스턴스의 해당 값으로 바꿉니다.

|                      자리표시자                       |                                   대체할 값                                 |                   예제                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | 테넌트 약식 이름                                                           | yourtenant.onmicrosoft.com의 "yourtenant" |
| {your_trustframeworkbase_policy}                       | Trustframeworkbase.xml 정책의 Azure AD B2C 이름                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 테넌트에 구성된 IdentityExperienceFramework 앱의 앱 ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 테넌트에 구성된 ProxyIdentityExperienceFramework 앱의 앱 ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 테넌트 스토리지 애플리케이션의 앱 ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 테넌트 스토리지 애플리케이션의 개체 ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | [여기](#part-4---create-api-policy-keys)에서 만든 사용자 이름 키의 이름입니다.             | B2C\_1A\_RestApiUsername                     |
| {your_api_password_key_name}                           | [여기](#part-4---create-api-policy-keys)에서 만든 암호 키의 이름입니다.             | B2C\_1A\_RestApiPassword                     |
| {your_app_service_URL}                                 | 설정한 App Service의 URL                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>6부 - Azure AD B2C 정책 구성

Azure AD B2C 테넌트를 설정하고 정책을 구성하는 방법에 대한 지침은 이 [문서](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)를 참조하세요.

>[!NOTE]
>이 샘플 정책은 [로컬 계정 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 기반으로 합니다.

>[!NOTE]
> 모범 사례로, 고객은 특성 컬렉션 페이지에서 동의 알림을 추가하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보가 보내짐을 사용자에게 알립니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 정책 아래에서 **사용자 흐름** 을 선택합니다.

2. 이전에 만든 **사용자 흐름** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   a. **애플리케이션**: 등록된 앱을 선택합니다(JWT는 샘플).

   b. **회신 URL**: **리디렉션 URL** 을 선택합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 따라 계정 만들기

5. 로그아웃

6. 로그인 흐름 진행  

7. **continue** 를 입력하면 CrossCore 퍼즐이 팝업됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C에서 사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
