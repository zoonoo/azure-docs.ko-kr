---
title: Microsoft Dynamics 365 사기 보호를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 위험한 및 사기성 계정을 식별 하기 위해 Microsoft Dynamics 365 사기 보호를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 24fe28eafc1d1da90b6a7b7f9d41b7e645e62855
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625775"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C을 사용 하 여 Microsoft Dynamics 365 사기 방지 구성

이 샘플 자습서에서는 Azure Active Directory (AD) B2C를 사용 하 여 [Microsoft Dynamics 365 사기 행위 보호](https://docs.microsoft.com/dynamics365/fraud-protection/overview) 를 통합 하는 방법에 대 한 지침을 제공 합니다.

Microsoft db는 클라이언트에 게 새 계정을 만들도록 시도 하는 위험과 클라이언트 에코 시스템에 대 한 로그인 시도가 사기성 인지 여부를 평가 하는 기능을 제공 합니다. 고객이 Microsoft EFP 평가를 사용 하 여 의심 스러운 시도를 차단 하거나 시도 하 여 새 가짜 계정을 만들거나 기존 계정을 손상 시킬 수 있습니다. 계정 보호에는 인공 지능 임 장치 지문 인식을, 실시간 위험 평가를 위한 Api, 클라이언트의 비즈니스 요구에 따라 위험 전략을 최적화 하는 규칙 및 목록 환경, 클라이언트 에코 시스템의 사기 행위 보호 효율성 및 추세를 모니터링 하는 성과 기록표가 포함 됩니다.

이 샘플에서는 Microsoft BFP의 계정 보호 기능을 Azure AD B2C 사용자 흐름과 통합 합니다. 서비스는 모든 로그인 또는 등록 시도에 대해 외부에서 지문을 제공 하거나 과거 또는 의심 스러운 동작을 시청 합니다. Azure AD B2C는 사용자의 모든 과거 및 현재 동작을 기반으로 결과를 반환 하 고, Microsoft efp 서비스 내에 지정 된 사용자 지정 규칙을 사용 하 여 Microsoft EFP에서 결정 끝점을 호출 합니다. 이 결과에 따라 승인 결정을 Azure AD B2C 하 고 Microsoft EFP와 동일 하 게 다시 전달 합니다.

## <a name="prerequisites"></a>전제 조건

시작 하려면 다음이 필요 합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테 넌 트](./tutorial-create-tenant.md)입니다. 테 넌 트는 Azure 구독에 연결 됩니다.

