---
title: 웹 API를 호출하는 데스크톱 앱 구성 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 데스크톱 앱의 코드를 구성하는 방법에 대해 알아봅니다.
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2ba69e6447c686230412c33e74196c4bb594e0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611819"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>웹 API를 호출하는 데스크톱 앱: 코드 구성

이제 응용 프로그램을 만들었으니 응용 프로그램의 좌표로 코드를 구성하는 방법을 배웁니다.

## <a name="microsoft-authentication-libraries"></a>마이크로소프트 인증 라이브러리

다음 Microsoft 인증 라이브러리(MSAL)는 데스크톱 응용 프로그램을 지원합니다.

  Microsoft 인증 라이브러리 | 설명
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Linux, Windows 및 macOS와 같은 여러 플랫폼에서 데스크톱 응용 프로그램 빌드를 지원합니다.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 여러 플랫폼에서 데스크톱 응용 프로그램 빌드를 지원합니다.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 여러 플랫폼에서 데스크톱 응용 프로그램 빌드를 지원합니다.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | macOS에서만 실행되는 데스크톱 응용 프로그램을 지원합니다.

## <a name="public-client-application"></a>공용 클라이언트 응용 프로그램

코드 관점에서 데스크톱 응용 프로그램은 공용 클라이언트 응용 프로그램입니다. 대화형 인증을 사용하는지 여부에 따라 구성이 약간 다릅니다.

# <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET. `IPublicClientApplication`

![I퍼블릭 클라이언트 응용 프로그램](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>코드에 의해독점적으로

다음 코드는 공용 클라이언트 응용 프로그램을 인스턴스화하고 Microsoft Azure 공용 클라우드의 사용자에게 직장 또는 학교 계정 또는 개인 Microsoft 계정이 있는 로그인을 제공합니다.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

앞서 와 같이 대화형 인증 또는 장치 코드 흐름을 `.WithRedirectUri` 사용하려는 경우 수정자를 사용합니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>구성 파일 사용

다음 코드는 프로그래밍 방식으로 작성하거나 구성 파일에서 읽을 수 있는 구성 개체에서 공용 클라이언트 응용 프로그램을 인스턴스화합니다.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>보다 정교한 구성

여러 개 수정자를 추가하여 응용 프로그램 빌드를 구체화할 수 있습니다. 예를 들어 여기에 표시된 미국 정부와 같이 응용 프로그램을 국가 클라우드의 다중 테넌트 응용 프로그램으로 지정하려면 다음을 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET 또한 Active Directory 페더레이션 서비스 2019에 대한 수정자를 포함합니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

마지막으로 Azure Active Directory(Azure AD) B2C 테넌트에 대한 토큰을 획득하려면 다음 코드 조각에 표시된 대로 테넌트를 지정합니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>자세한 정보

MSAL.NET 데스크톱 응용 프로그램을 구성하는 방법에 대해 자세히 알아보려면 다음을 수행하십시오.

- 에서 `PublicClientApplicationBuilder`사용할 수 있는 모든 수정자 목록은 참조 설명서 [PublicClientApplicationBuilder를](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)참조하십시오.
- 에 `PublicClientApplicationOptions`노출된 모든 옵션에 대한 설명은 참조 설명서의 [PublicClientApplication옵션을](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) 참조하십시오.

### <a name="complete-example-with-configuration-options"></a>구성 옵션으로 예제 완료

다음과 같은 `appsettings.json` 구성 파일이 있는 .NET Core 콘솔 응용 프로그램을 상상해 보십시오.

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

을 사용하여 이 파일에서 읽을 코드가 거의 없습니다. NET 제공 구성 프레임워크:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

이제 응용 프로그램을 만들려면 다음 코드를 작성합니다.

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

`.Build()` 메서드를 호출하기 전에 앞에서 보았듯이 메서드에 `.WithXXX` 대한 호출로 구성을 재정의할 수 있습니다.

# <a name="java"></a>[Java](#tab/java)

다음은 샘플을 구성하는 MSAL Java 개발 샘플에 사용되는 [클래스입니다.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java)

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[Macos](#tab/macOS)

다음 코드는 공용 클라이언트 응용 프로그램을 인스턴스화하고 Microsoft Azure 공용 클라우드의 사용자에게 직장 또는 학교 계정 또는 개인 Microsoft 계정이 있는 로그인을 제공합니다.

### <a name="quick-configuration"></a>빠른 구성

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>보다 정교한 구성

여러 개 수정자를 추가하여 응용 프로그램 빌드를 구체화할 수 있습니다. 예를 들어 여기에 표시된 미국 정부와 같이 응용 프로그램을 국가 클라우드의 다중 테넌트 응용 프로그램으로 지정하려면 다음을 작성할 수 있습니다.

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱에 대한 토큰 획득](scenario-desktop-acquire-token.md)
