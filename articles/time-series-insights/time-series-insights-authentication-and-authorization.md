---
title: 인증 및 Azure Time Series Insights에서 API를 사용 하 여 권한 부여 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에 대한 인증 및 권한 부여를 구성하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543965"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API에 대한 인증 및 권한 부여

이 문서에는 새 Azure Active Directory 블레이드를 사용 하 여 Azure Active Directory에서 앱을 등록 하는 방법을 설명 합니다. Azure Active Directory에 등록 된 앱을 사용 하면 인증 하 고 Time Series Insights 환경에 연결 된 Azure Time Series Insight API를 사용 하도록 권한을 부여할 수 있도록 합니다.

## <a name="service-principal"></a>서비스 주체

다음 섹션에서는 앱을 대신 하 여 Time Series Insights API에 액세스 하려면 응용 프로그램을 구성 하는 방법에 설명 합니다. 응용 프로그램 수를 쿼리 또는 Azure Active Directory를 통해 자체 응용 프로그램 자격 증명을 사용 하 여 Time Series Insights 환경에서 참조 데이터를 게시 합니다.

## <a name="summary-and-best-practices"></a>요약 및 모범 사례

Azure Active Directory 앱 등록 흐름에는 세 가지 주요 단계가 포함 됩니다.

1. [응용 프로그램을 등록](#azure-active-directory-app-registration) Azure Active Directory에서 합니다.
1. 응용 프로그램에 권한 부여 [Time Series Insights 환경에 대 한 데이터 액세스](#granting-data-access)합니다.
1. 사용 하 여는 **응용 프로그램 ID** 및 **클라이언트 비밀** 에서 토큰을 획득 하 `https://api.timeseries.azure.com/` 에서 프로그램 [클라이언트 앱](#client-app-initialization)합니다. 그런 다음 토큰을 사용하여 Time Series Insights API를 호출할 수 있습니다.

당 **3 단계**, 수 응용 프로그램 및 사용자 자격 증명을 구분 합니다.

* 자체 사용 권한과에서 다릅니다 앱 id에 사용 권한을 할당 합니다. 일반적으로 이러한 권한은 앱 실행에 필요한 것으로만 제한됩니다. 예를 들어, 앱을 특정 Time Series Insights 환경 에서만에서 데이터를 읽을 수 있습니다.
* 만드는 사용자의 인증 자격 증명에서 앱의 보안을 사용 하 여 격리를 **클라이언트 암호** 또는 보안 인증서입니다. 결과적으로, 응용 프로그램의 자격 증명이 특정 사용자의 자격 증명에 따라 달라 집니다. 사용자의 역할이 변경 되 면 응용 프로그램 필요는 없습니다 새 자격 증명 또는 추가로 구성 합니다. 사용자가 암호를 변경 하는 경우 응용 프로그램에 대 한 모든 액세스는 새 자격 증명 또는 키 필요 하지 않습니다.
* 사용 하 여 무인 설치 스크립트를 실행 한 **클라이언트 암호** 또는 특정 사용자의 자격 증명 (있어야 하도록 요구) 하는 대신 보안 인증서입니다.
* 에 Azure Time Series Insights API에 대 한 액세스를 보호 하는 암호 보다는 보안 인증서를 사용 합니다.

> [!IMPORTANT]
> 원칙을 따릅니다 **중요 한 부분의 분리** (설명 위의이 시나리오에 대 한) 경우 보안 정책에 따라 Azure Time Series Insights를 구성 합니다.

> [!NOTE]
> * 문서는 응용 프로그램은 하나의 조직 내 에서만 실행 되도록 하는 단일 테 넌 트 응용 프로그램에 중점을 둡니다.
> * 일반적으로 조직에서 실행 되는 기간 업무 응용 프로그램에 대 한 단일 테 넌 트 응용 프로그램을 사용할 수 있습니다.

## <a name="detailed-setup"></a>자세한 설치

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>데이터 액세스를 부여합니다.

1. Time Series Insights 환경에 대해 선택 **데이터 액세스 정책** 선택한 **추가**합니다.

   [![Time Series Insights 환경에 새 데이터 액세스 정책 추가](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 에 **사용자 선택** 대화 상자에 붙여넣습니다 중 하나는 **응용 프로그램 이름** 또는 **응용 프로그램 ID** Azure Active Directory 응용 프로그램 등록 섹션에서.

   [![사용자 선택 대화 상자에서 응용 프로그램 찾기](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 역할을 선택 합니다. 선택 **판독기** 데이터를 쿼리 또는 **참가자** 데이터를 쿼리하고 참조 데이터를 변경 합니다. **확인**을 선택합니다.

   [![사용자 역할 선택 대화 상자에서 읽기 권한자 또는 참가자 선택](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 선택 하 여 정책 저장 **확인**합니다.

   > [!TIP]
   > 읽어보세요 [데이터 액세스 권한을 부여](./time-series-insights-data-access.md) Azure Active Directory에서 Time Series Insights 환경에 있습니다.

### <a name="client-app-initialization"></a>클라이언트 앱 초기화

1. 사용 된 **응용 프로그램 ID** 하 고 **클라이언트 암호** (응용 프로그램 키) 응용 프로그램 대신 토큰을 획득 하는 Azure Active Directory 응용 프로그램 등록 섹션에서.

    C#, 다음 코드는 응용 프로그램 대신 토큰을 획득할 수 있습니다. 전체 샘플은 [C#을 사용하여 데이터 쿼리](time-series-insights-query-data-csharp.md)를 참조하세요.

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

1. 애플리케이션이 Time Series Insights API를 호출할 때 `Authorization` 헤더에서 이 토큰을 전달할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- GA Time Series Insights API를 호출 하는 샘플 코드를 보려면 [쿼리 데이터를 사용 하 여 C# ](./time-series-insights-query-data-csharp.md)합니다.

- 미리 보기 Time Series Insights API 코드 샘플을 참조 하세요 [쿼리 미리 보기 데이터를 사용 하 여 C# ](./time-series-insights-update-query-data-csharp.md)합니다.

- API 참조 정보에 대해서는 [쿼리 API 참조](/rest/api/time-series-insights/ga-query-api)를 참조하세요.

- 설명 하는 방법 [서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)합니다.