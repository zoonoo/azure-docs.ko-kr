---
title: 공용 클라이언트 앱 인스턴스화(MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: .NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하여 구성 옵션으로 공용 클라이언트 애플리케이션을 인스턴스화하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3e2ffebf0b414d4b59178fe04fb109530365786b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064711"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET을 사용하여 구성 옵션으로 공용 클라이언트 애플리케이션 인스턴스화

.NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용하여 구성 옵션으로 [공용 클라이언트 애플리케이션](msal-client-applications.md)을 인스턴스화하는 방법을 알아봅니다.  애플리케이션은 설정 파일에 정의된 구성 옵션으로 인스턴스화됩니다.

애플리케이션을 초기화하려면 먼저 앱이 Microsoft ID 플랫폼과 통합될 수 있도록 해당 애플리케이션을 [등록](quickstart-register-app.md)해야 합니다. 등록 후에는 다음 정보가 필요할 수 있으며 해당 정보는 Azure Portal에서 찾을 수 있습니다.

- 클라이언트 ID(GUID를 나타내는 문자열)
- ID 공급자 URL(인스턴스 이름) 및 애플리케이션의 로그인 대상. 이러한 두 매개 변수를 통칭하여 권한이라고 합니다.
- 조직 전용 사업 부문 애플리케이션(단일 테넌트 애플리케이션이라고도 함)을 작성하는 경우 테넌트 ID.
- 웹앱의 경우와 때로 공용 클라이언트 앱의 경우(특히 앱에서 broker를 사용해야 하는 경우) ID 공급자가 애플리케이션에 보안 토큰을 다시 연결할 redirectUri도 설정해야 합니다.


.NET Core 콘솔 애플리케이션에는 다음과 같은 *appsettings.json* 구성 파일이 있을 수 있습니다.

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

다음 코드는 .NET 구성 프레임워크를 사용하여 이 파일을 읽습니다.

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

다음 코드는 설정 파일의 구성을 사용하여 애플리케이션을 만듭니다.

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

