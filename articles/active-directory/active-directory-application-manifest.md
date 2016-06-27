<properties
   pageTitle="Azure Active Directory 응용 프로그램 매니페스트 이해 | Microsoft Azure"
   description="Azure AD 테넌트의 응용 프로그램 ID 구성을 나타내고 OAuth 권한 부여, 승인 환경 등을 용이하게 하는 데 사용되는 Azure Active Directory 응용 프로그램 매니페스트를 사용하는 방법에 대한 자세한 정보를 다룹니다."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/06/2016"
   ms.author="dkershaw;bryanla"/>

# Azure Active Directory 응용 프로그램 매니페스트 이해

Azure Active Directory(AD)와 통합된 응용 프로그램은 Azure AD 테넌트로 등록되어 있어야 하며 이는 응용 프로그램에 영구 ID 구성을 제공합니다. 이 구성을 런타임 시 참조하며 Azure AD 통해 응용 프로그램에서 아웃소싱 및 브로커 인증/권한 부여를 허용하는 시나리오를 사용할 수 있습니다. Azure AD 응용 프로그램 모델에 대한 자세한 내용은 [응용 프로그램 추가, 업데이트 및 제거][ADD-UPD-RMV-APP] 문서를 참조하세요.

## 응용 프로그램의 ID 구성 업데이트

실제로 응용 프로그램의 ID 구성에 속성을 업데이트하는 데 사용할 수 있는 여러 옵션이 있으며 다음을 포함하여 기능 및 난이도에 따라 다양합니다.

- **[Azure 클래식 포털의][AZURE-CLASSIC-PORTAL] 웹 사용자 인터페이스**를 사용하면 응용 프로그램의 가장 일반적인 속성을 업데이트할 수 있습니다. 응용 프로그램의 속성을 업데이트할 때 빠르고 오류가 적은 방법이지만 다음 두 방법과 마찬가지로 모든 속성에 액세스할 모든 권한은 없습니다.
- Azure 클래식 포털에서 노출되지 않은 속성을 업데이트해야 하는 고급 시나리오의 경우 **응용 프로그램 매니페스트**를 수정할 수 있습니다. 이것이 이 문서의 초점이며 다음 섹션에서 보다 자세히 설명합니다.
- 또한 **[Graph API][GRAPH-API]를 사용하는 응용 프로그램을 작성**하여 응용 프로그램을 업데이트할 수 있으며 이는 가장 많은 노력이 필요합니다. 하지만 자동화된 방식으로 정기적으로 관리 소프트웨어를 작성하거나 응용 프로그램 속성을 업데이트해야 하는 경우 매력적인 옵션입니다.

## 응용 프로그램 매니페스트를 사용하여 응용 프로그램의 ID 구성 업데이트
[Azure 클래식 포털][AZURE-CLASSIC-PORTAL]을 통해 JSON 파일 표현을 다운로드 및 업로드하여 응용 프로그램의 ID 구성을 관리할 수 있으며 이를 응용 프로그램 매니페스트라고 합니다. 어떤 실제 파일도 디렉터리에 저장되지 않습니다. 응용 프로그램 매니페스트는 Azure AD Graph API 응용 프로그램 엔터티의 단순한 HTTP GET 작업이며 업로드는 응용 프로그램 엔터티의 HTTP PATCH 작업입니다.

결과적으로 응용 프로그램 매니페스트의 형식 및 속성을 이해하기 위해 Graph API를 [응용 프로그램 엔터티][APPLICATION-ENTITY] 설명서를 참조해야 합니다. 응용 프로그램 매니페스트 업로드를 통해 수행할 수 있는 업데이트의 예는 다음을 포함합니다.

