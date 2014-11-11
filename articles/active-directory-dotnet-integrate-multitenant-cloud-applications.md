<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Integrating Multi-Tenant Cloud Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# Azure Active Directory와 다중 테넌트 클라우드 응용 프로그램 통합

## <a name="introduction"></a>소개

Azure AD(Azure Active Directory)는 클라우드 응용 프로그램과 관련하여 ID를 관리하고 액세스를 제어할 수 있도록 지원하는 최신 REST 기반 서비스입니다. Azure AD는 클라우드 서비스뿐 아니라 Azure, Microsoft Office 365, Dynamics CRM Online 및 Windows Intune과도 쉽게 통합됩니다. 또한 기존 온-프레미스 Active Directory 배포는 Azure AD를 완벽하게 활용할 수 있습니다. 자세한 내용은 [windowsazure.com][windowsazure.com]의 [ID 페이지][ID 페이지]를 참조하십시오.

이 자습서는 Azure AD와 다중 테넌트를 통합하려는 .NET 개발자를 대상으로 합니다. 다음 방법에 대해 알아봅니다.

-   고객이 Azure AD를 사용하여 응용 프로그램에 등록할 수 있도록 설정
-   Azure AD에서 SSO(Single Sign-On) 사용
-   Azure AD Graph API를 사용하여 고객의 디렉터리 데이터 쿼리

이 자습서의 공유 샘플 응용 프로그램은 [여기서 다운로드][여기서 다운로드]할 수 있습니다. 샘플은 변경 없이 그대로 실행할 수 있지만 https를 사용하도록 [Visual Studio의 포트 할당][Visual Studio의 포트 할당]을 변경해야 할 수도 있습니다. 이 링크의 지침을 따르지만 ApplicationHost.config 파일의 바인딩 섹션에서 바인딩 프로토콜을 "https"로 설정하십시오. 아래 단계의 모든 코드 조각은 샘플에서 가져온 것입니다.

> [WACOM.NOTE]
> 다중 테넌트 디렉터리 앱 샘플은 예시용으로만 제공된 것입니다. 프로덕션 환경에서는 이 샘플(도우미 라이브러리 클래스 포함)을 사용하지 마십시오.

### 필수 조건

이 연습에는 다음과 같은 개발자 필수 조건이 필요합니다.

-   [Visual Studio 2012][Visual Studio 2012]
-   [WCF Data Services for OData][WCF Data Services for OData]

### 목차

-   [소개][소개]
-   [1부: Azure AD 액세스를 위해 클라이언트 ID 가져오기][1부: Azure AD 액세스를 위해 클라이언트 ID 가져오기]
-   [2부: 고객이 Azure AD를 사용하여 등록할 수 있도록 설정][2부: 고객이 Azure AD를 사용하여 등록할 수 있도록 설정]
-   [3부: Single Sign-On 사용][3부: Single Sign-On 사용]
-   [4부: Access Azure AD Graph][4부: Access Azure AD Graph]
-   [5부: 응용 프로그램 게시][5부: 응용 프로그램 게시]
-   [요약][요약]

## <a name="getclientid"></a>1부: Azure AD 액세스를 위해 클라이언트 ID 가져오기

이 섹션에서는 Microsoft 판매자 대시보드 계정을 만든 후 클라이언트 ID 및 클라이언트 암호를 가져오는 방법에 대해 설명합니다. 클라이언트 ID는 응용 프로그램의 고유한 ID이고 클라이언트 암호는 클라이언트 ID를 사용하여 요청할 때 필요한 연결된 키입니다. 둘 다 Azure AD와 응용 프로그램을 통합하는 데 필요합니다.

### 1단계: Microsoft 판매자 대시보드의 계정 만들기

Azure AD와 통합할 응용 프로그램을 개발하고 게시하려면 [Microsoft 판매자 대시보드][Microsoft 판매자 대시보드] 계정에 등록해야 합니다. [계정 프로필을 회사 또는 개인으로 만들][계정 프로필을 회사 또는 개인으로 만들]라는 메시지가 표시됩니다. 이 프로필은 Azure 마켓플레이스 또는 기타 마켓플레이스에 응용 프로그램을 게시하는 데 사용되며, 클라이언트 ID 및 클라이언트 암호를 생성하는 데 필요합니다.

새로운 계정은 "승인 보류 중인 계정" 상태가 됩니다. 이 상태라고 해서 개발을 시작하지 못하는 것은 아닙니다. 클라이언트 ID와 초안 앱 목록은 계속 만들 수 있습니다. 그러나 계정 자체가 승인된 후에만 승인을 위해 앱 목록을 제출할 수 있습니다. 제출된 앱 목록은 승인된 후에만 Azure 마켓플레이스의 고객에게 표시됩니다.

