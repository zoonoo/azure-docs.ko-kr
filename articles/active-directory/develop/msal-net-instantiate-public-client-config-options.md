---
title: 공용 클라이언트 앱 인스턴스화(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 구성 옵션을 사용하여 공용 클라이언트 응용 프로그램을 인스턴스화하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083602"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET 사용하여 구성 옵션을 사용하여 공용 클라이언트 응용 프로그램을 인스턴스화합니다.

이 문서에서는 .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하여 [공용 클라이언트 응용 프로그램을](msal-client-applications.md) 인스턴스화하는 방법에 대해 설명합니다.  응용 프로그램은 설정 파일에 정의된 구성 옵션으로 인스턴스화됩니다.

응용 프로그램을 초기화하기 전에 먼저 앱을 Microsoft ID 플랫폼과 통합할 수 있도록 응용 프로그램을 [등록해야](quickstart-register-app.md) 합니다. 등록 후 Azure 포털에서 찾을 수 있는 다음 정보가 필요할 수 있습니다.

- 클라이언트 ID(GUID를 나타내는 문자열)
- 인스턴스라는 ID 공급자 URL과 응용 프로그램의 로그인 대상입니다. 이러한 두 매개 변수를 통칭하여 기관이라고 합니다.
- 조직에 대해서만 비즈니스 응용 프로그램을 작성하는 경우 테넌트 ID(단일 테넌트 응용 프로그램이라고도 함).
- 웹 앱의 경우, 공용 클라이언트 앱(특히 앱이 브로커를 사용해야 하는 경우)의 경우 ID 공급자가 보안 토큰으로 응용 프로그램에 다시 연락하는 리디렉션Uri도 설정해야 합니다.


.NET 코어 콘솔 응용 프로그램에는 다음과 같은 *appsettings.json* 구성 파일이 있을 수 있습니다.

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

다음 코드는 설정 파일의 구성을 사용하여 응용 프로그램을 만듭니다.

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