- 웹 API에서 노출된 **사용 권한 범위(oauth2Permissions)**를 선언합니다. 사용 권한 범위를 위임한 oauth2Permissions를 사용하여 사용자 가장을 구현하는 방법에 대한 정보는 [Azure Active Directory와 응용 프로그램 통합][INTEGRATING-APPLICATIONS-AAD]에서 "웹 API를 다른 응용 프로그램에 노출" 항목을 참조하세요. 이전에 언급했듯이 [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION] 형식의 컬렉션인 oauth2Permissions 속성을 포함하여 Graph API [엔터티 및 복합 형식 참조][APPLICATION-ENTITY] 참조 문서에서 모든 응용 프로그램 엔터티 속성을 설명합니다.
- **앱에서 노출된 응용 프로그램 역할(appRoles)을 선언**합니다. 응용 프로그램 엔터티의 appRoles 속성은 [AppRole][APPLICATION-ENTITY-APP-ROLE] 형식의 컬렉션입니다. 구현 예제는 [클라우드 응용 프로그램에서 Azure AD를 사용한 역할 기반 액세스 제어][RBAC-CLOUD-APPS-AZUREAD] 문서를 참조하세요.
- 리소스/웹 API에 지정된 클라이언트 응용 프로그램에 대한 동의와 논리적으로 연결하도록 하는 **알려진 클라이언트 응용 프로그램(knownClientApplications)을 선언**합니다.
- Azure AD를 요청하여 로그인한 사용자에 대해 **그룹 멤버 자격 클레임을 제시**합니다(groupMembershipClaims). 참고: 이를 구성하여 추가적으로 사용자의 디렉터리 역할 멤버 자격에 대한 문제를 제기할 수 있습니다. 구현 예제는 [AD 그룹을 사용하여 클라우드 응용 프로그램에서 권한 부여][AAD-GROUPS-FOR-AUTHORIZATION] 문서를 참조하세요.
- **응용 프로그램이 OAuth 2.0 암시적 허용 흐름을 지원**하도록 합니다.(oauth2AllowImplicitFlow) 이 형식의 허용 흐름은 포함된 JavaScript 웹 페이지 또는 단일 페이지 응용 프로그램(SPA)을 통해 사용됩니다. 암시적 권한 부여에 관한 자세한 내용은 [Azure Active Directory에서 OAuth2 암시적 권한 부여 흐름 이해][IMPLICIT-GRANT]를 참조하세요.
- **X509 인증서를 비밀 키로 사용하도록 허용**(keyCredentials). 구현 예제는 [Office 365에서 서비스 및 디먼 앱 빌드][O365-SERVICE-DAEMON-APPS]와 [Azure 리소스 관리자 API 인증에 대한 개발자 가이드][DEV-GUIDE-TO-AUTH-WITH-ARM] 문서를 참조하세요.
- 응용 프로그램에 대한 **새 앱 ID URI 추가**(identifierURIs). 앱 ID Uri는 Azure AD 테넌트 내에서 (또는 확인된 사용자 지정 도메인을 통해 한정될 경우 다중 테넌트 시나리오에 대한 여러 Azure AD 테넌트 사이에) 응용 프로그램을 고유하게 식별하는 데 사용됩니다. 리소스 응용 프로그램에 대한 사용 권한을 요청하거나 리소스 응용 프로그램에 대한 액세스 토큰을 획득하는 경우에 사용됩니다. 이 요소를 업데이트하면 응용 프로그램의 홈 테넌트에 존재하는 해당 서비스 주체의 servicePrincipalNames 컬렉션에 동일한 업데이트가 이루어집니다.

또한 응용 프로그램 매니페스트는 응용 프로그램 등록의 상태를 추적하는 좋은 방법을 제공합니다. JSON 형식에서 사용할 수 있기 때문에 파일 표시는 응용 프로그램의 소스 코드와 함께 소스 제어에 체크 인할 수 있습니다.

## 단계별 예제
이제 응용 프로그램 매니페스트를 통해 응용 프로그램의 ID 구성을 업데이트하는 데 필요한 단계를 살펴보겠습니다. 위에 주어진 예제 중 하나를 중점적으로 다루며 이는 리소스 응용 프로그램에 새 사용 권한 범위를 선언하는 방법을 보여줍니다.

1. [Azure 클래식 포털][AZURE-CLASSIC-PORTAL]을 탐색하고 서비스 관리자 또는 공동 관리자 권한이 있는 계정으로 로그인합니다.

2. 인증된 후에 아래로 스크롤하여 왼쪽된 탐색 패널(1)에서 Azure "Active Directory" 확장을 선택한 다음 응용 프로그램이 등록된(2) Azure AD 테넌트를 클릭합니다.

    ![Azure AD 테넌트 선택][SELECT-AZURE-AD-TENANT]

3. 디렉터리 페이지를 표시하면 페이지 맨 위의 "응용 프로그램"(1)을 클릭하여 테넌트에 등록된 응용 프로그램의 목록을 봅니다. 그런 다음 목록에서 업데이트하려는 응용 프로그램을 찾고 그것을(2) 클릭합니다.

    ![Azure AD 테넌트 선택][SELECT-AZURE-AD-APP]

4. 응용 프로그램의 기본 페이지를 선택했으므로 페이지의 아래쪽에서(1) "매니페스트 관리" 기능을 확인합니다. 이 링크를 클릭하면 JSON 매니페스트 파일을 업로드하거나 다운로드하 라는 메시지가 표시됩니다. "매니페스트 영역 다운로드"(3)를 클릭하여 확인할 창을 표시하는 다운로드 확인 대화 상자의 뒤에 바로 이어지는 "다운로드 매니페스트"(2)를 클릭한 다음 파일을 로컬로 열거나 저장합니다(4).

    ![매니페스트 관리, 다운로드 옵션][MANAGE-MANIFEST-DOWNLOAD]

    ![메타데이터 다운로드][DOWNLOAD-MANIFEST]

