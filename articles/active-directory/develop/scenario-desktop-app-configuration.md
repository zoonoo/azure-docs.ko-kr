---
title: 웹 Api를 호출 하는 데스크톱 앱 구성-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 데스크톱 앱의 코드를 구성 하는 방법을 알아봅니다.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1724c25ef2d85aa8f838811864104b49576a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423907"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>웹 Api를 호출 하는 데스크톱 앱-코드 구성

이제 응용 프로그램을 만들었으므로 응용 프로그램 좌표를 사용 하 여 코드를 구성 하는 방법을 배웁니다.

## <a name="msal-libraries"></a>MSAL 라이브러리

데스크톱 응용 프로그램을 지 원하는 Microsoft 라이브러리는 다음과 같습니다.

  MSAL 라이브러리 | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 여러 플랫폼 (Linux, Windows 및 MacOS)에서 데스크톱 응용 프로그램을 빌드할 수 있도록 지원 합니다.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 에서는 여러 플랫폼에서 데스크톱 응용 프로그램을 빌드할 수 있습니다.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 에서는 여러 플랫폼에서 데스크톱 응용 프로그램을 빌드할 수 있습니다.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | MacOS 에서만 실행 되는 데스크톱 응용 프로그램 지원

## <a name="public-client-application"></a>공용 클라이언트 응용 프로그램

코드 관점에서 볼 때 데스크톱 응용 프로그램은 공용 클라이언트 응용 프로그램입니다. 이 구성은 대화형 인증을 사용 하는지 여부에 따라 약간 다릅니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

MSAL.NET `IPublicClientApplication`를 빌드하고 조작 해야 합니다.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>코드 전용

다음 코드에서는 회사 및 학교 계정이 나 개인 Microsoft 계정를 사용 하 여 공용 클라이언트 응용 프로그램, Microsoft Azure 공용 클라우드의 로그인 사용자를 인스턴스화합니다.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

위에서 설명한 것 처럼 대화형 인증 또는 장치 코드 흐름을 사용 하려는 경우 `.WithRedirectUri` 한정자를 사용 합니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>구성 파일 사용

다음 코드는 구성 개체에서 공용 클라이언트 응용 프로그램을 인스턴스화하고 구성 파일에서 프로그래밍 방식으로 채우거 나 읽을 수 있습니다.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>보다 정교한 구성

여러 한정자를 추가 하 여 응용 프로그램 빌드를 정교 하 게 만들 수 있습니다. 예를 들어 응용 프로그램을 국가 클라우드의 다중 테 넌 트 응용 프로그램 (미국 정부 기관)으로 만들려면 다음을 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

또한 MSAL.NET에는 ADFS 2019에 대 한 한정자가 포함 되어 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

마지막으로 Azure AD B2C 테 넌 트에 대 한 토큰을 가져오려는 경우는 다음 코드 조각과 같이 테 넌 트를 지정할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>자세히 알아보기

MSAL.NET 데스크톱 응용 프로그램을 구성 하는 방법에 대 한 자세한 내용은 다음을 확인 하세요.

- `PublicClientApplicationBuilder`에서 사용할 수 있는 모든 한정자 목록은 참조 설명서 [Publicclientapplicationbuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) 를 참조 하세요.
- `PublicClientApplicationOptions`에서 제공 하는 모든 옵션에 대 한 설명은 참조 설명서의 [Publicclientapplicationoptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)를 참조 하세요.

### <a name="complete-example-with-configuration-options"></a>구성 옵션을 사용 하는 전체 예제

다음 `appsettings.json` 구성 파일을 포함 하는 .NET Core 콘솔 응용 프로그램을 가정해 보겠습니다.

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

.NET 제공 구성 프레임 워크를 사용 하 여이 파일을 읽을 수 있는 코드가 거의 없습니다.

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
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

이제 응용 프로그램을 만들려면 다음 코드를 작성 하기만 하면 됩니다.

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

`.Build()` 메서드를 호출 하기 전에 앞에서 볼 수 있는 것 처럼 `.WithXXX` 메서드를 호출 하 여 구성을 재정의할 수 있습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

다음은 샘플을 구성 하기 위해 MSAL Java dev 샘플에서 사용 되는 클래스입니다. [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

다음 코드에서는 회사 및 학교 계정이 나 개인 Microsoft 계정를 사용 하 여 공용 클라이언트 응용 프로그램, Microsoft Azure 공용 클라우드의 로그인 사용자를 인스턴스화합니다.

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

여러 한정자를 추가 하 여 응용 프로그램 빌드를 정교 하 게 만들 수 있습니다. 예를 들어 응용 프로그램을 국가 클라우드의 다중 테 넌 트 응용 프로그램 (미국 정부 기관)으로 만들려면 다음을 작성할 수 있습니다.

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
> [데스크톱 응용 프로그램에 대 한 토큰 가져오기](scenario-desktop-acquire-token.md)
