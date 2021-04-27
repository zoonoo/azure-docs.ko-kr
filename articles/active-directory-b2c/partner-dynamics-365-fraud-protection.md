---
title: Microsoft Dynamics 365 Fraud Protection을 사용하여 Azure Active Directory B2C를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 위험한 계정 및 사기성 계정을 식별하기 위해 Microsoft Dynamics 365 Fraud Protection을 사용하여 Azure Active Directory B2C를 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8c9d760ed888eb194ad8f282f180a634e3c09538
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587819"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C을 사용하여 Microsoft Dynamics 365 Fraud Protection 구성

이 샘플 자습서에서는 AD(Azure Active Directory) B2C를 사용하여 [Microsoft DFP(Dynamics 365 Fraud Protection)](https://docs.microsoft.com/dynamics365/fraud-protection/overview)를 통합하는 방법에 대한 지침을 제공합니다.

Microsoft DFP는 클라이언트에게 새 계정을 만들도록 시도하는 위험과 클라이언트 에코시스템에 대한 로그인 시도가 사기인지 여부를 평가하는 기능을 제공합니다. 고객은 Microsoft DFP 평가를 사용하여 새 가짜 계정을 만들거나 기존 계정을 손상시킬 위험이 있는 의심스러운 시도를 차단할 수 있습니다. 계정 보호에는 인공 지능 기반 디바이스 핑거프린팅, 실시간 위험 평가를 위한 API, 클라이언트의 비즈니스 요구에 따라 위험 전략을 최적화하는 규칙 및 목록 환경, 클라이언트 에코 시스템의 사기 행위 보호 효율성 및 추세를 모니터링하는 성과 기록표가 포함됩니다.

이 샘플에서는 Microsoft BFP의 계정 보호 기능을 Azure AD B2C 사용자 흐름과 통합합니다. 해당 서비스는 모든 로그인 또는 등록 시도에 대해 외부에서 신원을 알아내고 과거 또는 현재의 의심스러운 동작을 찾아냅니다. Azure AD B2C는 사용자의 모든 과거 및 현재 동작을 기반으로 결과를 반환하고, Microsoft DFP 서비스 내에 지정된 사용자 지정 규칙을 사용하여 Microsoft DFP에서 결정 엔드포인트를 호출합니다. 이 결과에 따라 Azure AD B2C는 승인을 결정하고 Microsoft DFP에게 동일한 내용을 전달합니다.

## <a name="prerequisites"></a>필수 요건

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md) 테넌트는 Azure 구독과 연결됩니다.