5. 이 예에서 파일을 로컬에서 저장하면 이를 편집기에서 열고 JSON을 변경하고 다시 저장할 수 있습니다. Visual Studio JSON 편집기 내에서 JSON 구조체 모양은 다음과 같습니다. 앞에서 언급한 대로 [응용 프로그램 엔터티][APPLICATION-ENTITY]에 대한 스키마를 수행합니다.

    ![JSON 매니페스트 업데이트][UPDATE-MANIFEST]

    예를 들어 리소스 응용 프로그램(API)에 "Employees.Read.All"이라는 새 사용 권한을 구현/노출하려는 경우 간단히 새로운/두 번째 요소를 oauth2Permissions 컬렉션인 IE에 추가합니다.

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    항목은 고유해야 하고 따라서 `"id"` 속성에 대한 새로운 고유 전역 ID(GUID)를 생성해야 합니다. 이 경우에 `"type": "User"`를 지정했기 때문에 이 사용 권한은 리소스/API 응용 프로그램이 등록된 Azure AD 테넌트에서 인증된 계정에 의해 승인할 수 있으며 이는 계정을 대신하여 액세스하는 클라이언트 응용 프로그램 사용 권한을 부여합니다. 설명 및 표시 이름 문자열은 승인하는 동안 Azure 클래식 포털에 표시하는 데 사용됩니다.

6. 매니페스트의 업데이트가 완료되면 Azure 클래식 포털의 Azure AD 응용 프로그램 페이지로 돌아가서 "매니페스트 관리" 기능(1)을 다시 클릭하지만 이번에는 "매니페스트 업로드" 옵션(2)을 선택합니다. 다운로드와 유사하게 두 번째 대화 상자가 표시되어 JSON 파일의 위치에 대한 메시지를 표시합니다. "파일 찾아보기..."(3)를 클릭한 다음 "업로드할 파일 선택" 대화 상자를 사용하여 JSON 파일(4)을 선택하고 "열기"를 누릅니다. 대화 상자가 사라지면 "OK" 확인 표시(5)를 선택하고 매니페스트가 업로드됩니다.

    ![매니페스트 관리, 업로드 옵션][MANAGE-MANIFEST-UPLOAD]

    ![JSON 매니페스트 업로드][UPLOAD-MANIFEST]

    ![매니페스트 JSON 업로드 - 확인][UPLOAD-MANIFEST-CONFIRM]

이제 매니페스트를 저장했으므로 위에 추가한 새 사용 권한에 대한 등록된 클라이언트 응용 프로그램을 제공할 수 있습니다. 하지만 이번에는 클라이언트 응용 프로그램의 매니페스트를 편집하는 대신 Azure 클래식 포털의 웹 UI를 사용할 수 있습니다.

1. 먼저 새로운 API에 대한 액세스를 추가하려는 클라이언트 응용 프로그램의 "구성" 페이지로 이동하고 "응용 프로그램 추가" 단추를 클릭합니다.
2. 그런 다음 테넌트의 등록된 리소스 응용 프로그램(API) 목록으로 보여집니다. 리소스 응용 프로그램의 이름 옆에 더하기/+ 기호를 클릭하여 선택합니다.  
3. 그런 다음 오른쪽 아래에 있는 확인 표시를 클릭합니다.
4. 클라이언트 구성 페이지의 "응용 프로그램 추가" 섹션을 반환할 때 새 리소스 응용 프로그램이 목록에 표시됩니다. 해당 행의 오른쪽 위로 "위임된 권한" 섹션을 마우스 포인터로 가리키는 경우 드롭 다운 목록이 나타납니다. 목록을 클릭한 다음 클라이언트의 요청된 사용 권한 목록에 추가하기 위해 새 사용 권한을 선택합니다. 참고: 이 새 사용 권한은 "requiredResourceAccess" 컬렉션 속성에서 클라이언트 응용 프로그램의 ID 구성에 저장됩니다.

![다른 응용 프로그램에 대한 권한][PERMS-TO-OTHER-APPS]

![다른 응용 프로그램에 대한 권한][PERMS-SELECT-APP]

![다른 응용 프로그램에 대한 권한][PERMS-SELECT-PERMS]

이것으로 끝입니다. 이제 응용 프로그램은 새 ID 구성을 사용하여 실행됩니다.

## 다음 단계
아래 DISQUS 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_0615_2016-->