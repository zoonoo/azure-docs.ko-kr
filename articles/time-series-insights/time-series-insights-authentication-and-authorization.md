---
title: API 인증 및 권한 부여 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에 대한 인증 및 권한 부여를 구성하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: beefad41a270233336bb9134268c98341e81a7cd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380800"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API에 대한 인증 및 권한 부여

이 문서에서는 새 Azure Active Directory 블레이드를 사용하여 Azure Active Directory에 앱을 등록하는 방법을 설명합니다. Azure Active Directory에 등록된 앱을 사용하면 사용자가 시계열 인사이트 환경과 연결된 Azure 시계열 인사이트 API를 인증하고 사용할 수 있습니다.

> [!IMPORTANT]
> Azure Time Series Insights는 다음 인증 라이브러리를 모두 지원합니다.
> * 최신 [Microsoft 인증 라이브러리(MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure 활성 디렉터리 인증 라이브러리(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>서비스 주체

다음 섹션에서는 앱을 대신하여 타임시리즈 인사이트 API에 액세스하도록 응용 프로그램을 구성하는 방법을 설명합니다. 그런 다음 응용 프로그램은 Azure Active Directory를 통해 자체 응용 프로그램 자격 증명을 사용하여 Time Series Insights 환경에서 참조 데이터를 쿼리하거나 게시할 수 있습니다.

## <a name="summary-and-best-practices"></a>요약 및 모범 사례

Azure Active Directory 앱 등록 흐름에는 세 가지 주요 단계가 포함됩니다.

1. Azure Active 디렉터리에서 [응용 프로그램을 등록합니다.](#azure-active-directory-app-registration)
1. 응용 프로그램에서 [타임시리즈 인사이트 환경에 대한 데이터 액세스](#granting-data-access)권한을 부여합니다.
1. 응용 **프로그램 ID** 및 **클라이언트 비밀을** `https://api.timeseries.azure.com/` 사용하여 [클라이언트 앱에서](#client-app-initialization)토큰을 획득합니다. 그런 다음 토큰을 사용하여 Time Series Insights API를 호출할 수 있습니다.

**3단계별로**응용 프로그램과 사용자 자격 증명을 분리하면 다음을 수행할 수 있습니다.

* 사용자 고유의 권한과 구별되는 앱 ID에 권한을 할당합니다. 일반적으로 이러한 권한은 앱 실행에 필요한 것으로만 제한됩니다. 예를 들어 앱이 특정 타임시리즈 인사이트 환경에서만 데이터를 읽도록 허용할 수 있습니다.
* **클라이언트 보안** 또는 보안 인증서를 사용하여 사용자의 인증 자격 증명을 만드는 데 앱의 보안을 격리합니다. 따라서 응용 프로그램의 자격 증명은 특정 사용자의 자격 증명에 종속되지 않습니다. 사용자의 역할이 변경되는 경우 응용 프로그램에 새 자격 증명이나 추가 구성이 반드시 필요한 것은 아닙니다. 사용자가 암호를 변경하면 응용 프로그램에 대한 모든 액세스에는 새 자격 증명이나 키가 필요하지 않습니다.
* 특정 사용자의 자격 증명이 아닌 **클라이언트 보안** 또는 보안 인증서를 사용하여 무인 스크립트를 실행합니다(존재하도록 요구).
* 암호 대신 보안 인증서를 사용하여 Azure Time Series Insights API에 대한 액세스를 보호합니다.

> [!IMPORTANT]
> Azure Time Series Insights 보안 정책을 구성할 때 **우려 사항 분리** 원칙(위에서 설명한 이 시나리오에 설명)을 따릅니다.

> [!NOTE]
> * 이 문서에서는 응용 프로그램이 하나의 조직에서만 실행되도록 하는 단일 테넌트 응용 프로그램에 중점을 둡니다.
> * 일반적으로 조직에서 실행되는 한 정중한 업무 응용 프로그램에 단일 테넌트 응용 프로그램을 사용합니다.

## <a name="detailed-setup"></a>자세한 설정

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>데이터 액세스 권한 부여

1. [열계 계열 인사이트] 환경의 **경우 데이터 액세스 정책을** 선택하고 추가 **를**선택합니다.

   [![Time Series Insights 환경에 새 데이터 액세스 정책 추가](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 사용자 **선택** 대화 상자에서 Azure Active Directory 앱 등록 섹션에서 **응용 프로그램 이름** 또는 응용 프로그램 **ID를** 붙여넣습니다.

   [![사용자 선택 대화 상자에서 애플리케이션 찾기](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 역할을 선택합니다. 데이터를 쿼리하려면 **Reader를** 선택하거나 **데이터를** 쿼리하고 참조 데이터를 변경합니다. **확인**을 선택합니다.

   [![선택 사용자 역할 대화 상자에서 리더 또는 기여자 선택](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. **확인을**선택하여 정책을 저장합니다.

   > [!TIP]
   > 고급 데이터 액세스 옵션의 경우 [데이터 액세스 권한 부여 를](./time-series-insights-data-access.md)읽어보십시오.

### <a name="client-app-initialization"></a>클라이언트 앱 초기화

* 개발자는 [MSAL(Microsoft 인증 라이브러리)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) 또는 [Azure Active Directory 인증 라이브러리(ADAL)를](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) 사용하여 Azure Time Series Insights를 사용하여 인증할 수 있습니다.

* 예를 들어 ADAL을 사용하여 인증하려면 다음을 수행합니다.

   1. Azure Active Directory 앱 등록 섹션에서 **응용 프로그램 ID** 및 클라이언트 **보안(응용** 프로그램 키)을 사용하여 응용 프로그램을 대신하여 토큰을 획득합니다.

   1. C#에서 다음 코드는 응용 프로그램을 대신하여 토큰을 획득할 수 있습니다. 전체 샘플에 대 한 [C#](time-series-insights-query-data-csharp.md)을 사용 하 여 쿼리 데이터를 읽으십시오.

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. 애플리케이션이 Time Series Insights API를 호출할 때 `Authorization` 헤더에서 이 토큰을 전달할 수 있습니다.

* 또는 개발자는 MSAL을 사용하여 인증하도록 선택할 수 있습니다. [MSAL로 마이그레이션에](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) 대해 알아보고 [C#](time-series-insights-manage-reference-data-csharp.md) 문서를 사용하여 Azure Time Series Insights 환경에 대한 GA 참조 데이터 관리를 참조하세요. 

## <a name="common-headers-and-parameters"></a>일반 헤더 및 매개 변수

이 섹션에서는 타임시리즈 인사이트 GA 및 미리 보기 API에 대해 쿼리를 만드는 데 사용되는 일반적인 HTTP 요청 헤더 및 매개 변수에 대해 설명합니다. API 관련 요구 사항은 [타임시리즈 인사이트 REST API 참조 설명서에서](https://docs.microsoft.com/rest/api/time-series-insights/)자세히 다룹니다.

> [!TIP]
> REST [API를](https://docs.microsoft.com/rest/api/azure/) 사용하고, HTTP 요청을 하고, HTTP 응답을 처리하는 방법에 대해 자세히 알아보려면 Azure REST API 참조를 읽어보십시오.

### <a name="authentication"></a>인증

[타임 시리즈 인사이트 REST API에](https://docs.microsoft.com/rest/api/time-series-insights/)대해 인증된 쿼리를 수행하려면 유효한 OAuth 2.0 베어러 토큰이 원하는 REST 클라이언트(Postman, JavaScript, C#)를 사용하여 [권한 부여 헤더에](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) 전달되어야 합니다. 

> [!TIP]
> 호스팅된 Azure Time Series Insights [클라이언트 SDK 샘플 시각화를](https://tsiclientsample.azurewebsites.net/) 읽고 차트 및 그래프와 함께 [JavaScript 클라이언트 SDK를](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 프로그래밍 방식으로 사용하여 프로그래밍 방식으로 타임시리즈 인사이트 API로 인증하는 방법을 알아봅니다.

### <a name="http-headers"></a>HTTP 헤더

필수 요청 헤더는 아래에 설명되어 있습니다.

| 필수 요청 헤더 | 설명 |
| --- | --- |
| 권한 부여 | 타임 시리즈 인사이트를 사용하여 인증하려면 유효한 OAuth 2.0 베어러 토큰을 **권한 부여** 헤더에 전달해야 합니다. | 

> [!IMPORTANT]
> 토큰은 리소스에 `https://api.timeseries.azure.com/` 정확히 발급되어야 합니다(토큰의 "대상"이라고도 함).
> * 따라서 [우체부](https://www.getpostman.com/) **AuthURL은** 다음과 같은 것입니다.`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`유효하지만 `https://api.timeseries.azure.com` 유효하지 않습니다.

선택적 요청 헤더는 아래에 설명되어 있습니다.

| 선택적 요청 헤더입니다. | 설명 |
| --- | --- |
| Content-type | 만 `application/json` 지원됩니다. |
| x-ms-client-request-id | 클라이언트 요청 ID입니다. 서비스는 이 값을 기록합니다. 서비스가 서비스 간에 작업을 추적할 수 있도록 합니다. |
| x-ms-클라이언트 세션 ID | 클라이언트 세션 ID입니다. 서비스는 이 값을 기록합니다. 서비스가 서비스 전체에서 관련 작업 그룹을 추적할 수 있도록 합니다. |
| x-ms-클라이언트-응용 프로그램 이름 | 이 요청을 생성한 응용 프로그램의 이름입니다. 서비스는 이 값을 기록합니다. |

선택적이지만 권장되는 응답 헤더는 아래에 설명되어 있습니다.

| 응답 헤더 | 설명 |
| --- | --- |
| Content-type | `application/json`만 지원됩니다. |
| x-ms-request-id | 서버에서 생성된 요청 ID입니다. Microsoft에 문의하여 요청을 조사하는 데 사용할 수 있습니다. |
| x-ms-속성-찾을 수 없는 동작 | GA API 선택적 응답 헤더입니다. 가능한 값은 `ThrowError` (기본값) 또는 `UseNull`. |

### <a name="http-parameters"></a>HTTP 매개 변수

> [!TIP]
> [참조 설명서에서](https://docs.microsoft.com/rest/api/time-series-insights/)필수 및 선택적 쿼리 정보에 대한 자세한 내용을 찾아보십시오.

필수 URL 쿼리 문자열 매개 변수는 API 버전에 따라 다릅니다.

| 해제 | 가능한 API 버전 값 |
| --- |  --- |
| 일반 공급 | `api-version=2016-12-12`|
| 미리 보기 | `api-version=2018-11-01-preview` |
| 미리 보기 | `api-version=2018-08-15-preview` |

선택적 URL 쿼리 문자열 매개 변수에는 HTTP 요청 실행 시간에 대한 시간 설정이 포함됩니다.

| 선택적 쿼리 매개 변수 | 설명 | 버전 |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 요청 실행을 위한 서버 측 시간 입니다. 환경 가져오기 [이벤트](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) 및 [환경 집계](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) API에만 적용됩니다. 시간 지정 값은 ISO 8601 기간 `"PT20S"` 형식이어야 하며 범위에 `1-30 s`있어야 합니다. 기본값은 `30 s`여야 합니다. | GA |
| `storeType=<storeType>` | 웜 저장소가 활성화된 미리 보기 환경의 경우 `WarmStore` `ColdStore`또는 에서 쿼리를 실행할 수 있습니다. 쿼리의 이 매개 변수는 쿼리를 실행할 저장소를 정의합니다. 정의되지 않은 경우 콜드 스토어에서 쿼리가 실행됩니다. 웜 저장소를 쿼리하려면 **storeType을** 로 `WarmStore`설정해야 합니다. 정의되지 않은 경우 콜드 스토어에 대해 쿼리가 실행됩니다. | 미리 보기 |

## <a name="next-steps"></a>다음 단계

- GA 타임 시리즈 인사이트 API를 호출하는 샘플 코드의 경우 [C#을 사용하여 쿼리 데이터를](./time-series-insights-query-data-csharp.md)읽습니다.

- 미리 보기 타임 시리즈 Insights API 코드 샘플의 경우 [C#을 사용하여 쿼리 미리 보기 데이터를](./time-series-insights-update-query-data-csharp.md)읽습니다.

- API 참조 정보는 [쿼리 API 참조](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) 설명서를 참조하십시오.

- [서비스 주체를 만드는](../active-directory/develop/howto-create-service-principal-portal.md)방법에 대해 알아봅니다.