- Microsoft EFP [구독](https://dynamics.microsoft.com/pricing/#Sales)을 가져옵니다. [평가판 클라이언트 버전](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)도 설정할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

Microsoft EFP 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C 테넌트**: 사용자를 인증하고 Microsoft DFP의 클라이언트 역할을 합니다. 대상 정책을 실행하는 모든 사용자의 식별 및 진단 데이터를 수집하는 핑거프린팅 스크립트를 호스팅합니다. Microsoft DFP에서 의심스럽다고 여기는 경우 이후에 로그인 또는 등록 시도를 차단합니다.

- **사용자 지정 앱 서비스**: 두 가지 용도로 사용되는 웹 애플리케이션입니다.

  - HTML 페이지를 Identity Experience Framework의 UI로 사용할 수 있도록 합니다. Microsoft Dynamics 365 핑거프린팅 스크립트를 포함해야 합니다.

  - Azure AD B2C에 Microsoft DFP를 연결하는 RESTful 엔드포인트가 포함된 API 컨트롤러입니다. 핸들의 데이터 처리, 구조 및 둘 다의 보안 요구 사항을 준수합니다.

- **Microsoft DFP 핑거프린팅 서비스**: 동적으로 포함된 스크립트이며, 디바이스 원격 분석 및 자체 어설션된 사용자 세부 정보를 기록하여 사용자가 의사 결정 과정에서 나중에 사용할 수 있도록 고유하게 식별할 수 있는 지문을 만듭니다.

- **Microsoft DFP API 엔드포인트**: 의사 결정 결과를 제공하고 클라이언트 애플리케이션에서 수행하는 작업을 반영하는 최종 상태를 수락합니다. Azure AD B2C는 다양한 보안 및 API 페이로드 요구 사항으로 인해 엔드포인트와 직접 통신하지 않으며, 대신 앱 서비스를 거쳐 통신합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![Microsoft Dynamics 365 Fraud Protection 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 도달합니다. 사용자는 등록을 선택하여 새 계정을 만들고 페이지에 정보를 입력합니다. Azure AD B2C는 사용자 특성을 수집합니다.
| 2. | Azure AD B2C는 중간 API를 호출하고 사용자 특성을 전달합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집하고 Microsoft DFP API에서 사용할 수 있는 형식으로 변환합니다. 그런 다음은 Microsoft DFP API로 보냅니다.
| 4. | Microsoft DFP API는 정보를 사용하고 처리한 후 중간 계층 API에 결과를 반환합니다.
| 5. | 중간 계층 API는 정보를 처리하고 Azure AD B2C에 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시되면 사용자에게 오류 메시지가 표시됩니다. 성공 응답이 표시되면 사용자가 인증되고 디렉터리에 기록됩니다.

## <a name="set-up-the-solution"></a>솔루션 설정

1. Azure AD B2C 페더레이션을 허용하도록 구성된 [Facebook 애플리케이션을 만듭니다](./identity-provider-facebook.md#create-a-facebook-application).
2. 만든 [Facebook 비밀](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key)을 Identity Experience Framework 정책 키로 추가합니다.

## <a name="configure-your-application-under-microsoft-dfp"></a>Microsoft DFP에서 애플리케이션 구성

Microsoft DFP를 사용하도록 [Azure AD 테넌트를 설정](/dynamics365/fraud-protection/integrate-real-time-api)합니다.

## <a name="deploy-to-the-web-application"></a>웹 애플리케이션에 배포

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Microsoft DFP 서비스 핑거프린팅 구현

[Microsoft DFP 디바이스 핑거프린팅](/dynamics365/fraud-protection/device-fingerprinting)은 Microsoft DFP 계정 보호를 위한 요구 사항입니다.

>[!NOTE]
>Azure AD B2C UI 페이지 외에도 사용자는 보다 포괄적인 디바이스 프로파일링을 위해 앱 코드 내부에서 핑거프린팅 서비스를 구현할 수 있습니다. 이 샘플에 앱 코드의 핑거프린팅 서비스는 포함되어 있지 않습니다.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Azure AD B2C API 코드 배포

제공된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API)를 Azure 서비스에 배포합니다. 코드는 [Visual Studio에서 게시](/visualstudio/deployment/quickstart-deploy-to-azure)할 수 있습니다.

CORS를 설정, **허용된 원본** 추가 `https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>필요한 설정을 사용하여 Azure AD를 구성하려면 나중에 배포된 서비스의 URL이 필요합니다.

자세히 알아보려면 [App Service 설명서](../app-service/app-service-web-tutorial-rest-api.md)를 참조하세요.

### <a name="add-context-dependent-configuration-settings"></a>컨텍스트에 따라 다른 구성 설정

[Azure의 App Service](../app-service/configure-common.md#configure-app-settings)에서 애플리케이션 설정을 구성합니다. 이렇게 하면 설정을 리포지토리로 체크인하지 않고도 안전하게 구성할 수 있습니다. Rest API에는 다음 설정이 제공되어야 합니다.

| 애플리케이션 설정 | 원본 | 참고 |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Microsoft DFP 구성 |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Microsoft 디바이스 핑거프린팅 고객 ID |     |
| FraudProtectionSettings:ApiBaseUrl |  Microsoft DFP 포털의 기본 URL   | '-int '를 제거하여 프로덕션 API를 대신 호출|
|  TokenProviderConfig: 리소스  | 기본 URL - https://api.dfp.dynamics-int.com     | '-int '를 제거하여 프로덕션 API를 대신 호출|
|   TokenProviderConfig:ClientId       |Fraud Protection 판매자 Azure AD 클라이언트 앱 ID      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | Fraud Protection 판매자 Azure AD 테넌트 기관 |
| TokenProviderConfig:CertificateThumbprint* | 판매자 Azure AD 클라이언트 앱에 대해 인증하는 데 사용할 인증서의 지문입니다. |
| TokenProviderConfig:ClientSecret* | 판매자 Azure AD 클라이언트 앱에 대한 비밀 | 비밀 관리자를 사용하는 것이 좋습니다. |

*인증 방법(인증서 또는 암호와 같은 비밀)에 따라 표시되는 매개 변수 2개 중 하나만 설정합니다.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C 구성 업데이트

### <a name="replace-the-configuration-values"></a>구성 값 바꾸기

제공된 [사용자 지정 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)에서 다음 자리 표시자를 찾아 인스턴스의 해당 값으로 바꿉니다.

| 자리표시자 | 다음 항목으로 교체 | 참고 |
| :-------- | :------------| :-----------|
|{your_tenant_name} | 테넌트 약식 이름 |  yourtenant.onmicrosoft.com의 "yourtenant"   |
|{your_tenantId} | Azure AD B2C 테넌트의 테넌트 ID |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Azure AD B2C 테넌트에 구성된 IdentityExperienceFramework 앱의 앱 ID    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Azure AD B2C 테넌트에 구성된 ProxyIdentityExperienceFramework 앱의 앱 ID      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  테넌트 스토리지 애플리케이션의 앱 ID   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | 테넌트 스토리지 애플리케이션의 개체 ID    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   App Insights 인스턴스의 계측 키*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | UI 파일이 제공되는 앱 서비스의 엔드포인트    | `https://yourapp.azurewebsites.net/B2CUI/GetUIPage`   |
|   {your_app_service_url}  | 앱 서비스의 URL    |  `https://yourapp.azurewebsites.net`  |
|   {your-facebook-app-id}  |  Azure AD B2C와 페더레이션을 위해 구성된 Facebook 앱의 앱 ID   | 000000000000000   |
|  {your-facebook-app-secret}   |  Facebook의 앱 비밀을 저장한 정책 키의 이름   | B2C_1A_FacebookAppSecret   |

*App Insights는 다른 테넌트에 있을 수 있습니다. 이 단계는 선택 사항입니다. 필요하지 않은 경우 해당 TechnicalProfiles 및 OrechestrationSteps를 제거합니다.

### <a name="call-microsoft-dfp-label-api"></a>Microsoft DFP레이블 API 호출

고객은 [레이블 API를 구현](/dynamics365/fraud-protection/integrate-ap-api)해야 합니다. 자세히 알아보려면 [Microsoft DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0)를 참조하세요.

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

UserID의 값은 해당하는 Azure AD B2C 구성 값(ObjectID)의 값과 동일해야 합니다.

>[!NOTE]
>특성 컬렉션 페이지에 동의 알림을 추가합니다. 계정 보호를 위해 사용자의 원격 분석 및 사용자 ID 정보가 기록됨을 알립니다.

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C 정책 구성

1. 정책 폴더의 [Azure AD B2C 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)으로 이동합니다.

2. 이 [문서](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack)에 따라 [LocalAccounts 스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 다운로드합니다.

3. Azure AD B2C 테넌트에 대한 정책을 구성합니다.

>[!NOTE]
>특정 테넌트와 관련하여 제공되는 정책을 업데이트합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 정책에서 **Identity Experience Framework** 를 선택합니다.

2. 이전에 만든 **SignUpSignIn** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   a. **애플리케이션**: 등록된 앱을 선택합니다(JWT는 샘플).

   b. **회신 URL**: **리디렉션 URL** 을 선택합니다.

   c. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 따라 계정 만들기

5. 사용자 특성을 만든 후 흐름을 따라 진행하는 동안 Microsoft DFP 서비스가 호출됩니다. 흐름이 불완전한 경우 사용자가 디렉터리에 저장되지 않았는지 확인합니다.

>[!NOTE]
>[Microsoft DFP 규칙 엔진](/dynamics365/fraud-protection/rules)을 사용하는 경우 Microsoft DFP 포털에서 규칙을 직접 업데이트합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Microsoft DFP 샘플](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
