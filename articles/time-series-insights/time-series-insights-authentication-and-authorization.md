---
title: API 인증 및 권한 부여 - Azure Time Series Insights | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에 대한 인증 및 권한 부여를 구성하는 방법을 설명합니다.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 88fd575d40cc31f12f052158bda0aed9a5335555
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009269"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API에 대한 인증 및 권한 부여

비즈니스 요구 사항에 따라 솔루션은 Azure Time Series Insights 환경의 [api](/rest/api/time-series-insights/reference-data-access-overview)와 상호 작용 하는 데 사용 하는 클라이언트 응용 프로그램을 하나 이상 포함할 수 있습니다. Azure Time Series Insights는 [OAUTH 2.0을 기반으로 하는 AZURE AD 보안 토큰을](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)사용 하 여 인증을 수행 합니다. 클라이언트를 인증 하려면 올바른 권한이 있는 전달자 토큰을 가져와서 API 호출과 함께 전달 해야 합니다. 이 문서에서는 관리 되는 id 및 Azure Active Directory 앱 등록을 사용 하는 것을 포함 하 여 전달자 토큰을 가져오고 인증 하는 데 사용할 수 있는 자격 증명을 가져오는 몇 가지 방법을 설명 합니다.

## <a name="managed-identities"></a>관리 ID

다음 섹션에서는 Azure Active Directory (Azure AD)에서 관리 되는 id를 사용 하 여 Azure Time Series Insights API에 액세스 하는 방법을 설명 합니다. Azure에서 관리 ID를 사용하면 개발자가 Azure AD에서 Azure 리소스에 대한 ID를 제공하고 이를 사용하여 Azure AD(Azure Active Directory) 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다. 관리 ID를 사용하는 경우 얻을 수 있는 몇 가지 혜택은 다음과 같습니다.

- 자격 증명을 관리할 필요가 없으며, 자격 증명에 액세스할 수도 없습니다.
- 관리 ID를 사용하여 Azure Key Vault를 포함한 Azure AD 인증을 지원하는 모든 Azure 서비스에 인증할 수 있습니다.
- 관리 ID는 추가 비용 없이 사용할 수 있습니다.

관리 되는 id의 두 가지 유형에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../active-directory/managed-identities-azure-resources/overview.md) 을 참조 하세요.

에서 관리 되는 id를 사용할 수 있습니다.

- Azure VM
- Azure App Services
- Azure Functions
- Azure Container instances
- 기타 ...

전체 목록은 [azure 리소스에 대 한 관리 되는 id를 지 원하는 azure 서비스를](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) 참조 하세요.

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록

