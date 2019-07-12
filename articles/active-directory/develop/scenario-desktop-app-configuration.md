---
title: 데스크톱 앱 웹 Api (코드 구성-)를 호출 되는 Microsoft id 플랫폼
description: 데스크톱 앱을 빌드하는 방법을 알아봅니다 호출 web Api (응용 프로그램의 코드 구성)는
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 600b6db1eb3d3b422d62e49c5bc816a1a56370f9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798520"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>호출 웹 Api 코드 구성 되는 데스크톱 앱

응용 프로그램을 만들었으므로 이제 응용 프로그램의 좌표를 사용 하 여 코드를 구성 하는 방법을 배웁니다.

## <a name="msal-libraries"></a>MSAL 라이브러리

데스크톱 응용 프로그램을 현재 지 원하는 유일한 MSAL 라이브러리는 MSAL.NET

## <a name="public-client-application"></a>공용 클라이언트 응용 프로그램

코드의 관점에서 데스크톱 응용 프로그램은 공용 클라이언트 응용 프로그램 및 이유는 빌드 및 조작 MSAL.NET `IPublicClientApplication`합니다. 다시 작업 약간 달라 지므로 대화형 인증 사용 여부

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>코드에서 단독으로

다음 코드는 공용 클라이언트 응용 프로그램을 회사 및 학교 계정 또는 개인 Microsoft 계정을 사용 하 여 Microsoft Azure 공용 클라우드, 사용자 로그인을 인스턴스화합니다.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

사용 하려는 하려는 경우 대화형 인증 또는 장치 코드 흐름을 사용 하 여 위의 예제와 같이는 `.WithRedirectUri` 한정자:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>구성 파일을 사용 하 여

다음 코드를 인스턴스화하고 되거나 수 있는 입력 기능에 프로그래밍 방식으로 구성 파일에서 읽을 구성 개체의 공용 클라이언트 응용 프로그램

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>더 상세 구성

다양 한 한정자를 추가 하 여 빌드 응용 프로그램을 중점적으로 설명 수입니다. 예를 들어, 응용 프로그램 (여기서 미국 정부) national 클라우드에서 다중 테 넌 트 응용 프로그램을 원한다 면 작성할 수 있습니다.

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET은 ADFS 2019에 대 한 한정자를도 포함 되어 있습니다.

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

마지막으로, Azure AD B2C 테 넌 트에 대 한 토큰을 획득 하려는 경우 다음 코드 조각에 나와 있는 것 처럼 테 넌 트를 지정할 수 있습니다.

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>자세한 정보

MSAL.NET 데스크톱 응용 프로그램을 구성 하는 방법을 자세히 알아보려면:

- 사용할 수 있는 모든 한정자의 목록은 `PublicClientApplicationBuilder`, 참조 설명서를 참조 [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 에 표시 된 모든 옵션의 설명은 `PublicClientApplicationOptions` 참조 [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), 참조 설명서에서

## <a name="complete-example-with-configuration-options"></a>구성 옵션을 사용 하 여 전체 예제

다음에는.NET Core 콘솔 응용 프로그램을 imagine `appsettings.json` 구성 파일:

```JSon
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

구성 프레임 워크; 제공 된.NET을 사용 하 여이 파일을 읽는 코드를 조금 해야

```CSharp
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
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

이제 응용 프로그램을 만들려면만 해야 다음 코드를 작성 합니다.

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

및 호출 하기 전에 합니다 `.Build()` 메서드를 호출 하 여 구성을 재정의할 수 있습니다 `.WithXXX` 메서드 이전에 표시 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱에 대 한 토큰 가져오기](scenario-desktop-acquire-token.md)
