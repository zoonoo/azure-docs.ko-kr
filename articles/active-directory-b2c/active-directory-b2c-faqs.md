<properties
	pageTitle="Azure Active Directory B2C 미리 보기: FAQ | Microsoft Azure"
	description="Azure Active Directory B2C에 대해 자주 묻는 질문과 대답입니다."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: FAQ

이 페이지는 Azure AD(Azure Active Directory) B2C 미리 보기에 대한 자주 묻는 질문에 응답합니다. 업데이트를 계속 확인합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### 기존의 직원 기반 Azure AD 테넌트에서 Azure AD B2C 기능을 사용할 수 있나요?

현재 Azure AD B2C 기능은 기존 Azure AD 테넌트에 설정할 수 없습니다. 즉, Azure AD B2C 기능을 사용하여 소비자를 관리할 수 있도록 별도의 테넌트를 만드는 것이 좋습니다.

### Azure AD B2C를 사용하여 소셜 로그인(Facebook 및 Google+)을 Office 365에 제공할 수 있나요?

Microsoft Azure AD B2C는 Microsoft Office 365와 함께 사용할 수 없습니다. 일반적으로 임의 SaaS 앱(Office 365, Salesforce, Workday 등)에 인증을 제공하는 데 사용할 수 없습니다. 소비자 지향 웹 및 모바일 응용 프로그램에 대해서만 ID 및 액세스 관리를 제공하고 직원 또는 파트너 시나리오에 대해서는 적용되지 않습니다.

### Azure AD B2C에서 로컬 계정은 무엇인가요? Azure AD의 회사 또는 학교 계정과 어떻게 다른가요?

Azure AD 테넌트에서 테넌트의 모든 사용자(기존 Microsoft 계정을 가진 사용자 제외)는 형식 `<xyz>@<tenant domain>`의 메일 주소를 사용하여 로그인합니다. 여기서 `<tenant domain>`는 테넌트 또는 초기 `<...>.onmicrosoft.com` 도메인에서 확인된 도메인 중 하나입니다. 이 계정 유형은 회사 또는 학교 계정입니다.

Azure AD B2C 테넌트에서 대부분의 앱은 사용자가 임의의 메일 주소(예: joe@comcast.net, bob@gmail.com, sarah@contoso.com 또는 jim@live.com))를 사용하여 로그인하도록 합니다. 이 계정 유형은 로컬 계정입니다. 또한 현재 임의의 사용자 이름(일반 문자열)을 로컬 계정(예: joe, bob, sarah 또는 jim)으로 지원합니다. Azure AD B2C 서비스에서 이러한 두 로컬 계정 형식 중 하나를 선택할 수 있습니다.

### 지금 어떤 소셜 ID 공급자를 지원하나요? 나중에 어떤 공급자를 지원하려는 계획입니까?

현재 Facebook, Google+, LinkedIn 및 Amazon을 지원합니다. 고객의 요구에 따라 다른 인기 있는 소셜 ID 공급자에 대한 지원을 추가합니다.

### 범위를 구성하여 다양한 소셜 ID 공급자에서 소비자에 대한 자세한 정보를 수집할 수 있나요?

아니요, 하지만 이 기능은 우리의 로드맵입니다. 지원되는 소셜 ID 공급자 집합에 사용되는 기본 범위는 다음과 같습니다.

- Facebook: 전자 메일
- Google+: 전자 메일
- Microsoft 계정: openid 메일 프로필
- Amazon: 프로필
- LinkedIn: r\_emailaddress, r\_basicprofile

### 내 응용 프로그램을 Azure AD B2C와 함께 작업하려면 Azure에서 실행해야 하나요?

아니요, 어디서나 응용 프로그램을 호스팅할 수 있습니다.(클라우드 또는 온-프레미스) Azure AD B2C와 상호 작용하기 위해 필요한 것은 공개적으로 액세스할 수 있는 끝점에서 HTTP 요청을 발신 및 수신하는 기능입니다.

### 여러 Azure AD B2C 테넌트가 있습니다. Azure 포털에서 어떻게 관리할 수 있나요?

각 Azure AD B2C 테넌트는 Azure 포털에 고유의 B2C 기능 블레이드가 있습니다. Azure 포털에서 특정 테넌트의 B2C 기능 블레이드로 이동할 수 있는 방법을 알아보려면 [Azure AD B2C 미리 보기: 응용 프로그램 등록](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)을 참조하세요. Azure 포털에서 Azure AD B2C 디렉터리를 전환하면 대부분의 브라우저에서 B2C 기능 블레이드를 열어둘 수 없습니다.

### Azure AD B2C에서 보낸 확인 메일(콘텐츠 및 "From:" 필드)을 어떻게 사용자 지정하나요?

[회사 브랜딩 기능](../active-directory/active-directory-add-company-branding.md)을 사용하여 확인 메일의 콘텐츠를 사용자 지정합니다. "From:" 필드는 지원을 통해 변경할 수 있습니다.

### 데이터베이스에서 Azure AD B2C로 기존 사용자 이름, 암호 및 프로필을 어떻게 마이그레이션할 수 있나요?

Azure AD Graph API를 사용하여 마이그레이션 도구를 작성할 수 있습니다. 자세한 내용은 [Graph API 샘플](active-directory-b2c-devquickstarts-graph-dotnet.md)을 참조하세요. 나중에 다양한 마이그레이션 옵션 및 창의적인 도구를 제공합니다.

