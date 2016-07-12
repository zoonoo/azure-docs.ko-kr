<properties
	pageTitle="Azure AD에서 서명 키 롤오버 | Microsoft Azure"
	description="이 문서에서는 Azure Active Directory에 대한 서명 키 롤오버 모범 사례를 설명합니다."
	services="active-directory"
	documentationCenter=".net"
	authors="gsacavdm"
	manager="krassk"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="gsacavdm"/>

# Azure Active Directory에서 서명 키 롤오버

이 항목에서는 보안 토큰을 서명하기 위해 Azure AD(Azure Active Directory)에 사용되는 공개 키에 대해 알아야 할 내용을 설명합니다. 이러한 키 롤오버가 정기적으로 있으며, 비상 시에는 곧바로 롤오버될 수 있습니다. Azure AD를 사용하는 모든 응용 프로그램은 키 롤오버 프로세스를 프로그래밍 방식으로 처리할 수 있어야 합니다. 키의 작동 방식과 롤오버가 응용 프로그램에 미친 영향을 평가하는 방법, 필요한 경우 키 롤오버를 처리하도록 응용 프로그램을 업데이트하는 방법을 이해하려면 계속 읽어 보세요.

> [AZURE.IMPORTANT] 다음 서명 키 롤오버는 2016년 8월 15일에 발생하며 갤러리 응용 프로그램이나 B2C 테넌트의 응용 프로그램에 영향을 주지 *않습니다*.

## Azure AD의 서명 키 개요

