---
title: Azure Time Series Insights에서 API로 인증하고 권한을 부여하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에 대한 인증 및 권한 부여를 구성하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9b6cd993e9f6c6dbf173c161de638c6c4a8b18d3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237046"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API에 대한 인증 및 권한 부여

이 문서에서는 Azure Time Series Insights API를 호출하는 사용자 지정 애플리케이션에서 사용되는 인증 및 권한 부여를 구성하는 방법을 설명합니다.

> [!TIP]
> 읽어보세요 [데이터 액세스 권한을 부여](./time-series-insights-data-access.md) Azure Active Directory에서 Time Series Insights 환경에 있습니다.

## <a name="service-principal"></a>서비스 주체

이 및 다음 섹션에는 응용 프로그램 대신 Time Series Insights API에 액세스 하는 응용 프로그램을 구성 하는 방법을 설명 합니다. 응용 프로그램 수를 쿼리 또는 사용자 자격 증명이 아니라 응용 프로그램 자격 증명을 사용 하 여 Time Series Insights 환경에서 참조 데이터를 게시 합니다.

## <a name="best-practices"></a>모범 사례

갖고 있는 경우 액세스 Time Series Insights는 응용 프로그램:

1. Azure Active Directory 앱을 설정 합니다.
1. [데이터 액세스 정책을 할당](./time-series-insights-data-access.md) Time Series Insights 환경에서.

사용자 자격 증명 대신 응용 프로그램을 사용 하 여 이후 바람직한:

* 자체 사용 권한과에서 다릅니다 앱 id에 사용 권한을 할당할 수 있습니다. 일반적으로 이러한 권한은 앱 실행에 필요한 것으로만 제한됩니다. 예를 들어 앱이 Time Series Insights 환경에서만 데이터를 읽을 수 있도록 허용할 수 있습니다.
* 책임이 변경 되 면 앱의 자격 증명을 변경할 수 없는 합니다.
* 무인 스크립트를 실행할 때 인증서 또는 애플리케이션 키를 사용하여 인증을 자동화할 수 있습니다.

다음 섹션에서는 Azure portal 통해 이러한 단계를 수행 하는 방법을 보여 줍니다. 문서는 응용 프로그램은 하나의 조직 내 에서만 실행 되도록 하는 단일 테 넌 트 응용 프로그램에 중점을 둡니다. 일반적으로 조직에서 실행 되는 기간 업무 응용 프로그램에 대 한 단일 테 넌 트 응용 프로그램을 사용할 수 있습니다.

## <a name="set-up-summary"></a>설정 요약

설정 흐름 세 단계로 구성 됩니다.

1. Azure Active Directory에서 애플리케이션을 만듭니다.
1. 이 애플리케이션에 Time Series Insights 환경에 액세스할 수 있는 권한을 부여합니다.
1. 토큰을 획득 하는 응용 프로그램 ID 및 키를 사용 하 여 `https://api.timeseries.azure.com/`입니다. 그런 다음 토큰을 사용하여 Time Series Insights API를 호출할 수 있습니다.

## <a name="detailed-setup"></a>자세한 설치

1. Azure Portal에서 **Azure Active Directory** > **앱 등록** > **새 애플리케이션 등록**을 선택합니다.

   [![Azure Active Directory에서 새 응용 프로그램 등록](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. 애플리케이션의 이름을 지정하고, **웹앱/API** 형식을 선택하고, **로그온 URL**에 대한 유효한 URI를 선택하고, **만들기**를 클릭합니다.

   [![Azure Active Directory에서 응용 프로그램 만들기](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. 새로 만든 애플리케이션을 선택하고 해당 애플리케이션 ID를 원하는 텍스트 편집기에 복사합니다.

   [![응용 프로그램 ID 복사](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. **키**를 선택하고 키 이름을 입력한 후 만료를 선택하고 **저장**을 클릭합니다.

   [![응용 프로그램 키를 선택 합니다.](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![키 이름 및 만료를 입력 하 고 저장 클릭](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. 원하는 텍스트 편집기에 키를 복사합니다.

   [![응용 프로그램 키를 복사 합니다.](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Time Series Insights 환경에 대해 **데이터 액세스 정책**을 선택하고 **추가**를 클릭합니다.

   [![Time Series Insights 환경에 새 데이터 액세스 정책 추가](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 에 **사용자 선택** 대화 상자에서 응용 프로그램 이름을 붙여 넣습니다 (에서 **2 단계**) 또는 응용 프로그램 ID (에서 **3 단계**).

   [![사용자 선택 대화 상자에서 응용 프로그램 찾기](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 역할 선택 (**판독기** 데이터를 쿼리 하는 것에 대 한 **참가자** 데이터를 쿼리하고 참조 데이터를 변경 하기 위한)를 클릭 하 고 **확인**합니다.

   [![읽기 권한자 또는 참가자 역할 선택 대화 상자에서 선택](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 클릭 하 여 정책 저장 **확인**합니다.

1. 응용 프로그램 ID를 사용 하 여 (에서 **3 단계**) 및 응용 프로그램 키 (에서 **5 단계**) 응용 프로그램 대신 토큰을 획득 합니다. 애플리케이션이 Time Series Insights API를 호출할 때 `Authorization` 헤더에서 이 토큰을 전달할 수 있습니다.

    C#을 사용하는 경우 다음 코드를 사용하여 애플리케이션 대신 토큰을 가져올 수 있습니다. 전체 샘플은 [C#을 사용하여 데이터 쿼리](time-series-insights-query-data-csharp.md)를 참조하세요.

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

사용 합니다 **응용 프로그램 ID** 하 고 **키** Azure Time Series Insight를 사용 하 여 인증 하도록 응용 프로그램에서 합니다.

## <a name="next-steps"></a>다음 단계

- Time Series Insights API를 호출하는 샘플 코드는 [C#을 사용하여 데이터 쿼리](time-series-insights-query-data-csharp.md)를 참조하세요.

- API 참조 정보에 대해서는 [쿼리 API 참조](/rest/api/time-series-insights/ga-query-api)를 참조하세요.

- 설명 하는 방법 [서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)합니다.