- Microsoft EFP [구독](https://dynamics.microsoft.com/pricing/#Sales)을 가져옵니다. [평가판 클라이언트 버전](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) 도 설정할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

Microsoft EFP 통합에는 다음 구성 요소가 포함 됩니다.

- **Azure AD B2C 테 넌 트**: 사용자를 인증 하 고 MICROSOFT efp의 클라이언트 역할을 합니다. 대상 정책을 실행 하는 모든 사용자의 식별 및 진단 데이터를 수집 하는 지문 인식을 스크립트를 호스팅합니다. Microsoft EFP에서 의심 스러운 것을 발견 하는 경우 나중에 차단 하거나 로그인 하거나 등록을 시도 합니다.

- **사용자 지정 app service**: 두 가지 용도로 사용 되는 웹 응용 프로그램입니다.

  - HTML 페이지를 Id 경험 프레임 워크의 UI로 사용할 수 있도록 합니다. Microsoft Dynamics 365 지문 인식을 스크립트를 포함 해야 합니다.

  - Azure AD B2C에 Microsoft EFP를 연결 하는 RESTful 끝점이 포함 된 API 컨트롤러입니다. 핸들의 데이터 처리, 구조 및의 보안 요구 사항을 준수 합니다.

- **Microsoft EFP 지문 인식을 service**: 동적으로 포함 된 스크립트는 장치 원격 분석 및 자체 어설션된 사용자 세부 정보를 기록 하 여 사용자가 의사 결정 과정에서 나중에 사용할 수 있도록 고유 하 게 식별할 수 있는 지문을 만듭니다.

- **Microsoft EFP API 끝점**: 의사 결정 결과를 제공 하 고 클라이언트 응용 프로그램에서 수행 하는 작업을 반영 하는 최종 상태를 수락 합니다. Azure AD B2C은 다양 한 보안 및 API 페이로드 요구 사항으로 인해 끝점과 직접 통신 하지 않으며 대신 app service를 중간으로 사용 합니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![Microsoft dynamics365 사기 방지 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 도착 합니다. 사용자는 등록을 선택 하 여 새 계정을 만들고 페이지에 정보를 입력 합니다. Azure AD B2C 사용자 특성을 수집 합니다.
| 2. | Azure AD B2C 중간 계층 API를 호출 하 고 사용자 특성을 전달 합니다.
| 3. | 중간 계층 API는 사용자 특성을 수집 하 고 Microsoft EFP API에서 사용할 수 있는 형식으로 변환 합니다. 그런 다음은 Microsoft EFP API로 보냅니다.
| 4. | Microsoft EFP API는 정보를 사용 하 고 처리 한 후 중간 계층 API에 결과를 반환 합니다.
| 5. | 중간 계층 API는 정보를 처리 하 고 Azure AD B2C에 관련 정보를 다시 보냅니다.
| 6. | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. 오류 응답이 표시 되 면 사용자에 게 오류 메시지가 표시 됩니다. 성공 응답이 표시 되 면 사용자가 인증 되 고 디렉터리에 기록 됩니다.

## <a name="set-up-the-solution"></a>솔루션 설정

1. 페더레이션을 Azure AD B2C 페더레이션을 허용 하도록 구성 된 [Facebook 응용 프로그램을 만듭니다](./identity-provider-facebook.md#create-a-facebook-application) .
2. 만든 [Facebook 비밀](./custom-policy-get-started.md#create-the-facebook-key) 을 Id 경험 프레임 워크 정책 키로 추가 합니다.

## <a name="configure-your-application-under-microsoft-dfp"></a>Microsoft EFP에서 응용 프로그램 구성

Microsoft EFP를 사용 하도록 [AZURE AD 테 넌 트를 설정](/dynamics365/fraud-protection/integrate-real-time-api) 합니다.

## <a name="deploy-to-the-web-application"></a>웹 응용 프로그램에 배포

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Microsoft EFP service 지문 인식을 구현

Microsoft efp [device 지문 인식을](/dynamics365/fraud-protection/device-fingerprinting) 는 MICROSOFT efp 계정 보호를 위한 요구 사항입니다.

>[!NOTE]
>사용자는 UI 페이지 Azure AD B2C 외에도 보다 포괄적인 장치 프로 파일링을 위해 앱 코드 내부에서 지문 인식을 서비스를 구현할 수 있습니다. 앱 코드의 지문 인식을 서비스는이 샘플에 포함 되어 있지 않습니다.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Azure AD B2C API 코드 배포

제공 된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) 를 Azure 서비스에 배포 합니다. [Visual Studio에서](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)코드를 게시할 수 있습니다.

CORS 설정, **허용 된 원본** 추가 `https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>필요한 설정을 사용 하 여 Azure AD를 구성 하려면 나중에 배포 된 서비스의 URL이 필요 합니다.

자세히 알아보려면 [App service 설명서](../app-service/app-service-web-tutorial-rest-api.md) 를 참조 하세요.

### <a name="add-context-dependent-configuration-settings"></a>컨텍스트 종속 구성 설정 추가

[Azure의 App service](../app-service/configure-common.md#configure-app-settings)에서 응용 프로그램 설정을 구성 합니다. 이렇게 하면 설정을 리포지토리로 체크 인하지 않고도 안전 하 게 구성할 수 있습니다. Rest API에는 다음 설정이 제공 되어야 합니다.

| 애플리케이션 설정 | 원본 | 참고 |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Microsoft EFP 구성 |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Microsoft device 지문 인식을 고객 ID |     |
| FraudProtectionSettings:ApiBaseUrl |  Microsoft LFP 포털의 기본 URL   | 대신 '-int '를 제거 하 여 프로덕션 API를 호출 하십시오.|
|  TokenProviderConfig: 리소스  | 기본 URL- https://api.dfp.dynamics-int.com     | 대신 '-int '를 제거 하 여 프로덕션 API를 호출 하십시오.|
|   TokenProviderConfig: ClientId       |사기 행위 보호 판매자 Azure AD 클라이언트 앱 ID      |       |
| TokenProviderConfig: Authority | https://login.microsoftonline.com/<directory_ID> | 사기 행위 보호 판매자 Azure AD 테 넌 트 기관 |
| TokenProviderConfig: CertificateThumbprint * | 판매자 Azure AD 클라이언트 앱에 대해 인증 하는 데 사용할 인증서의 지문입니다. |
| TokenProviderConfig: ClientSecret * | 판매자 Azure AD 클라이언트 앱에 대 한 비밀 | 비밀 관리자를 사용 하는 것이 좋습니다. |

* 인증서 또는 암호와 같은 암호를 사용 하 여 인증 하는 경우에 따라 표시 되는 매개 변수 2 개 중 하나만 설정 합니다.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C 구성

### <a name="replace-the-configuration-values"></a>구성 값 바꾸기

제공 된 [사용자 지정 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)에서 다음 자리 표시자를 찾아 인스턴스의 해당 값으로 바꿉니다.

| 자리표시자 | 다음 항목으로 교체 | 참고 |
| :-------- | :------------| :-----------|
|{your_tenant_name} | 테 넌 트 약식 이름 |  yourtenant.onmicrosoft.com의 "모든 테 넌 트"   |
|{your_tenantId} | Azure AD B2C 테 넌 트의 테 넌 트 ID |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Azure AD B2C 테 넌 트에 구성 된 IdentityExperienceFramework 앱의 앱 ID    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Azure AD B2C 테 넌 트에 구성 된 ProxyIdentityExperienceFramework 앱의 앱 ID      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  테 넌 트 저장소 응용 프로그램의 앱 ID   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | 테 넌 트 저장소 응용 프로그램의 개체 ID    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   App insights 인스턴스의 계측 키 *  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | UI 파일이 제공 되는 앱 서비스의 끝점    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | 앱 서비스의 URL    |  https://yourapp.azurewebsites.net  |
|   {사용자의 facebook-앱 id}  |  Azure AD B2C와 페더레이션 하도록 구성 된 facebook 앱의 앱 ID   | 000000000000000   |
|  {facebook-앱 비밀}   |  Facebook의 앱 비밀을 저장 한 정책 키의 이름   | B2C_1A_FacebookAppSecret   |

* App insights는 다른 테 넌 트에 있을 수 있습니다. 이 단계는 선택 사항입니다. 필요 하지 않은 경우 해당 TechnicalProfiles 및 OrechestrationSteps를 제거 합니다.

### <a name="call-microsoft-dfp-label-api"></a>Microsoft DFP label API 호출

고객은 [LABEL API를 구현](/dynamics365/fraud-protection/integrate-ap-api)해야 합니다. 자세히 알아보려면 [Microsoft EFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) 를 참조 하세요.

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

UserID의 값은 해당 하는 Azure AD B2C 구성 값 (ObjectID)의 값과 동일 해야 합니다.

>[!NOTE]
>특성 컬렉션 페이지에 동의 알림을 추가 합니다. 계정 보호를 위해 사용자의 원격 분석 및 사용자 id 정보가 기록 됨을 알립니다.

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C 정책 구성

1. 정책 폴더의 [Azure AD B2C 정책](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) 으로 이동 합니다.

2. 이 [문서](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) 에 따라 [localaccounts 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) 을 다운로드 합니다.

3. Azure AD B2C 테 넌 트에 대 한 정책을 구성 합니다.

>[!NOTE]
>특정 테 넌 트와 관련 하 여 제공 되는 정책을 업데이트 합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 정책에서 **Id 경험 프레임 워크** 를 선택 합니다.

2. 이전에 만든 **Signupsignin** 을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 설정을 선택 합니다.

   a. **응용 프로그램**: 등록 된 앱을 선택 합니다 (JWT는 샘플).

   b. **회신 url**: **리디렉션 url** 을 선택 합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름으로 이동 하 여 계정 만들기

5. 사용자 특성을 만든 후 흐름을 진행 하는 동안 Microsoft EFP 서비스가 호출 됩니다. 흐름이 불완전 한 경우 사용자가 디렉터리에 저장 되지 않았는지 확인 합니다.

>[!NOTE]
>Microsoft efp [규칙 엔진](/dynamics365/fraud-protection/rules)을 사용 하는 경우 MICROSOFT Efp 포털에서 규칙을 직접 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Microsoft EFP 샘플](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C에서 사용자 지정 정책 시작](./custom-policy-get-started.md?tabs=applications)
