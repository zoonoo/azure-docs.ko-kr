---
title: ID 공급자 구성(MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: iOS 및 macOS용 MSAL을 사용하여 B2C, 주권 클라우드 및 게스트 사용자와 같은 다양한 권한을 사용하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085216"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>방법: iOS 및 macOS용 MSAL을 구성하여 다른 ID 공급자를 사용하도록 합니다.

이 문서에서는 Azure Active Directory(Azure AD), B2C(비즈니스-소비자), 주권 클라우드 및 게스트 사용자와 같은 다른 기관에 대해 iOS 및 macOS(MSAL)에 대한 Microsoft 인증 라이브러리 앱을 구성하는 방법을 보여 줍니다.  이 문서 전체에서 일반적으로 권한을 ID 공급자로 생각할 수 있습니다.

## <a name="default-authority-configuration"></a>기본 기관 구성

`MSALPublicClientApplication`의 기본 기관 URL로 `https://login.microsoftonline.com/common`구성된다. 국가 클라우드와 같은 고급 시나리오를 구현하거나 B2C로 작업하지 않는 한 변경할 필요가 없습니다.

> [!NOTE]
> ADFS(ID 공급자)로 Active Directory 페더레이션 서비스를 사용한 최신 인증은 지원되지 않습니다(자세한 내용은 [개발자용 ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) 참조). ADFS는 페더레이션을 통해 지원됩니다.

## <a name="change-the-default-authority"></a>기본 권한 변경

B2C(비즈니스 대 소비자)와 같은 일부 시나리오에서는 기본 권한을 변경해야 할 수 있습니다.

### <a name="b2c"></a>B2C

B2C에서 작업하려면 [MSAL(Microsoft 인증 라이브러리)에](reference-v2-libraries.md) 다른 권한 구성이 필요합니다. MSAL은 하나의 기관 URL 형식을 B2C로 인식합니다. 인식된 B2C 기관 `https://<host>/tfp/<tenant>/<policy>`형식은 `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`예를 들어 입니다. 그러나 권한을 B2C 기관으로 명시적으로 선언하여 지원되는 다른 B2C 기관 URL을 사용할 수도 있습니다.

B2C에 대한 임의의 URL `MSALB2CAuthority` 형식을 지원하려면 다음과 같이 임의의 URL로 설정할 수 있습니다.

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

기본 B2C 권한 형식을 사용하지 않는 모든 B2C 권한은 알려진 기관으로 선언되어야 합니다.

각 다른 B2C 권한을 알려진 기관 목록에 추가하여 정책이 다를 경우에만

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

앱에서 새 정책을 요청하면 각 정책에 대해 권한 URL이 다르므로 권한 URL을 변경해야 합니다. 

B2C 응용 프로그램을 구성하려면 다음과 `@property MSALAuthority *authority` `MSALB2CAuthority` 같이 `MSALPublicClientApplicationConfig` 만들기 `MSALPublicClientApplication`전에 의 인스턴스로 설정합니다.

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>주권 구름

앱이 주권 클라우드에서 실행되는 경우 `MSALPublicClientApplication`에서 권한 URL을 변경해야 할 수 있습니다. 다음 예제는 독일 AAD 클라우드에서 작동하도록 권한 URL을 설정합니다.

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

각 주권 클라우드에 서로 다른 범위를 전달해야 할 수 있습니다. 보낼 범위는 사용 중인 리소스에 따라 다릅니다. 예를 들어 전 `"https://graph.microsoft.com/user.read"` 세계 클라우드와 `"https://graph.microsoft.de/user.read"` 독일어 클라우드에서 사용할 수 있습니다.

### <a name="signing-a-user-into-a-specific-tenant"></a>사용자를 특정 테넌트에 서명

기관 URL을 `"login.microsoftonline.com/common"`로 설정하면 사용자는 홈 테넌트에 로그인됩니다. 그러나 일부 앱은 사용자를 다른 테넌트에 서명해야 할 수 있으며 일부 앱은 단일 테넌트에서만 작동합니다.

사용자를 특정 테넌트에 로그인하려면 특정 권한으로 구성합니다. `MSALPublicClientApplication` 예를 들어:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

다음은 특정 테넌트에 사용자를 서명하는 방법을 보여 주며,

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>지원되는 기관

### <a name="msalauthority"></a>MSAL 권한

클래스는 `MSALAuthority` MSAL 기관 클래스의 기본 추상 클래스입니다. 또는 `alloc` `new`를 사용하여 인스턴스를 만들려고 하지 마십시오. 대신 직접 (,`MSALAADAuthority` `MSALB2CAuthority`) 의 하위 클래스 중 하나를 `authorityWithURL:error:` 만들거나 팩터리 메서드를 사용하여 권한 URL을 사용하여 하위 클래스를 만듭니다.

속성을 `url` 사용하여 정규화된 권한 URL을 가져옵니다. 권한의 일부가 아닌 추가 매개 변수 및 경로 구성 요소 또는 조각은 반환된 정규화 된 권한 URL에 없습니다.

다음은 사용하려는 권한에 따라 인스턴스화할 수 `MSALAuthority` 있는 하위 클래스입니다.

### <a name="msalaadauthority"></a>MSALAAD 기관

`MSALAADAuthority`는 AAD 기관을 나타냅니다. 기관 URL은 다음과 같은 형식이어야 `<port>` 하며 여기서는 선택 사항입니다.`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2C 기관

`MSALB2CAuthority`는 B2C 기관을 나타냅니다. 기본적으로 B2C 권한 URL은 다음과 `<port>` `https://<host>:<port>/tfp/<tenant>/<policy>`같은 형식이어야 합니다. 그러나 MSAL은 다른 임의B2C 권한 형식도 지원합니다.

## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기