Azure AD는 업계 표준을 기반으로 하는 공개 키 암호화를 사용하여 Azure AD 자체 및 이를 사용하는 응용 프로그램 간의 트러스트를 설정합니다. 실질적인 측면에서, Azure AD는 공개 및 개인 키 쌍으로 구성된 서명 키를 사용하는 방식으로 작동합니다. 사용자가 인증을 위해 Azure AD를 사용하는 응용 프로그램에 로그인하면 Azure AD는 사용자에 대한 정보가 포함된 보안 토큰을 만듭니다. 이 토큰은 응용 프로그램으로 다시 전송되기 전에 개인 키를 사용하여 Azure AD에 의해 서명됩니다. 해당 토큰이 유효하고 실제로 Azure AD에서 발생한 것인지 확인하기 위해 응용 프로그램은 테넌트의 [OpenID Connect Discovery 문서](http://openid.net/specs/openid-connect-discovery-1_0.html) or SAML/WS-Fed [페더레이션 메타데이터 문서](active-directory-federation-metadata.md)에 포함된 Azure AD가 노출한 공개 키를 사용하여 토큰의 서명을 유효성 검사해야 합니다.

보안을 위해 Azure AD의 서명 키는 주기적으로 롤링하고, 비상 시에는 곧바로 롤오버될 수 있습니다. 빈도에 관계 없이 키 롤오버 이벤트를 처리하기 위해 Azure AD와 통합되는 모든 응용 프로그램을 준비해야 합니다. 없는 경우 응용 프로그램은 토큰에서 서명을 확인하기 위해 만료된 키를 사용하려고 시도하며 서명 요청이 실패합니다.

OpenID Connect discovery 문서와 페더레이션 메타데이터 문서에는 사용 가능한 키가 항상 두 개 이상 있습니다. 하나의 키가 곧 롤오버되고 다른 키가 대체되는 방식으로 문서에 지정된 키를 사용하도록 응용 프로그램이 준비되어야 합니다.

## 응용 프로그램이 영향을 받을지 여부와 그에 대한 대처 방안을 평가하는 방법

응용 프로그램이 키 롤오버를 처리하는 방식은 응용 프로그램 유형 또는 사용된 ID 프로토콜 및 라이브러리 버전과 같은 변수에 따라 달라집니다. 아래 섹션에서는 가장 일반적인 유형의 응용 프로그램이 키 롤오버의 영향을 받는지 여부와 자동 롤오버를 지원하거나 수동으로 키를 업데이트하도록 응용 프로그램을 업데이트하는 방법에 대한 지침을 제공하는지 여부를 평가합니다.

* [웹 응용 프로그램 /.NET OWIN OpenID Connect, WS-Fed 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용하는 API](#owin)
* [웹 응용 프로그램 /.NET Core OpenID Connect 또는 JwtBearerAuthentication middleware를 사용하는 API](#owincore)
* [웹 응용 프로그램 /Node.js passport-azure-ad module을 사용하는 API](#passport)
* [웹 응용 프로그램 / Visual Studio 2015를 사용하여 만든 API](#vs2015)
* [Visual Studio 2013을 사용하여 만든 웹 응용 프로그램](#vs2013)
* [Visual Studio 2013을 사용하여 만든 웹 API](#vs2013_webapi)
* [Visual Studio 2012를 사용하여 만든 웹 응용 프로그램](#vs2012)
* [Visual Studio 2010, 2008 또는 WIF(Windows Identity Foundation)를 사용하여 만든 웹 응용 프로그램](#vs2010)
* [웹 응용 프로그램 / 다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 API](#other)

### <a name="owin"></a> 웹 응용 프로그램 /.NET OWIN OpenID Connect, WS-Fed 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용하는 API

응용 프로그램에서 .NET OWIN OpenID Connect, WS-Fed 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용 하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

응용 프로그램의 Startup.cs 또는 Startup.Auth.cs에서 다음 코드 조각을 찾아봄으로써 응용 프로그램에서 이들 미들웨어를 사용하는지 확인할 수 있습니다.

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
	 // ...
 	});
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
	 new WindowsAzureActiveDirectoryBearerAuthenticationOptions
	 {
	 // ...
	 });
```

### <a name="owincore"></a> 웹 응용 프로그램 /.NET Core OWIN OpenID Connect 또는 JwtBearerAuthentication middleware를 사용하는 API

응용 프로그램에서 .NET Core OWIN OpenID Connect 또는 JwtBearerAuthentication 미들웨어를 사용 하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

응용 프로그램의 Startup.cs 또는 Startup.Auth.cs에서 다음 코드 조각을 찾아봄으로써 응용 프로그램에서 이들 미들웨어를 사용하는지 확인할 수 있습니다.

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
	 // ...
 	});
```

### <a name="passport"></a> 웹 응용 프로그램 /Node.js passport-ad module을 사용하는 API

응용 프로그램에서 Node.js passport-ad module을 사용 하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

응용 프로그램의 app.js에서 다음 코드 조각을 찾아봄으로써 응용 프로그램 passport ad를 확인할 수 있습니다.

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
	//...
));
```

### <a name="vs2015"></a> 웹 응용 프로그램 / Visual Studio 2015를 사용하여 만든 API

Visual Studio 2015에서 웹 응용 프로그램 템플릿을 사용하여 응용 프로그램을 빌드했고 **인증 변경** 메뉴에서 **회사 및 학교 계정**을 선택한 경우 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다. OWIN OpenID Connect 미들웨어에 포함된 이 논리는 OpenID Connect discovery 문서에서 키를 검색하고 캐시하며 주기적으로 새로 고칩니다.

인증을 솔루션에 수동으로 추가하면 응용 프로그램에 필요한 키 롤오버 논리가 없을 수도 있습니다. 사용자가 직접 작성하거나 [웹 응용 프로그램 / 다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 API](#other)의 단계를 따라야 합니다.

### <a name="vs2013"></a> Visual Studio 2013를 사용하여 만든 웹 응용 프로그램

Visual Studio 2013에서 웹 응용 프로그램 템플릿을 사용하여 응용 프로그램을 빌드했고 **인증 변경** 메뉴에서 **조직 계정**을 선택한 경우 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다. 이 논리는 조직의 고유 ID 및 서명 키 정보를 프로젝트와 연결된 두 데이터베이스 테이블에 저장합니다. 프로젝트의 Web.config 파일에서 데이터베이스에 대한 연결 문자열을 찾을 수 있습니다.

인증을 솔루션에 수동으로 추가하면 응용 프로그램에 필요한 키 롤오버 논리가 없을 수도 있습니다. 사용자가 직접 작성하거나 [웹 응용 프로그램 / 다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 API](#other)의 단계를 따라야 합니다.

다음 단계는 응용 프로그램에서 논리가 제대로 작동하는지 확인하는 데 도움이 됩니다.

1. Visual Studio 2013에서 솔루션을 열고 오른쪽 창에서 **서버 탐색기** 탭을 클릭합니다.
2. **데이터 연결**, **DefaultConnection**, **테이블**을 차례로 확장합니다. **IssuingAuthorityKeys** 테이블을 찾아 마우스 오른쪽 단추로 클릭한 후 **테이블 데이터 표시**를 클릭합니다.
3. **IssuingAuthorityKeys** 테이블에는 키에 대한 지문 값에 해당하는 행이 하나 이상 있습니다. 테이블의 모든 행을 삭제합니다.
4. **테넌트** 테이블을 마우스 오른쪽 단추로 클릭하고 **테이블 데이터 표시**를 클릭합니다.
5. **테넌트** 테이블에는 고유한 디렉터리 테넌트 식별자에 해당하는 행이 하나 이상 있습니다. 테이블의 모든 행을 삭제합니다. **테넌트** 테이블 및 **IssuingAuthorityKeys** 테이블 모두에서 행을 삭제하지 않으면 런타임에 오류가 발생합니다.
6. 응용 프로그램을 빌드 및 실행합니다. 계정에 로그인한 후에 응용 프로그램을 중지할 수 있습니다.
7. **서버 탐색기**로 돌아가 **IssuingAuthorityKeys** 및 **테넌트** 테이블에서 값을 확인합니다. 페더레이션 메타데이터 문서의 적절한 정보로 자동으로 다시 채워진 것을 알 수 있습니다.

### <a name="vs2013"></a> Visual Studio 2013을 사용하여 만든 웹 API

Visual Studio 2013에서 Web API 템플릿을 사용하여 Web API 응용 프로그램을 만들었고 **인증 변경** 메뉴에서 **조직 계정**을 선택한 경우 응용 프로그램에 필요한 논리가 이미 있는 것입니다. 인증을 수동으로 구성한 경우 아래 지침을 따라 키 정보를 자동으로 업데이트하는 Web API를 구성하는 방법을 알아봅니다.

다음 코드 조각은 페더레이션 메타데이터 문서에서 최신 키를 가져온 후 [JWT 토큰 처리기](https://msdn.microsoft.com/library/dn205065.aspx)를 사용하여 토큰의 유효성을 검사하는 방법을 보여 줍니다. 이 코드 조각은 Azure AD에서 향후 토큰의 유효성을 검사하기 위해 키를 유지하는 데 키 위치에 관계 없이(데이터베이스, 구성 파일 또는 다른 곳) 고유의 캐싱 메커니즘을 사용한다고 가정합니다.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a> Visual Studio 2012를 사용하여 만든 웹 응용 프로그램

Visual Studio 2012에서 응용 프로그램을 빌드한 경우 응용 프로그램을 구성하는 데 아마도 ID 및 액세스 도구를 사용한 것입니다. 또한 [발급자 이름 레지스트리 유효성 검사(VINR)](https://msdn.microsoft.com/library/dn205067.aspx)를 사용할 가능성이 높습니다. VINR은 발급된 토큰의 유효성을 검사하는 데 사용된 키와 신뢰할 수 있는 ID 공급자(Azure AD)에 대한 정보의 유지 관리를 담당합니다. 또한 VINR을 통해 해당 디렉터리에 연결된 최신 페더레이션 메타데이터 문서를 다운로드하고 구성이 최신 문서와 함께 만료되었는지 확인하며 필요에 따라 새 키를 사용하도록 응용 프로그램을 업데이트하여 Web.config 파일에 저장된 키 정보를 자동으로 간편하게 업데이트할 수 있습니다.

Microsoft에서 제공하는 코드 샘플 또는 연습 문서를 사용하여 응용 프로그램을 만든 경우 키 롤오버 논리가 프로젝트에 이미 포함됩니다. 아래 코드가 프로젝트에 이미 있는 것을 확인할 수 있습니다. 응용 프로그램에 이 논리가 아직 없는 경우 아래 단계에 따라 추가하고 제대로 작동하는지 확인합니다.

1. **솔루션 탐색기**에서 참조를 해당 프로젝트에 대한 **System.IdentityModel** 어셈블리에 추가합니다.
2. **Global.asax.cs** 파일을 열고 지시문을 사용하여 다음을 추가합니다.
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. **Global.asax.cs** 파일에 다음 메서드를 추가합니다.
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. 아래와 같이 **Global.asax.cs**의 **Application\_Start()** 메서드에서 **RefreshValidationSettings()** 메서드를 추가합니다.
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

이러한 단계를 완료하면 응용 프로그램의 Web.config가 최신 키를 포함한 페더레이션 메타데이터 문서의 최신 정보로 업데이트됩니다. 이 업데이트는 응용 프로그램 풀이 IIS에서 재활용될 때마다 발생하며 기본적으로 IIS는 29시간마다 응용 프로그램을 재활용하도록 설정됩니다.

아래 단계에 따라 키 롤오버 논리가 제대로 작동하는지 확인합니다.

1. 응용 프로그램이 위의 코드를 사용 중인지를 확인한 후 **Web.config** 파일을 열고 **<issuerNameRegistry>** 블록으로 이동하여 구체적으로 다음 몇 줄을 확인합니다.
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. **<add thumbprint=””>** 설정에서 모든 문자를 다른 것으로 바꿔서 지문 값을 변경합니다. **Web.config** 파일을 저장합니다.

3. 응용 프로그램을 빌드하고 실행합니다. 로그인 프로세스를 완료할 수 있으면 응용 프로그램은 디렉터리의 페더레이션 메타데이터 문서에서 필요한 정보를 다운로드하여 키를 성공적으로 업데이트합니다. 로그인하는 데 문제가 있는 경우 [Azure AD를 사용하여 웹 응용 프로그램에 로그온 추가](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) 항목을 읽거나 [Azure Active Directory에 대한 다중 테넌트 클라우드 응용 프로그램](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) 코드 샘플을 다운로드 및 검사하여 응용 프로그램의 변경 내용이 올바른지 확인합니다.


### <a name="vs2010"></a> Visual Studio 2008 또는 2010과 .NET 3.5용 WIF(Windows Identity Foundation) v1.0을 사용하여 만든 웹 응용 프로그램

WIF v1.0에서 응용 프로그램을 빌드한 경우 새 키를 사용하도록 응용 프로그램의 구성을 자동으로 새로 고치는 데 제공된 메커니즘이 없습니다. 키를 업데이트하는 가장 쉬운 방법은 WIF SDK에 포함된 FedUtil 도구를 사용하는 것입니다. 이 도구를 통해 최신 메타데이터 문서를 검색하고 구성을 업데이트할 수 있습니다. 이러한 지침은 아래에 포함됩니다. 또는 다음 중 하나를 수행할 수 있습니다.

- 아래 최신 키 수동 검색 및 응용 프로그램 업데이트 섹션의 지침을 따르고 단계를 프로그래밍 방식으로 수행하도록 논리를 작성합니다.
- 응용 프로그램을 시스템 네임스페이스에 있는 최신 버전의 WIF를 포함하는 .NET 4.5로 업데이트합니다. 그런 다음 [발급자 이름 레지스트리 유효성 검사(VINR)](https://msdn.microsoft.com/library/dn205067.aspx)를 사용하여 응용 프로그램 구성의 자동 업데이트를 수행할 수 있습니다.


1. Visual Studio 2008 또는 2010의 개발 컴퓨터에 WIF v1.0 SDK가 설치되어 있는지 확인합니다. 아직 설치되어 있지 않은 경우 [여기에서 다운로드](https://www.microsoft.com/en-us/download/details.aspx?id=4451)할 수 있습니다.
2. Visual Studio에서 솔루션을 열고 해당 프로젝트를 마우스 오른쪽 단추로 클릭하고 **페더레이션 메타데이터 업데이트**를 선택합니다. 이 옵션을 사용할 수 없는 경우 FedUtil 및/또는 WIF v1.0 SDK가 설치되지 않은 것입니다.
3. 프롬프트에서 **업데이트**를 선택하여 페더레이션 메타데이터의 업데이트를 시작합니다. 응용 프로그램이 호스팅되는 서버 환경에 대한 액세스 권한이 있는 경우 필요에 따라 FedUtil의 [자동 메타데이터 업데이트 스케줄러](https://msdn.microsoft.com/library/ee517272.aspx)를 사용할 수 있습니다.
4. **마침**을 클릭하고 업데이트 프로세스를 완료합니다.

### <a name="other"></a> 웹 응용 프로그램 / 다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 API

다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 경우, 키가 OpenID Connect discovery 문서 또는 페더레이션 메타데이터 문서에서 검색되는지 확인하기 위해 라이브러리나 구현을 검토할 필요가 있습니다. 이를 확인하는 하나의 방법은 OpenID discovery 문서 또는 페더레이션 메타데이터 문서에 대한 모든 호출 코드 또는 라이브러리 코드를 검색하는 것입니다.

키는 어딘가에 저장되거나 응용 프로그램에 하드 코딩되어 있다면, 키를 수동으로 검색하여 그에 따라 업데이트할 수 있습니다. Azure Ad가 롤오버 주기를 늘리거나 비상 대역 외 롤오버를 갖는 경우 이후 작업 중단 및 오버 헤드를 방지하기 위해 이 문서에 나오는 방법 개요를 사용하여 **자동 롤오버를 지원하기 위해 응용 프로그램을 향상하는 것이 아주 좋습니다**

OpenID discovery 문서에서 최신 키 수동 검색.

1. 웹 브라우저에서 `https://login.microsoftonline.com/your_directory_name/.well-known/openid-configuration`으로 이동합니다. OpenID Connect discovery 문서의 내용이 표시됩니다. 이 문서에 대한 자세한 내용은 [OpenID Discovery 문서 사양](http://openid.net/specs/openid-connect-discovery-1_0.html)을 참조합니다.
2. Jwks\_uri의 값에 있는 링크를 복사합니다.
3. 브라우저에서 새 탭을 열고 방금 복사한 URL로 이동합니다. JSON Web Key Set 문서의 내용이 표시됩니다.
4. 새 키를 사용하도록 응용 프로그램을 업데이트하기 위해 각 **x5c** 요소를 찾은 후 각 요소의 값을 복사합니다. 예:
```
keys: [
	{
		kty: "RSA",
		use: "sig",
		kid: "MnC_VZcATfM5pOYiJHMba9goEKY",
		x5t: "MnC_VZcATfM5pOYiJHMba9goEKY",
		n: "vIqz-4-ER_vNW...ixLUQ",
		e: "AQAB",
		x5c: [
			"MIIC4jCCAcqgAw...dhXsIIKvJQ=="
		]
	},
```
5. **<X509Certificate>** 요소 값을 복사한 후 일반 텍스트 편집기를 열고 값을 붙여 넣습니다. 후행 공백을 모두 제거하고 **.cer** 확장명으로 파일을 저장합니다.

페더레이션 메타데이터 문서 문서에서 최신 키 수동 검색.

1. 웹 브라우저에서 `https://login.microsoftonline.com/your_directory_name/federationmetadata/2007-06/federationmetadata.xml`으로 이동합니다. 페더레이션 메타데이터 XML 문서의 내용이 표시됩니다. 이 문서에 대한 자세한 내용은 [페더레이션 메타데이터](active-directory-federation-metadata.md) 항목을 참조하세요.
2. 새 키를 사용하도록 응용 프로그램을 업데이트하기 위해 각 **<RoleDescriptor>** 블록을 찾은 후 각 블록의 **<X509Certificate>** 요소 값을 복사합니다. 예:
```
<RoleDescriptor xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType">
      <KeyDescriptor use="signing">
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
                <X509Data>
                    <X509Certificate>MIIDPjC…BcXWLAIarZ</X509Certificate>
```
3. **<X509Certificate>** 요소 값을 복사한 후 일반 텍스트 편집기를 열고 값을 붙여 넣습니다. 후행 공백을 모두 제거하고 **.cer** 확장명으로 파일을 저장합니다.

Azure AD에 대한 공개 키로 사용되는 X509 인증서를 만들었습니다. 지문 및 만료 날짜 등의 세부 정보를 사용하여 응용 프로그램의 현재 사용되는 인증서 및 지문이 유효한지 수동으로 또는 프로그래밍 방식으로 확인할 수 있습니다.

<!---HONumber=AcomDC_0629_2016-->