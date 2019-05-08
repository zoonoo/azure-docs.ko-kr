---
title: 옵션 (Microsoft Authentication Library for.NET)를 사용 하 여 공용 클라이언트 앱을 인스턴스화하고 | Azure
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 구성 옵션을 사용 하 여 공용 클라이언트 응용 프로그램을 인스턴스화하는 방법에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a483cd6e22ce3c9fafb9b2b4d839e22c248f020
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158702"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET을 사용 하 여 구성 옵션을 사용 하 여 공용 클라이언트 응용 프로그램 인스턴스화

이 문서를 인스턴스화하는 방법을 설명 합니다는 [공용 클라이언트 응용 프로그램](msal-client-applications.md) .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 합니다.  응용 프로그램 설정 파일에 정의 된 구성 옵션을 사용 하 여 인스턴스화됩니다.

응용 프로그램을 초기화 하기 전에 먼저 [등록](quickstart-register-app.md) 해당 Microsoft id 플랫폼을 사용 하 여 앱을 통합 될 수 있도록 합니다. 등록이 완료 되 면 다음 정보를 (Azure portal에서 찾을 수 있습니다) 해야 합니다.

- 클라이언트 ID (GUID를 나타내는 문자열)
- Id 공급자 URL (명명 된 인스턴스) 및 응용 프로그램에 대 한 로그인 대상 그룹입니다. 이 두 매개 변수를 기관으로 통칭 합니다.
- 조직 (또한 명명 된 단일 테 넌 트 응용 프로그램)에 대해 전적으로 비즈니스 응용 프로그램을 작성 하는 경우에 테 넌 트 ID입니다.
- 웹 앱 및 경우에 따라 공용 클라이언트 앱 (특히 앱은 broker 사용 해야 하는 경우)에 대 한 것도 설정한 redirectUri는 id 공급자 백 연락을 보안 토큰을 사용 하 여 응용 프로그램.


.NET Core 콘솔 응용 프로그램에는 다음 있을 수 있습니다 *appsettings.json* 구성 파일:

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

다음 코드는.NET 구성 프레임 워크를 사용 하 여이 파일을 읽습니다.

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

다음 코드는 구성 설정 파일에서 사용 하 여 응용 프로그램을 만듭니다.

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

