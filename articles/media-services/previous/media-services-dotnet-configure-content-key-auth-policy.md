---
title: Media Services .NET SDK를 사용 하 여 콘텐츠 키 권한 부여 정책 구성 | Microsoft Docs
description: Media Services .NET SDK를 사용 하 여 콘텐츠 키에 대 한 권한 부여 정책을 구성 하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 386b49698ca6b8ded2972aba14c1968620fcbb08
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974498"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>동적 암호화: 콘텐츠 키 인증 정책 구성
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 Azure Media Services를 사용 하 여 128 비트 암호화 키 또는 [PLAYREADY DRM (디지털 권한 관리](https://www.microsoft.com/playready/overview/))을 사용 하 여 AES (AES(Advanced Encryption Standard))로 보호 되는 HLS (MPEG 부드러운 스트리밍 및 HTTP 라이브 스트리밍) 스트림을 배달할 수 있습니다. Media Services를 사용 하 여 Widevine DRM으로 암호화 된 대시 스트림도 제공할 수도 있습니다. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

또한 Media Services는 클라이언트가 AES 키 또는 PlayReady/Widevine 라이선스를 가져와 암호화 된 콘텐츠를 재생할 수 있는 키/라이선스 배달 서비스를 제공 합니다.

Media Services 자산을 암호화 하려면 암호화 키 (CommonEncryption 또는 EnvelopeEncryption)를 자산에 연결 해야 합니다. 자세한 내용은 [.net을 사용 하 여 ContentKeys 만들기](media-services-dotnet-create-contentkey.md)를 참조 하세요. 또한이 문서에 설명 된 대로 키에 대 한 권한 부여 정책을 구성 해야 합니다.

플레이어에서 스트림을 요청 하면 Media Services는 지정 된 키를 사용 하 여 AES 또는 DRM 암호화를 사용 하 여 콘텐츠를 동적으로 암호화 합니다. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. 사용자에 게 키를 가져올 수 있는 권한이 있는지 여부를 확인 하기 위해 서비스는 키에 대해 지정한 권한 부여 정책을 평가 합니다.

A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. 콘텐츠 키 인증 정책에는 하나 이상의 권한 부여 제한이 있을 수 있습니다. 옵션은 open 또는 token restriction입니다. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. Media Services는[SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)(단순 웹 토큰) 형식 및 JSON Web Token ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) 형식의 토큰을 지원 합니다.

Media Services STS를 제공 하지 않습니다. 사용자 지정 STS를 만들거나 Azure Access Control Service를 사용 하 여 토큰을 발급할 수 있습니다. 지정 된 키로 서명 된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급 하도록 STS를 구성 해야 합니다 (이 문서에서 설명). 토큰이 유효 하 고 토큰의 클레임이 콘텐츠 키에 대해 구성 된 것과 일치 하면 Media Services 키 배달 서비스는 암호화 키를 클라이언트에 반환 합니다.

További információkért tekintse át a következő cikkeket:

- [JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Media Services OWIN MVC 기반 앱을 Azure Active Directory와 통합 하 고 JWT 클레임을 기반으로 콘텐츠 키 배달 제한](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>몇 가지 고려 사항이 적용 됩니다.
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. 콘텐츠 스트리밍을 시작 하 고 동적 패키징 및 동적 암호화를 활용 하려면 스트리밍 끝점이 "실행 중" 상태 여야 합니다. 
* 자산은 적응 비트 전송률 Mp4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함 해야 합니다. 자세한 내용은 [자산 인코딩](media-services-encode-asset.md)을 참조 하세요.
* Assetcreationoptions.storageencrypted 옵션을 사용 하 여 자산을 업로드 하 고 인코딩합니다.
* 동일한 정책 구성이 필요한 여러 콘텐츠 키를 사용 하려는 경우 단일 권한 부여 정책을 만들고 여러 콘텐츠 키와 함께 다시 사용 하는 것이 좋습니다.
* 키 배달 서비스는 ContentKeyAuthorizationPolicy 및 관련 개체 (정책 옵션 및 제한)를 15 분 동안 캐시 합니다. ContentKeyAuthorizationPolicy를 만들고 토큰 제한을 사용 하도록 지정 하 고, 테스트 한 다음, 정책을 개방형 제한으로 업데이트할 수 있습니다. 이 프로세스는 정책이 미해결 버전의 정책으로 전환 될 때까지 약 15 분이 걸립니다.
* Az objektumhoz tartozó továbbítási szabályzat hozzáadásakor vagy módosításakor törölnie kell minden meglévő lokátort, majd létre kell hoznia egy újat.
* 현재는 점진적 다운로드를 암호화할 수 없습니다.
* Media Services 스트리밍 끝점은 실행 전 응답에서 CORS ' Access-t e r-t o s ' 헤더의 값을 와일드 카드 '\*'로 설정 합니다. 이 값은 Azure Media Player, Roku 및 JWPlayer 등 대부분의 플레이어에서 잘 작동 합니다. 그러나 자격 증명 모드를 "포함"으로 설정 하 고, 해당 .js의 XMLHttpRequest는 '\*' 와일드 카드를 ' Access-t e r-t o n s '의 값으로 허용 하지 않기 때문에,이를 사용 하는 일부 플레이어는 작동 하지 않습니다. 이러한 제한 사항에 대 한 해결 방법으로, 단일 도메인에서 클라이언트를 호스트 하는 경우 실행 전 응답 헤더에서 해당 도메인을 지정할 수 Media Services. 도움이 필요 하면 Azure Portal를 통해 지원 티켓을 여세요.

## <a name="aes-128-dynamic-encryption"></a>AES-128 동적 암호화
### <a name="open-restriction"></a>열기 제한
열기 제한은 시스템이 키를 요청 하는 사람에 게 키를 제공 하는 것을 의미 합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

다음 예제에서는 공개 권한 부여 정책을 만들고 콘텐츠 키에 추가 합니다.
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>토큰 제한
이 섹션에서는 콘텐츠 키 권한 부여 정책을 만들고 콘텐츠 키와 연결 하는 방법을 설명 합니다. 권한 부여 정책은 사용자가 키를 받을 권한이 있는지 확인 하기 위해 충족 해야 하는 권한 부여 요구 사항을 설명 합니다. 예를 들어 인증 키 목록에 토큰에 서명 된 키가 포함 되어 있습니까?

토큰 제한 옵션을 구성 하려면 XML을 사용 하 여 토큰의 권한 부여 요구 사항을 설명 해야 합니다. 토큰 제한 구성 XML은 다음 XML 스키마를 준수 해야 합니다.
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 STS입니다. 대상 (범위 라고도 함)은 토큰의 의도 또는 토큰에서 액세스 권한을 부여 하는 리소스에 대해 설명 합니다. Media Services 키 배달 서비스는 토큰의 이러한 값이 템플릿의 값과 일치 하는지 확인 합니다.

Media Services SDK for .NET을 사용 하는 경우 TokenRestrictionTemplate 클래스를 사용 하 여 제한 토큰을 생성할 수 있습니다.
다음 예제에서는 토큰 제한으로 권한 부여 정책을 만듭니다. 이 예제에서는 클라이언트가 서명 키 (VerificationKey), 토큰 발급자 및 필요한 클레임을 포함 하는 토큰을 제공 해야 합니다.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>테스트 토큰
키 권한 부여 정책에 사용 된 토큰 제한에 따라 테스트 토큰을 가져오려면 다음을 수행 합니다.
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>PlayReady 동적 암호화
Media Services를 사용 하 여 사용자가 보호 된 콘텐츠를 재생 하려고 할 때 PlayReady DRM 런타임에서 적용할 권한 및 제한 사항을 구성할 수 있습니다. 

PlayReady를 사용 하 여 콘텐츠를 보호 하는 경우 권한 부여 정책에서 지정 해야 하는 항목 중 하나는 [playready 라이선스 템플릿을](media-services-playready-license-template-overview.md)정의 하는 XML 문자열입니다. .NET 용 Media Services SDK에서는 PlayReadyLicenseResponseTemplate 및 PlayReadyLicenseTemplate 클래스를 사용 하 여 PlayReady 라이선스 템플릿을 정의 하는 데 도움이 됩니다.

PlayReady 및 Widevine 사용 하 여 콘텐츠를 암호화 하는 방법을 알아보려면 [playready 및/또는 widevine 동적 일반 암호화 사용](media-services-protect-with-playready-widevine.md)을 참조 하세요.

### <a name="open-restriction"></a>열기 제한
열기 제한은 시스템이 키를 요청 하는 사람에 게 키를 제공 하는 것을 의미 합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

다음 예제에서는 공개 권한 부여 정책을 만들고 콘텐츠 키에 추가 합니다.

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>토큰 제한
토큰 제한 옵션을 구성 하려면 XML을 사용 하 여 토큰의 권한 부여 요구 사항을 설명 해야 합니다. 토큰 제한 구성 XML은 "토큰 제한 스키마" 섹션에 표시 된 XML 스키마를 준수 해야 합니다.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

키 권한 부여 정책에 사용 된 토큰 제한에 따라 테스트 토큰을 가져오려면 "[테스트 토큰](#test-token)" 섹션을 참조 하세요. 

## <a id="types"></a>ContentKeyAuthorizationPolicy를 정의할 때 사용 되는 형식
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>토큰

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
콘텐츠 키의 권한 부여 정책을 구성 했으므로 [자산 배달 정책 구성](media-services-dotnet-configure-asset-delivery-policy.md)을 참조 하세요.