### 2단계: 응용 프로그램의 클라이언트 ID 가져오기

Azure AD와 응용 프로그램을 통합하려면 클라이언트 ID 및 클라이언트 암호가 필요합니다. 클라이언트 ID는 응용 프로그램의 고유한 ID이며 기본적으로 Single Sign-On 또는 Azure AD Graph에 대한 호출 인증을 위해 응용 프로그램을 식별하는 데 사용됩니다. 클라이언트 ID 및 클라이언트 암호를 가져오는 방법에 대한 자세한 내용은 [Microsoft 판매자 대시보드에서 클라이언트 ID 및 암호 만들기][Microsoft 판매자 대시보드에서 클라이언트 ID 및 암호 만들기]를 참조하십시오.

> [WACOM.NOTE]
> 이 자습서의 뒷부분에서 클라이언트 ID 및 클라이언트 암호가 필요하므로, 기록해 두세요.

클라이언트 ID 및 클라이언트 암호를 생성하려면 Microsoft 판매자 대시보드에서 다음 속성을 입력해야 합니다.

**앱 도메인**: 응용 프로그램의 호스트 이름(예: "contoso.com")입니다. 이 속성에는 포트 번호가 포함되지 않아야 합니다. 개발 중 이 속성을 "localhost"로 설정해야 합니다.

**앱 리디렉션 URL**: 사용자가 로그인한 후 그리고 조직에서 응용 프로그램에 권한을 부여할 때 Azure AD에서 응답을 보내는 리디렉션 URL입니다. 예를 들면 "<https://contoso.com/>"입니다. 개발 중 이 속성을 "<https://localhost>:\<포트 번호\>"로 설정해야 합니다.

### 3단계: 클라이언트 ID 및 클라이언트 암호를 사용하도록 응용 프로그램 구성

이 단계에서는 판매자 대시보드에 등록 중 생성한 클라이언트 ID 및 클라이언트 암호가 필요합니다. 클라이언트 ID는 SSO에 사용되며, 이 자습서 뒷부분에서 Azure AD Graph API를 호출하는 액세스 토큰을 가져오는 데 클라이언트 ID 및 클라이언트 암호를 사용합니다.

샘플 응용 프로그램은 Azure AD를 사용하도록 미리 연결되었으며 구성에서 클라이언트 ID 및 클라이언트 암호를 로드합니다. 샘플 응용 프로그램의 **Web.config** 파일에서 다음과 같이 변경하십시오.

1.  **appSettings** 노드에서 "clientId" 및 "SymmetricKey"의 값을 클라이언트 ID, 클라이언트 암호 및 앱 도메인으로 바꾸십시오.

        <appSettings>
            <add key="clientId" value="(Your Client ID value)"/>
            <add key="SymmetricKey" value="(Your Client Secret value)"/>
            <add key="AppHostname" value="(Your App Domain)"/>
        </appSettings>

2.  **system.identityModel**의 **audienceUris** 노드에서 "spn" 뒤에 클라이언트 ID를 삽입합니다.

        <system.identityModel>
            <audienceUris>
                <add value="spn:(Your Client ID value)" />
            </audienceUris>

## <a name="enablesignup"></a>2부: 고객이 Azure AD를 사용하여 등록할 수 있도록 설정

이 섹션에서는 고객이 Azure AD를 사용하여 응용 프로그램에 등록하도록 설정하는 방법을 설명합니다. 고객이 Azure AD와 통합된 응용 프로그램을 사용하려면 먼저 테넌트 관리자가 응용 프로그램에 권한을 부여해야 합니다. 이 권한 부여 프로세스는 응용 프로그램의 Azure에 대한 동의 요청을 시작하며, 이 요청은 응용 프로그램에서 처리해야 하는 응답을 생성합니다. 다음 단계에서는 동의 요청을 생성하고 응답을 처리하는 방법을 설명합니다.

이 섹션의 단계에서는 샘플 응용 프로그램의 도우미 클래스를 사용합니다. 이러한 클래스는 샘플의 *Microsoft.IdentityModel.WAAD.Preview* 라이브러리에 포함되며, 이를 사용하면 ID 및 프로토콜 세부 사항이 아닌 응용 프로그램 코드에 더 쉽게 집중할 수 있습니다.

### 1단계: 응용 프로그램에 대한 동의 요청

다음 예제 조작에서는 응용 프로그램의 동의를 요청하는 프로세스를 나타냅니다.