### Azure AD B2C의 로컬 계정에 사용되는 암호 정책은 무엇인가요?

로컬 계정에 대한 Azure AD B2C 암호 정책은 Azure AD 정책을 기반으로 합니다. Azure AD B2C는 “강력한" 암호 보안 수준을 사용하며 모든 암호가 만료되지 않습니다. 자세한 내용은 [Azure AD 암호 정책](https://msdn.microsoft.com/library/azure/jj943764.aspx)을 참조하세요.

### Azure AD Connect를 사용하여 온-프레미스 Active Directory에 저장된 소비자 ID를 Azure AD B2C에 마이그레이션할 수 있나요?

아니요, Azure AD Connect는 Azure AD B2C와 함께 작동하지 않습니다. 나중에 다양한 마이그레이션 옵션 및 창의적인 도구를 제공합니다.

### Azure AD B2C는 Microsoft Dynamics와 같은 CRM 시스템과 함께 작동합니까?

현재는 아닙니다. 이러한 시스템을 통합하는 것이 로드맵입니다.

### Azure AD B2C가 SharePoint 온-프레미스 2016 또는 이전 버전과 함께 작동하나요?

현재는 아닙니다. Azure AD B2C는 SharePoint 온-프레미스 요구에 따라 빌드된 포털 및 전자상거래 응용 프로그램의 SAML 1.1 토큰을 지원하지 않습니다. Azure AD B2C는 Sharepoint 외부 파트너 공유 시나리오에 해당하지 않습니다. 대신 [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx)를 참조하세요.

### 외부 ID를 관리하려면 Azure AD B2C 또는 B2B를 사용해야 합니까?

외부 ID 시나리오에 적절한 기능을 적용하는 방법에 대한 자세한 내용은 이 문서에서 [외부 ID](../active-directory/active-directory-b2b-compare-external-identities.md)를 참조하세요.

### Azure AD B2C가 제공하는 보고 및 감사 기능은 무엇인가요? Azure AD Premium과 동일한가요?

아니요, Azure AD B2C는 Azure AD Premium과 동일한 보고서 집합을 지원하지 않습니다. Azure AD B2C는 기본 보고 및 감사 API를 곧 출시합니다.

### Azure AD B2C에서 제공하는 페이지의 UI를 지역화할 수 있습니까? 어떤 언어가 지원되나요?

현재 Azure AD B2C는 영어에 최적화되었습니다. 지역화 기능을 최대한 빨리 롤아웃할 계획입니다.

### Azure AD B2C에서 제공하는 등록 및 로그인 페이지에 고유 URL을 사용할 수 있나요? 예를 들어 login.microsoftonline.com에서 login.contoso.com으로 URL을 변경할 수 있나요?

현재는 아닙니다. 이 기능은 우리의 로드맵입니다. 또한 Azure 클래식 포털에서 테넌트의 **도메인** 탭에서 해당 도메인 확인은 해당 내용을 수행하지 않습니다.

### Azure AD B2C 테넌트를 삭제하려면 어떻게 해야 하나요?

Azure AD B2C 테넌트를 삭제하려면 다음 단계를 수행합니다.

- [Azure 클래식 포털](https://manage.windowsazure.com/)에 구독 관리자로 로그인합니다. (이것은 동일한 작업이거나 학교 계정 또는 Azure에 등록 하는데 사용한 동일한 Microsoft 계정입니다.)
- 왼쪽에서 Active Directory 확장으로 이동하고 B2C 테넌트를 클릭합니다.
- **응용 프로그램** 탭을 클릭합니다.
- **표시** 드롭다운 필드에서 **회사가 보유한 응용 프로그램**을 선택하고 확인 표시를 클릭합니다.
- 아래에 나열된 **b2c-extensions-app**이라는 응용 프로그램을 확인할 수 있습니다. 페이지의 아래쪽에서 **삭제**를 클릭한 다음 메시지가 나타나면 **예**를 클릭합니다.
- Active Directory 확장으로 다시 이동하고 B2C 테넌트를 선택합니다.
- 페이지 맨 아래에서 **삭제**를 클릭합니다. 화면의 지시에 따라 프로세스를 완료합니다.

B2C 테넌트에서 **사용자**, **응용 프로그램** 또는 기타 개체를 이미 만든 경우 위의 단계를 수행하기 전에 삭제해야 합니다.

### Enterprise Mobility Suite의 일부로 Azure AD B2C를 가져올 수 있나요?

아니요, Azure AD B2C는 종량제 Azure 서비스이며 Enterprise Mobility Suite의 일부가 아닙니다.

### Azure AD B2C에서 발생한 문제를 보고하려면 어떻게 해야 합니까?

[Azure Active Directory B2C에 대한 파일 지원 요청](active-directory-b2c-support.md)을 참조하세요.

### 언제 Azure AD B2C를 일반적으로 사용할 수 있나요?

이번에는 일반적으로 사용할 수 있는 날짜에 대한 정보를 제공할 수 없습니다.

## 자세한 정보

또한 현재 [미리 보기 제한, 제한 사항 및 제약 조건](active-directory-b2c-limitations.md)을 검토하려 합니다.

<!---HONumber=AcomDC_0518_2016-->