자격 증명을 관리할 필요가 없도록 가능한 경우 관리 되는 id를 사용 하는 것이 좋습니다. 클라이언트 응용 프로그램이 관리 되는 id를 지 원하는 Azure 서비스에서 호스팅되지 않는 경우 Azure AD 테 넌 트를 사용 하 여 응용 프로그램을 등록할 수 있습니다. Azure AD에 응용 프로그램을 등록 하면 Azure AD와 통합 될 수 있도록 응용 프로그램에 대 한 id 구성이 생성 됩니다. [Azure Portal](https://portal.azure.com/)에 앱을 등록 하는 경우이는 단일 테 넌 트 (테 넌 트 에서만 액세스할 수 있음) 또는 다중 테 넌 트 (다른 테 넌 트에서 액세스할 수 있음) 인지 여부를 선택 하 고 필요에 따라 액세스 토큰이 전송 되는 리디렉션 URI를 설정할 수 있습니다.

앱 등록을 완료 하면 홈 테 넌 트 또는 디렉터리 내에 상주 하는 앱의 전역적으로 고유한 인스턴스 (응용 프로그램 개체)가 있습니다. 앱에 대 한 전역적으로 고유한 ID (앱 또는 클라이언트 ID)도 있습니다. 그런 다음 포털에서 비밀 또는 인증서 및 범위를 추가 하 여 앱이 작동 하도록 하 고 로그인 대화 상자에서 앱의 브랜딩을 사용자 지정할 수 있습니다.

포털에서 응용 프로그램을 등록 하는 경우 응용 프로그램 개체 및 서비스 주체 개체는 홈 테 넌 트에 자동으로 만들어집니다. Microsoft Graph Api를 사용 하 여 응용 프로그램을 등록/만들 경우에는 별도의 단계를 통해 서비스 주체 개체를 만들 수 있습니다. 토큰을 요청 하려면 서비스 사용자 개체가 필요 합니다.

응용 프로그램에 대 한 [보안](../active-directory/develop/identity-platform-integration-checklist.md#security) 검사 목록을 검토 해야 합니다. 모범 사례로, 암호 자격 증명 (클라이언트 암호)이 아닌 [인증서 자격 증명](../active-directory/develop/active-directory-certificate-credentials.md)을 사용 해야 합니다.

자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md) 를 참조 하세요.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>1 단계: 관리 id 또는 앱 등록 만들기

관리 id 또는 앱 등록을 사용할지 여부를 확인 한 후에는 다음 단계를 프로 비전 해야 합니다.

### <a name="managed-identity"></a>관리 ID

관리 id를 만드는 데 사용 하는 단계는 코드의 위치 및 시스템 할당 또는 사용자 할당 id를 만드는 지 여부에 따라 달라 집니다. 차이점을 이해 하려면 [관리 되는 id 형식을](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 읽습니다. Id 유형을 선택한 후에는 Azure AD 관리 id [설명서](../active-directory/managed-identities-azure-resources/index.yml)에서 올바른 자습서를 찾아 따르십시오. 다음에 대 한 관리 id를 구성 하는 방법에 대 한 지침을 찾을 수 있습니다.

- [Azure VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service 및 Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- 기타 ...

### <a name="application-registration"></a>애플리케이션 등록

[응용 프로그램 등록](../active-directory/develop/quickstart-register-app.md#register-an-application)에 나열 된 단계를 따릅니다.

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>2 단계: 액세스 권한 부여

Azure Time Series Insights 환경에서 요청을 받으면 먼저 호출자의 전달자 토큰에 대 한 유효성을 검사 합니다. 유효성 검사에 통과 하면 호출자가 인증 된 후 호출자에 게 요청 된 작업을 수행할 수 있는 권한이 있는지 확인 하는 검사가 수행 됩니다. 사용자 또는 서비스 사용자에 게 권한을 부여 하려면 먼저 해당 사용자 또는 서비스 사용자에 게 독자 또는 참가자 역할을 할당 하 여 환경에 대 한 액세스 권한을 부여 해야 합니다.

- [Azure Portal](https://portal.azure.com/) UI를 통해 액세스 권한을 부여 하려면 [환경에 데이터 액세스 권한 부여](concepts-access-policies.md) 문서에 나와 있는 지침을 따르세요. 사용자를 선택할 때 이름 또는 ID로 관리 되는 id 또는 앱 등록을 검색할 수 있습니다.

- Azure CLI를 사용 하 여 액세스 권한을 부여 하려면 다음 명령을 실행 합니다. [여기](/cli/azure/ext/timeseriesinsights/tsi/access-policy) 에서 설명서를 검토 하 여 액세스를 관리 하는 데 사용할 수 있는 명령의 전체 목록을 확인 하세요.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Azure CLI에 대 한 timeseriesinsights-environment-with-eventhub 확장에는 버전 2.11.0 이상이 필요 합니다. 확장은 az tsi 액세스 정책 명령을 처음 실행할 때 자동으로 설치 됩니다. 확장에 대해 [자세히 알아보세요](/cli/azure/azure-cli-extensions-overview) .

## <a name="step-3-requesting-tokens"></a>3 단계: 토큰 요청

관리 id 또는 앱 등록을 프로 비전 하 고 역할을 할당 한 후에는이를 사용 하 여 OAuth 2.0 전달자 토큰을 요청할 준비가 된 것입니다. 토큰을 가져오는 데 사용 하는 방법은 코드가 호스팅되는 위치 및 선택한 언어에 따라 달라 집니다. 리소스 (토큰의 "대상"이 라고도 함)를 지정할 때 URL 또는 GUID로 Azure Time Series Insights를 식별할 수 있습니다.

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> URL을 리소스 ID로 사용 하는 경우 토큰을 정확히에 대해 발급 해야 합니다 `https://api.timeseries.azure.com/` . 후행 슬래시가 필요합니다.

> * [Postman](https://www.getpostman.com/) 을 사용 하는 경우 **authurl** 은 다음과 같습니다.`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/`은 유효하지만 `https://api.timeseries.azure.com`은 유효하지 않습니다.

### <a name="managed-identities"></a>관리 ID

Azure App Service 또는 함수에서 액세스 하는 경우 [Azure 리소스에 대 한 토큰 가져오기](../app-service/overview-managed-identity.md)의 지침을 따릅니다.

.NET 응용 프로그램 및 함수의 경우 관리 되는 id를 사용 하는 가장 간단한 방법은 .NET 용 [Azure id 클라이언트 라이브러리](/dotnet/api/overview/azure/identity-readme) 를 사용 하는 것입니다. 이 클라이언트 라이브러리는 단순함 및 보안상의 이점 때문에 널리 사용 되 고 있습니다. 개발자는 코드를 한 번 작성 하 여 클라이언트 라이브러리가 응용 프로그램 환경을 기반으로 인증 하는 방법을 결정 하도록 할 수 있습니다. 개발자 계정을 사용 하거나 관리 서비스 id를 사용 하 여 Azure에 배포 하는 방법 선행 AppAuthentication 라이브러리의 마이그레이션 지침은 [Azure로 Appauthentication을 읽습니다. Id 마이그레이션 지침](/dotnet/api/overview/azure/app-auth-migration)을 참조 하세요.

C # 및 .NET 용 Azure Id 클라이언트 라이브러리를 사용 하 여 Azure Time Series Insights에 대 한 토큰을 요청 합니다.

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>앱 등록

* 개발자는 msal ( [Microsoft 인증 라이브러리](../active-directory/develop/msal-overview.md) )을 사용 하 여 앱 등록에 대 한 토큰을 가져올 수 있습니다.

MSAL은 다음을 비롯 한 여러 응용 프로그램 시나리오에서 사용할 수 있습니다.

* [단일 페이지 응용 프로그램 (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [사용자가 로그인하고 사용자를 대신하여 웹 API를 호출하는 웹 애플리케이션](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [로그인한 사용자를 대신하여 또 다른 다운스트림 웹 API를 호출하는 웹 API](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [로그인 한 사용자를 대신 하 여 web API를 호출 하는 데스크톱 응용 프로그램](../active-directory/develop/scenario-desktop-overview.md)
* [대화형으로 로그인 한 사용자를 대신 하 여 WEB API를 호출 하는 모바일 응용 프로그램](../active-directory/develop/scenario-mobile-overview.md)입니다.
* [자체적으로 대신 웹 API를 호출하는 데스크톱/서비스 디먼 애플리케이션](../active-directory/develop/scenario-daemon-overview.md)

Gen2 환경의 앱 등록 및 쿼리 데이터로 토큰을 획득 하는 방법을 보여 주는 샘플 c # 코드는 [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs) 에서 샘플 앱 보기

> [!IMPORTANT]
> [ADAL (Azure Active Directory Authentication Library)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) 을 사용 하는 경우 [msal으로의 마이그레이션](../active-directory/develop/msal-net-migration.md)에 대해 읽어 보십시오.

## <a name="common-headers-and-parameters"></a>일반 헤더 및 매개 변수

이 섹션에서는 Azure Time Series Insights Gen1 및 Gen2 Api에 대 한 쿼리를 수행 하는 데 사용 되는 일반적인 HTTP 요청 헤더 및 매개 변수에 대해 설명 합니다. API 관련 요구 사항은 [Azure Time Series Insights REST API 참조 설명서](/rest/api/time-series-insights/)에 자세히 설명 되어 있습니다.

> [!TIP]
> REST API를 사용하고, HTTP 요청을 수행하고, HTTP 응답을 처리하는 방법에 대해 자세히 알아보려면 [Azure REST API 참조](/rest/api/azure/)를 읽어보세요.

### <a name="http-headers"></a>HTTP 헤더

필요한 요청 헤더는 아래에 설명되어 있습니다.

| 필수 요청 헤더 | Description |
| --- | --- |
| 권한 부여 | Azure Time Series Insights 인증 하려면 유효한 OAuth 2.0 전달자 토큰을 [인증 헤더](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)에 전달 해야 합니다. |

> [!TIP]
> 호스팅된 Azure Time Series Insights [CLIENT sdk 샘플 시각화](https://tsiclientsample.azurewebsites.net/) 를 읽고 차트 및 그래프와 함께 [JavaScript 클라이언트 sdk](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 를 사용 하 여 프로그래밍 방식으로 Azure Time Series Insights api를 사용 하 여 인증 하는 방법을 알아봅니다.

선택적 요청 헤더는 아래에 설명되어 있습니다.

| 선택적 요청 헤더입니다. | Description |
| --- | --- |
| Content-type | `application/json`만 지원됩니다. |
| x-ms-client-request-id | 클라이언트 요청 ID입니다. 서비스는 이 값을 기록합니다. 이 서비스는 서비스 간 작업을 추적할 수 있습니다. |
| x-ms-client-session-id | 클라이언트 세션 ID입니다. 서비스는 이 값을 기록합니다. 이 서비스는 서비스 간 관련 작업 그룹을 추적할 수 있습니다. |
| x-ms-client-application-name | 이 요청을 생성한 애플리케이션의 이름입니다. 서비스는 이 값을 기록합니다. |

선택 사항이지만 권장되는 응답 헤더는 아래에 설명되어 있습니다.

| 응답 헤더 | Description |
| --- | --- |
| Content-type | `application/json`만 지원됩니다. |
| x-ms-request-id | 서버에서 생성된 요청 ID입니다. Microsoft에 문의하여 요청을 조사하는 데 사용할 수 있습니다. |
| x-ms-property-not-found-behavior | GA API의 선택적 응답 헤더입니다. 가능한 값은 `ThrowError`(기본값) 또는 `UseNull`입니다. |

### <a name="http-parameters"></a>HTTP 매개 변수

> [!TIP]
> [참조 설명서](/rest/api/time-series-insights/)에서 필수 및 선택적 쿼리 정보에 대한 자세한 내용을 확인하세요.

필수 URL 쿼리 문자열 매개 변수는 API 버전에 따라 달라집니다.

| 해제 | API 버전 값 |
| --- |  --- |
| 1세대 | `api-version=2016-12-12`|
| 2세대 | `api-version=2020-07-31`|

선택적 URL 쿼리 문자열 매개 변수에는 HTTP 요청 실행 시간에 대한 시간 제한을 설정하는 작업이 포함됩니다.

| 선택적 쿼리 매개 변수 | Description | 버전 |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 요청 실행을 위한 서버 쪽 시간 제한입니다. [Get Environment Events](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) 미 [Get Environment Aggregates](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API에만 적용됩니다. 제한 시간 값은 ISO 8601 기간 형식(예: `"PT20S"`)이어야 하며 `1-30 s` 범위에 있어야 합니다. 기본값은 `30 s`입니다. | 1세대 |
| `storeType=<storeType>` | 웜 저장소가 활성화 된 Gen2 환경의 경우 또는에서 쿼리를 실행할 수 있습니다 `WarmStore` `ColdStore` . 쿼리의 이 매개 변수는 쿼리를 실행해야 하는 저장소를 정의합니다. 정의되지 않은 경우 쿼리는 콜드 저장소에서 실행됩니다. 웜 저장소를 쿼리하려면 **storeType** 을 `WarmStore`로 설정해야 합니다. 정의되지 않은 경우 콜드 저장소에 대해 쿼리가 실행됩니다. | 2세대 |

## <a name="next-steps"></a>다음 단계

* Gen1 Azure Time Series Insights API를 호출 하는 샘플 코드는 [c #을 사용 하 여 Query Gen1 data](./time-series-insights-query-data-csharp.md)를 읽습니다.

* Gen2 Azure Time Series Insights API 코드 샘플을 호출 하는 샘플 코드는 [c #을 사용 하 여 Query Gen2 data](./time-series-insights-update-query-data-csharp.md)를 참조 하세요.

* API 참조 정보를 보려면 [쿼리 API 참조](/rest/api/time-series-insights/reference-query-apis) 설명서를 참조하세요.