1.  고객은 응용 프로그램에서 웹 페이지의 "Azure AD를 사용한 등록" 링크를 클릭합니다.
2.  고객을 요청에 추가된 응용 프로그램 정보와 함께 AD 권한 부여 페이지로 리디렉션합니다.
3.  고객은 응용 프로그램에 대해 동의하거나 거부합니다.
4.  Azure AD는 지정된 앱 리디렉션 URL로 고객을 리디렉션합니다. Microsoft 판매자 대시보드에서 클라이언트 ID 및 클라이언트 암호를 생성할 때 이 URL을 지정했습니다. 리디렉션 요청에서는 동의를 받은 경우 테넌트 관련 정보를 포함하여 동의 요청 결과를 표시합니다.

위의 2단계에서 리디렉션 요청을 생성하려면 Azure AD 권한 부여 페이지의 다음 URL에 쿼리 문자열 매개 변수를 추가해야 합니다. *<http://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx>*

쿼리 문자열 매개 변수는 아래에 설명되어 있습니다.

**ApplicationID**: (필수) 판매자 대시보드에서 받은 **ClientID** 값입니다.

**RequestedPermissions**: (옵션) 테넌트에서 응용 프로그램에 부여해야 하는 권한입니다.
배포 중 이러한 권한은 게시 취소된 응용 프로그램을 테스트하는 데 사용됩니다. 게시된 응용 프로그램에 대해 이 매개 변수는 무시됩니다. 대신 응용 프로그램 목록의 요청된 권한이 사용됩니다. 이 목록에 대한 자세한 내용은 5부를 참조하세요.
이 매개 변수의 가능한 3가지 값은 다음과 같습니다.

**DirectoryReader**: 디렉터리 데이터(예: 사용자 계정, 그룹 및 조직에 대한 정보)를 읽을 수 있는 권한을 부여합니다. SSO를 사용하도록 설정합니다.

**UserAccountAdministrator**: 데이터(예: 사용자, 그룹 및 조직에 대한 정보)를 읽고 쓸 수 있는 권한을 부여합니다. SSO를 사용하도록 설정합니다.

**None**: Single Sign-On을 사용하도록 설정하지만 디렉터리 데이터에 대한 액세스를 사용하지 않도록 설정합니다.

매개 변수를 지정하지 않거나 잘못 지정한 경우 기본값은 "None"입니다.

다음은 유효한 동의 요청 URL 예제입니다.
*<https://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx?ApplicationId=33E48BD5-1C3E-4862-BA79-1C0D2B51FB26&RequestedPermissions=DirectoryReader>*

샘플 응용 프로그램에서 "등록" 링크에는 아래와 유사한 동의 요청용 URL이 포함됩니다.

![로그인][로그인]

> [WACOM.NOTE]
> 게시 취소된 응용 프로그램을 테스트할 때 고객과 유사한 동의 환경을 진행하게 됩니다. 그러나 게시 취소된 응용 프로그램의 권한 부여 페이지는 게시된 응용 프로그램의 권한 부여 페이지와 다르게 표시됩니다. 게시된 응용 프로그램은 앱 이름, 로고 및 게시자 세부 정보를 표시하지만 게시 취소된 응용 프로그램은 이러한 세부 정보를 표시하지 않습니다.

### 2단계: 동의 응답 처리

고객이 응용 프로그램에 대해 동의하거나 거부한 후 Azure AD에서 응용 프로그램의 앱 리디렉션 URL로 응답을 보냅니다. 이 응답에는 다음과 같은 쿼리 문자열 매개 변수가 포함됩니다.

**TenantId**: 응용 프로그램에 권한을 부여한 Azure AD 테넌트의 고유한 GUID입니다. 이 매개 변수는 고객이 응용 프로그램에 권한을 부여한 경우에만 지정됩니다.

**Consent**: 응용 프로그램에 권한이 부여되면 이 값은 "Granted"로 설정되고, 요청이 거부되면 "Denied"로 설정됩니다.

다음은 응용 프로그램에 권한이 부여되었음을 나타내는 동의 요청에 대한 유효한 응답 예제입니다.
*<https://app.litware.com/redirect.aspx&TenantId=7F3CE253-66DB-4AEF-980A-D8312D76FDC2&Consent=Granted>*

Azure AD 권한 부여 페이지에 전송된 요청이 응답과 연결되도록(그리고 연결된 요청이 없는 모든 응답을 거부하도록) 응용 프로그램에서 컨텍스트를 유지해야 합니다.

<div class="dev-callout"><strong>참고</strong><p>동의한 경우 SSO 및 Graph 액세스가 프로비전될 때까지 Azure AD에서 어느 정도 시간이 걸릴 수 있습니다. 프로비전이 완료될 때까지 응용 프로그램에 등록한 각 조직의 첫 번째 사용자에게는 로그인 오류가 표시될 수 있습니다.</p></div>

고객이 응용 프로그램에 동의한 후 응용 프로그램에서 새로 만든 테넌트를 동의 응답에서 반환한 TenantId와 연결하여 저장하는 것이 중요합니다. 샘플 응용 프로그램은 *Microsoft.IdentityModel.WAAD.Preview.Consent* 네임스페이스에 *HttpModule*을 포함합니다. 이 항목은 성공적인 모든 동의 응답의 고객/TenantId "데이터 저장소"에 TenantId를 자동으로 기록합니다. 이를 위한 코드는 아래에 포함되어 있으며, 고객/TenantId "데이터 저장소"에 TenantId를 기록하는 작업은 *TrustedIssuers.Add* 메서드에서 수행합니다.

    private void Application_BeginRequest(Object source,
             EventArgs e)
    {
        HttpApplication application = (HttpApplication)source;
        HttpRequest req = application.Context.Request;             

        if((!string.IsNullOrEmpty(req.QueryString["TenantId"]) && (!string.IsNullOrEmpty(req.QueryString["Consent"]))))
        { 
            if(req.QueryString["Consent"].Equals("Granted",StringComparison.InvariantCultureIgnoreCase))
            {
                // For this sample we store the consenting tenants in
                // an XML file. We strongly recommend that you change
                // this to use your DataStore
                TrustedIssuers.Add(req.QueryString["TenantId"]; 
            }
        }            
    }

응용 프로그램의 동의 요청/응답 코드를 테스트하려면 먼저 Azure AD 디렉터리 테넌트를 가져와야 합니다.

### 3단계: Azure AD 테넌트를 가져와서 응용 프로그램 테스트

Azure AD와 통합되는 응용 프로그램의 기능을 테스트하려면 Azure AD 테넌트가 필요합니다. 다른 응용 프로그램을 테스트하는 데 사용하는 테넌트가 이미 있는 경우 이를 다시 사용할 수 있습니다. 다중 테넌트에서 앱을 테스트하고 사용할 수 있도록 테넌트를 두 개 이상 가져오는 것이 좋습니다. 이를 위해서 프로덕션 테넌트를 사용하지 않는 것이 좋습니다. [Azure AD 테넌트를 가져][Azure AD 테넌트를 가져]오십시오.

Azure AD 테넌트를 가져온 후 **F5**를 눌러 응용 프로그램을 빌드하고 실행할 수 있습니다. 또한 새 테넌트를 사용하여 응용 프로그램에 등록해 볼 수도 있습니다.

<div class="dev-callout"><strong>참고</strong><p>고객이 새 Azure AD 테넌트에 등록하는 경우 해당 테넌트가 완전히 프로비전되는 데 어느 정도 시간이 걸릴 수 있습니다. 프로비전이 완료될 때까지 동의 페이지에서 오류 메시지가 표시될 수 있습니다.</p></div>

## <a name="enablesso"></a>3부: Single Sign-On 사용

이 섹션에서는 SSO(Single Sign-On)를 사용하도록 설정하는 방법을 보여 줍니다. 이 프로세스는 응용 프로그램에 사용자를 인증해 주는 Azure AD에 대한 로그인 요청을 구성한 후 응용 프로그램에 권한을 부여한 테넌트에 고객이 속해 있다는 로그인 응답을 확인합니다. 로그인 요청을 수행하려면 판매자 대시보드의 클라이언트 ID 및 고객 조직의 테넌트 ID가 있어야 합니다.

로그인 요청은 디렉터리 테넌트와 관련되어 있으며 TenantID를 포함해야 합니다. Azure AD 디렉터리 테넌트의 도메인 이름에서 TenantID를 확인할 수 있습니다. 최종 사용자가 로그인할 때 해당 사용자의 이 도메인 이름을 가져오는 일반적인 방법은 다음과 같이 두 가지입니다.

-   응용 프로그램의 URL이 *<https://contoso.myapp.com>* 또는 *<https://myapp.com/contoso.com>*인 경우 *[contoso][contoso]* 및 *[contoso.com][contoso]*은 Azure AD 도메인 이름을 나타내며 *[myapp.com][contoso]*은 응용 프로그램의 URL을 나타냅니다.
-   응용 프로그램에서 사용자에게 전자 메일 주소 또는 Azure AD 도메인 이름을 묻는 메시지를 표시할 수 있습니다. 이 방법은 샘플 응용 프로그램에 사용되며, 사용자는 아래와 같이 Azure AD 도메인 이름을 입력해야 합니다.

![로그인][로그인]

### 1단계: 테넌트 ID 찾기

고객이 제공한 Azure AD 도메인 이름을 사용해서 Azure AD 페더레이션 메타데이터를 구문 분석하여 테넌트 ID를 찾을 수 있습니다. 샘플 응용 프로그램에서 이 프로세스는 *Microsoft.IdentityModel.WAAD.Preview.TenantUtils.Globals* 클래스의 *Domain2TenantId* 메서드로 처리됩니다.

이 프로세스를 확인하기 위해 다음 단계에서는 contoso.com 도메인 이름을 사용합니다.

1.  Azure AD 테넌트용 **FederationMetadata.xml** 파일을 가져옵니다. 예를 들면 다음과 같습니다.
    *<https://accounts.accesscontrol.windows.net/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml>*
2.  **FederationMetadata.xml** 파일에서 **Entity Descriptor** 항목을 찾습니다. 테넌트 ID는 아래와 같이 "<https://sts.windows.net>" 다음에 나오는 **entityID** 속성의 일부로 포함됩니다.

         <EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sts.windows.net/a7456b11-6fe2-4e5b-bc83-67508c201e4b/" ID="_cba45203-f8f4-4fc3-a3bb-0b136a2bafa5"> 

    이 경우 TenantID 값은 **a7456b11-6fe2-4e5b-bc83-67508c201e4b**입니다.

3.  응용 프로그램의 고객/TenantId "데이터 저장소"에 도메인 및 연결된 TenantID를 저장해야 합니다. 이후 로그인 요청에 이러한 두 값을 함께 사용할 수 있으므로 매번 **FederationMetadata.xml**을 가져올 필요가 없습니다. 샘플 응용 프로그램은 이 최적화 기능을 제공하지 않습니다.

### 2단계: 로그인 요청 생성

고객이 로그인 단추를 클릭하는 등의 방법으로 응용 프로그램에 로그인할 때 고객의 테넌트 ID 및 응용 프로그램의 클라이언트 ID를 사용하여 로그인 요청을 생성해야 합니다. 샘플 응용 프로그램에서 이 요청은 *Microsoft.IdentityModel.WAAD.Preview.WebSSO.URLUtils* 클래스의 *GenerateSignInMessage* 메서드로 생성됩니다. 이 메서드는 고객의 TenantID가 응용 프로그램에 권한을 부여한 조직을 나타내는지 확인하고 아래와 같은 로그인 단추의 대상 URL을 생성합니다.

![로그인][로그인]

이 단추를 클릭하면 사용자의 브라우저에서 Azure AD의 로그인 페이지로 이동합니다. 로그인하면 Azure AD에서 응용 프로그램으로 로그인 응답을 반환합니다.

### 3단계: 로그인 응답의 들어오는 토큰 발급자 유효성 검사

고객이 응용 프로그램에 로그인할 때 테넌트에서 응용 프로그램에 권한을 부여했는지 확인해야 합니다. 로그인 응답에는 토큰이 포함되며 토큰에는 응용 프로그램에서 검사할 수 있는 속성 및 클레임이 포함됩니다.

이 유효성 검사를 수행하기 위해 토큰의 발급자 속성에서 TenantID를 가져오고 이 발급자 속성이 고객/TenantId "데이터 저장소"에 있는지 확인해야 합니다. 샘플 응용 프로그램에서 Windows Identity Foundation의 *Saml2SecurityTokenHandler*를 확장하는 사용자 지정 토큰 처리기 클래스를 만들어 이 유효성 검사를 수행합니다. 사용자 지정 토큰 처리기는 들어오는 보안 토큰을 확인하고 TenantID의 유효성을 검사하도록 응용 프로그램에 클레임 및 속성을 사용하도록 설정합니다. 이 클래스의 코드 조각은 아래와 같습니다.

샘플 응용 프로그램에서는 *Microsoft.IdentityModel.WAAD.Preview.WebSSO* 네임스페이스에서 원래 코드를 찾을 수 있습니다. 토큰 처리기는 *Microsoft.IdentityModel.WAAD.Preview.WebSSO.TrustedIssuers* 클래스의 Contains 메서드도 사용하며, 이 메서드는 TenantID가 고객/TenantId "데이터 저장소"에 저장되어 있는지 확인합니다.

    /// <summary>
    /// Extends the out of the box SAML2 token handler by ensuring
    /// that incoming tokens have been issued by registered tenants 
    /// </summary>
    public class ConfigurationBasedSaml2SecurityTokenHandler : Saml2SecurityTokenHandler
    {
        public override ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> ValidateToken(SecurityToken token)
        {
            ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> aa = base.ValidateToken(token);
            Saml2SecurityToken ss = token as Saml2SecurityToken;
            string tenant = ss.Assertion.Issuer.Value.Split('/')[3];
            if (!TrustedIssuers.Contains(tenant))
            {
                throw new SecurityTokenValidationException(string.Format("The tenant {0} is not registered with the application", tenant));
            }
            return aa;
        }
    }

토큰 유효성이 검사되면 사용자가 응용 프로그램에 로그인됩니다. 응용 프로그램을 실행하고 앞서 만든 동의받은 테넌트에서 Azure AD 계정을 사용하여 로그인하십시오.

## <a name="accessgraph"></a>4부: Access Azure AD Graph

이 섹션에서는 액세스 토큰을 가져오고 Azure AD Graph API를 호출하여 테넌트의 디렉터리 데이터에 액세스하는 방법을 설명합니다. 예를 들어 로그인 중 가져온 토큰에 사용자 정보(예: 이름 및 전자 메일 주소)가 포함되어 있지만 응용 프로그램에서 그룹 구성원이나 사용자 관리자의 이름과 같은 정보를 요구할 수 있습니다. Graph API를 사용하여 테넌트의 디렉터리에서 이 정보를 가져올 수 있습니다. Graph API에 대한 자세한 내용은 [이 항목][이 항목]을 참조하십시오.

응용 프로그램에서 Azure AD Graph를 호출하려면 먼저 해당 응용 프로그램이 직접 자신을 인증하고 액세스 토큰을 가져와야 합니다. 액세스 토큰은 클라이언트 ID 및 클라이언트 암호로 응용 프로그램을 인증하여 가져옵니다. 다음 단계에서는 아래 방법을 보여 줍니다.

1.  생성된 프록시 클래스를 사용하여 Azure AD Graph 호출
2.  AAL(Azure 인증 라이브러리)을 사용하여 액세스 토큰 확보
3.  Azure AD Graph를 호출하여 테넌트 사용자 목록 가져오기

<div class="dev-callout"><strong>참고</strong><p>샘플 응용 프로그램 도우미 라이브러리인 Microsoft.IdentityModel.WAAD.Preview에는 자동 생성된 프록시 클래스(https://graph.windows.net/your-domain-name called GraphService에 대한 서비스 참조를 추가하여 만든 클래스)가 이미 포함되어 있습니다. 응용 프로그램은 이 프록시 클래스를 사용하여 Azure AD Graph 서비스를 호출합니다.</p></div>

### 1단계: 프록시 클래스를 사용하여 Azure AD Graph 호출

이 단계에서는 샘플 응용 프로그램을 사용하여 아래 방법을 보여 줍니다.

1.  테넌트 관련 Azure AD Graph 끝점 만들기
2.  끝점에서 프록시를 인스턴스화하여 Graph 호출
3.  요청에 Authorization 헤더 추가 및 토큰 확보

샘플 응용 프로그램에서 API에 대한 호출은 다음 코드와 같이 *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* 클래스의 GraphInterface 메서드로 처리됩니다.

    public GraphInterface()
    {
        // 1a: When the customer was signed in, we get a security token 
        // that contains a tenant id. Extract that here
        TenantDomainName = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/ws/2012/10/identity/claims/tenantid").Value;

        // 1b: We generate a URL (https://graph.windows.net/<CustomerDomainName>)
        // to access the Azure AD Graph API endpoint for the tenant 
        connectionUri = new Uri(string.Format(@"https://{0}/{1}", TenantUtils.Globals.endpoint, TenantDomainName));

        // 2: create an instance of the AzureAD Service proxy with the connection URL
        dataService = new DirectoryDataService(connectionUri);

        // This flags ignores the resource not found exception
        // If AzureAD Service throws this exception, it returns null
        dataService.IgnoreResourceNotFoundException = true;
        dataService.MergeOption = MergeOption.OverwriteChanges;
        dataService.AddAndUpdateResponsePreference = DataServiceResponsePreference.IncludeContent;

        // 3: This adds the default required headers to each request
        AddHeaders(GetAuthorizationHeader());
    }

### 2단계: Azure 인증 라이브러리를 사용하여 액세스 토큰 확보

샘플 응용 프로그램에서는 AAL(Azure 인증 라이브러리)을 사용하여 Graph API를 액세스하는 데 사용할 토큰을 확보합니다. 토큰 확보 프로세스는 *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* 클래스의 *GetAuthorizationHeader* 메서드로 관리되며 아래와 같습니다.

<div class="dev-callout"><strong>참고</strong><p>AAL은 NuGet 패키지로 사용할 수 있으며 Visual Studio에 설치할 수 있습니다.</p></div>

    /// <summary>
    /// Method to get the Oauth2 Authorization header from ACS
    /// </summary>
    /// <returns>AOauth2 Authorization header</returns>
    private string GetAuthorizationHeader()
    {
        // AAL values
        string fullTenantName = TenantUtils.Globals.StsUrl + TenantDomainName;
        string serviceRealm = string.Format("{0}/{1}@{2}", TenantUtils.Globals.GraphServicePrincipalId, TenantUtils.Globals.GraphServiceHost, TenantDomainName);
        string issuingResource = string.Format("{0}@{1}", Globals.ClientId, TenantDomainName);
        string clientResource = string.Format("{0}/{1}@{2}", Globals.ClientId, Globals.AppHostname, TenantDomainName);

        string authzHeader = null;
        AuthenticationContext _authContext = new AuthenticationContext(fullTenantName);

        try
        {
            ClientCredential credential = new ClientCredential(issuingResource, clientResource, Globals.ServicePrincipalKey);
            AssertionCredential _assertionCredential = _authContext.AcquireToken(serviceRealm, credential);
            authzHeader = _assertionCredential.CreateAuthorizationHeader();
        }
        catch (Exception ex)
        {
            AALException aex = ex as AALException;
            string a = aex.InnerException.Message;
        }

        return authzHeader;
    }

다음 정보는 위의 코드에 나온 대로 액세스 토큰을 확보하는 데 사용됩니다.

1.  응용 프로그램의 정보(ClientID, ServicePrincipalKey 및 AppHostname)
2.  Graph인 대상 정보(위에서는 ServiceRealm으로 참조됨)
3.  이전에 확보한 TenantDomainName

### 3단계: Azure AD Graph를 호출하여 사용자 목록 가져오기

*Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* 클래스의 다음 메서드는 이전에 생성한 *DataService* 프록시를 사용하여 테넌트의 모든 사용자 목록을 가져옵니다.

    public List<User> GetUsers()
    {
        // Add the page size using top
        var users = dataService.Users.AddQueryOption("$top", 20);

        // Execute the Query
        var userQuery = users.Execute();

        // Get the return users list
        return userQuery.ToList();
    }

이 메서드는 **HomeController.cs** 파일에서 호출되어 웹 응용 프로그램의 사용자 탭에 있는 사용자 목록을 보여 줍니다.

## <a name="publish"></a>5부: 응용 프로그램 게시

응용 프로그램을 철저히 테스트한 후에는 응용 프로그램 목록을 만들고 Azure 마켓플레이스에 응용 프로그램 목록을 게시할 수 있습니다. 이러한 단계는 Microsoft 판매자 대시보드에서 수행합니다.

<div class="dev-callout"><strong>참고</strong><p>사용 중인 앱에서 고객에 대한 청구 관계를 관리해야 합니다. Azure 마켓플레이스는 응용 프로그램의 웹 사이트에 대한 링크와 그에 대한 정보만 제공합니다.</p></div>

### 1단계: 응용 프로그램 매니페스트 및 앱 목록 만들기

앱 목록을 만들기 전에 응용 프로그램의 프로덕션 버전을 위해 새로운 클라이언트 ID 및 클라이언트 암호를 생성해야 합니다. 이 자습서의 1부에서 응용 프로그램의 테스트 버전을 위해 클라이언트 ID 및 클라이언트 암호를 생성했습니다. 이러한 단계를 반복하고 새로운 값을 사용하도록 응용 프로그램을 구성하면서 프로덕션 앱 도메인 및 앱 리디렉션 URL을 설정합니다.

그런 다음 응용 프로그램에서 고객 동의를 위해 요청하는 권한을 나열하는 응용 프로그램 매니페스트를 만들어야 합니다. 이 매니페스트는 XSD 파일로 제어되는 XML 형식으로 작성됩니다. 이 매니페스트는 생성 중인 응용 프로그램 목록의 일부로 업로드해야 합니다.

자습서의 1부에서 설명한 대로 3가지 권한 수준이 있습니다.

**DirectoryReader**: 디렉터리 데이터(예: 사용자 계정, 그룹 및 조직에 대한 정보)를 읽을 수 있는 권한을 부여합니다. SSO를 사용하도록 설정합니다.

**UserAccountAdministrator**: 데이터(예: 사용자, 그룹 및 조직에 대한 정보)를 읽고 쓸 수 있는 권한을 부여합니다. SSO를 사용하도록 설정합니다.

**None**: Single Sign-On을 사용하도록 설정하지만 디렉터리 데이터에 대한 액세스를 사용하지 않도록 설정합니다.

두 가지 응용 프로그램 매니페스트 예제가 아래에 포함되어 있습니다. 첫 번째는 SSO 전용 응용 프로그램에 대한 권한을 보여 주고, 두 번째는 읽기 전용 응용 프로그램에 대한 권한을 보여 줍니다.

    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="None" Scope="http://directory" />
      </AppPermissionRequests>
    </AppRequiredPermissions>


    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="Directory Reader" Scope="http://directory">
          <Reason culture="ko-KR" value="Needs to read the app"/>
        </AppPermissionRequest>
      </AppPermissionRequests>
    </AppRequiredPermissions>

위의 예제에서 *Policy* 특성은 요청 중인 응용 프로그램 권한 유형을 설명합니다. 현재는 "AppOnly" 권한 특성만 지원됩니다. 이 권한 유형은 응용 프로그램만 디렉터리에 액세스한다는 것을 나타냅니다.

옵션인 *Reason* 요소를 통해 (여러 문화권에서) 필요한 권한 수준의 근거를 지정할 수 있습니다. 이 텍스트는 고객이 응용 프로그램을 승인하거나 거부할 때 고객을 돕기 위해 동의 페이지에 표시됩니다.

새로운 클라이언트 ID 및 응용 프로그램 매니페스트를 사용하여 [Microsoft 판매자 대시보드에서 앱 추가][Microsoft 판매자 대시보드에서 앱 추가]의 지침에 따라 응용 프로그램 목록을 만들 수 있습니다. 응용 프로그램 목록을 만들 때 Azure AD 응용 프로그램 유형을 선택해야 합니다. 응용 프로그램 목록을 만든 후에는 "제출"을 클릭하여 Azure 마켓플레이스에 응용 프로그램을 게시합니다. 응용 프로그램이 승인될 때까지 기다려야 게시가 완료됩니다.

<div class="dev-callout"><strong>참고</strong><p>&quot;세금 및 지급액 정보를 추가&quot;하라는 메시지가 표시되는 경우 Microsoft를 통해서가 아니라 고객에게 직접 응용 프로그램을 판매하고 있으므로 이 단계를 건너뛸 수 있습니다.</p></div>

### 2단계: 테스트 완료 및 응용 프로그램을 공용으로 설정

응용 프로그램 목록이 승인된 후 응용 프로그램 전체를 다시 테스트해야 합니다. 예를 들어 응용 프로그램이 프로덕션 클라이언트 ID 및 클라이언트 암호로 업데이트되었는지 확인하십시오. 마지막으로 테스트 검사 목록을 실행하면서, 이제 동의 페이지에서 응용 프로그램 목록에 포함된 정보를 표시하는지 확인합니다.

## <a name="summary"></a>요약

이 자습서에서는 Azure AD와 다중 테넌트 응용 프로그램을 통합하는 방법에 대해 알아보았습니다. 이 프로세스에는 다음과 같은 3개 단계가 포함됩니다.

-   고객이 Azure AD를 사용하여 응용 프로그램에 등록할 수 있도록 설정
-   Azure AD에서 SSO(Single Sign-On) 사용
-   Azure AD Graph API를 사용하여 고객의 디렉터리 데이터 쿼리

Azure AD와 통합하면 고객은 이미 유지 관리되고 있는 ID 관리 시스템을 사용하여 응용 프로그램을 등록하고 로그인할 수 있습니다. 따라서 응용 프로그램으로 별도의 ID 관리 작업을 수행할 필요가 없거나 그럴 필요가 줄어듭니다. 이 기능을 통해 응용 프로그램을 사용할 때 고객에게 보다 능률적인 환경을 제공할 수 있으며, 관리 작업을 수행하지 않아도 됩니다.

  [ID 페이지]: http://www.windowsazure.com/ko-KR/home/features/identity/
  [여기서 다운로드]: http://go.microsoft.com/fwlink/?LinkId=271213
  [Visual Studio의 포트 할당]: http://msdn.microsoft.com/ko-KR/library/ms178109(v=vs.100).aspx
  [Visual Studio 2012]: http://www.microsoft.com/visualstudio/eng/downloads
  [WCF Data Services for OData]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [소개]: #introduction
  [1부: Azure AD 액세스를 위해 클라이언트 ID 가져오기]: #getclientid
  [2부: 고객이 Azure AD를 사용하여 등록할 수 있도록 설정]: #enablesignup
  [3부: Single Sign-On 사용]: #enablesso
  [4부: Access Azure AD Graph]: #accessgraph
  [5부: 응용 프로그램 게시]: #publish
  [요약]: #summary
  [Microsoft 판매자 대시보드]: https://sellerdashboard.microsoft.com/
  [계정 프로필을 회사 또는 개인으로 만들]: http://msdn.microsoft.com/ko-KR/library/jj552460.aspx
  [Microsoft 판매자 대시보드에서 클라이언트 ID 및 암호 만들기]: http://msdn.microsoft.com/ko-KR/library/jj552461.aspx
  [로그인]: ./media/active-directory-dotnet-integrate-multitent-cloud-applications/login.png
  [Azure AD 테넌트를 가져]: http://g.microsoftonline.com/0AX00en/5
  [contoso]: https://contoso.myapp.com
  [이 항목]: http://msdn.microsoft.com/ko-KR/library/windowsazure/hh974476.aspx
  [Microsoft 판매자 대시보드에서 앱 추가]: http://msdn.microsoft.com/ko-KR/library/jj552465.aspx
