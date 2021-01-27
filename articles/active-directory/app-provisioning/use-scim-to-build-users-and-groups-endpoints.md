---
title: Azure AD에서 앱에 대 한 사용자 프로 비전을 위한 SCIM 끝점 빌드
description: SCIM(도메인 간 ID 관리를 위한 시스템)은 자동 사용자 프로비저닝을 표준화합니다. SCIM 엔드포인트를 개발하고, SCIM API를 Azure Active Directory와 통합하고, 프로비저닝 사용자 및 그룹을 클라우드 애플리케이션으로 자동화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900243"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>자습서: 샘플 SCIM 끝점 개발

새 끝점을 처음부터 빌드할 수 없으므로 [Scim](https://aka.ms/scimoverview)을 시작 하기 위한 몇 가지 [참조 코드](https://aka.ms/scimreferencecode) 를 만들었습니다. 이 자습서에서는 Azure에서 SCIM 참조 코드를 배포 하 고 postman을 사용 하 여 테스트 하거나 Azure AD SCIM 클라이언트와 통합 하는 방법을 설명 합니다. 단 5 분 내에 코드 없이 SCIM 끝점을 실행 하 여 실행할 수 있습니다. 이 자습서는 SCIM을 시작 하거나 SICM 끝점을 테스트 하는 데 관심이 있는 개발자를 대상으로 합니다. 

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 참조 코드 다운로드
> * Azure에서 SCIM 끝점 배포
> * SCIM 끝점 테스트

포함 된 끝점 기능은 다음과 같습니다.

|엔드포인트|설명|
|---|---|
|`/User`|사용자 리소스에 대 한 CRUD 작업 수행: **만들기**, **업데이트**, **삭제**, **가져오기**, **목록**, **필터**|
|`/Group`|그룹 리소스에 대 한 CRUD 작업 수행: **만들기**, **업데이트**, **삭제**, **가져오기**, **목록**, **필터**|
|`/Schemas`|하나 이상의 지원 되는 스키마를 검색 합니다.<br/><br/>각 서비스 공급자가 지 원하는 리소스의 특성 집합은 다를 수 있습니다. 예를 들어 서비스 공급자 A는 "이름", "제목" 및 "전자 메일"을 지원 하 고, 서비스 공급자 B는 "이름", "제목" 및 "phoneNumbers"를 지원 합니다.|
|`/ResourceTypes`|지원 되는 리소스 종류를 검색 합니다.<br/><br/>각 서비스 공급자가 지 원하는 리소스의 수와 유형은 다를 수 있습니다. 예를 들어 서비스 공급자 A는 사용자를 지원 하 고 서비스 공급자 B는 사용자 및 그룹을 지원 합니다.|
|`/ServiceProviderConfig`|서비스 공급자의 SCIM 구성 검색<br/><br/>각 서비스 공급자가 지 원하는 SCIM 기능은 다를 수 있습니다. 예를 들어 서비스 공급자 A는 패치 작업을 지원 하 고 서비스 공급자 B는 패치 작업 및 스키마 검색을 지원 합니다.|

## <a name="download-the-reference-code"></a>참조 코드 다운로드

다운로드할 [참조 코드](https://github.com/AzureAD/SCIMReferenceCode) 에는 다음 프로젝트가 포함 되어 있습니다.

- SCIM API를 빌드하고 프로 비전 하는 .NET Core MVC web API **Microsoft.SystemForCrossDomainIdentityManagement**
- **Microsoft scim. WebHostSample**, scim 끝점의 작업 예제

프로젝트에는 다음 폴더 및 파일이 포함 되어 있습니다.

|파일/폴더|설명|
|-|-|
|**스키마** 폴더| 공유 기능에 대해 스키마 화 된 같은 일부 추상 클래스와 함께 **사용자** 및 **그룹** 리소스에 대 한 모델입니다.<br/><br/> **사용자** 및 **그룹** 의 복합 특성에 대 한 클래스 정의를 포함 하는 **Attributes** 폴더 (예: 주소)|
|**서비스** 폴더 | 리소스 쿼리 및 업데이트 방법과 관련 된 작업에 대 한 논리를 포함 합니다.<br/><br/> 참조 코드에는 사용자 및 그룹을 반환 하는 서비스가 있습니다.<br/><br/>**Controllers** 폴더에는 다양 한 scim 끝점이 포함 되어 있습니다. 리소스 컨트롤러는 리소스 (**GET**, **POST**, **PUT**, **PATCH**, **DELETE**)에 대 한 CRUD 작업을 수행 하는 HTTP 동사를 포함 합니다. 컨트롤러는 서비스를 사용 하 여 작업을 수행 합니다.|
|**프로토콜** 폴더|SCIM RFC에 따라 리소스가 반환 되는 방식과 관련 된 작업에 대 한 논리를 포함 합니다. 예를 들면 다음과 같습니다.<br/><ul><li>여러 리소스를 목록으로 반환 합니다.</li><li>필터를 기반으로 특정 리소스만 반환 합니다.</li><li>단일 필터의 연결 된 목록 목록으로 쿼리를 설정 합니다.</li><li>값 경로와 관련 된 특성을 사용 하 여 패치 요청을 작업으로 설정 합니다.</li><li>리소스 개체에 변경 내용을 적용 하는 데 사용할 수 있는 작업의 유형을 정의 합니다.</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| 샘플 소스 코드입니다.|
|`Microsoft.SCIM.WebHostSample`| SCIM 라이브러리의 샘플 구현입니다.|
|*. .gitignore*|커밋 시 무시할 항목을 정의 합니다.|
|*CHANGELOG.md*|샘플에 대 한 변경 내용 목록입니다.|
|*CONTRIBUTING.md*|샘플에 기여 하기 위한 지침입니다.|
|*README.md*|이 **추가 정보** 파일입니다.|
|*사용권이*|샘플에 대 한 라이선스입니다.|

> [!NOTE]
> 이 코드는 SCIM 끝점 빌드를 시작 하는 데 도움을 주기 위한 것으로 서, 있는 **그대로** 제공 됩니다. 포함 된 참조에는 활성 유지 및 지원 기능이 보장 되지 않습니다.
>
> 이 프로젝트는 [Microsoft 오픈 소스 준수 사항](https://opensource.microsoft.com/codeofconduct/)을 채택했습니다. 커뮤니티의 이러한 [기여](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) 는 리포지토리를 작성 하 고 유지 관리 하는 데 도움이 되며 다른 오픈 소스 기여와 마찬가지로 Cla (기여자 사용권 계약)에 동의 하 게 됩니다. 이 계약은 사용자가 보유 하 고 있는 권한을 부여 하 고 사용자의 기여를 사용 하는 권한을 부여 합니다. 자세한 내용은 [Microsoft 오픈 소스](https://cla.opensource.microsoft.com)를 참조 하십시오.
>
> 자세한 내용은 [준수 사항 FAQ](https://opensource.microsoft.com/codeofconduct/faq/)를 참조하고, 추가 질문이나 의견이 있는 경우에는 [opencode@microsoft.com](mailto:opencode@microsoft.com)으로 문의하세요.

###  <a name="use-multiple-environments"></a>다양한 환경 사용하기

포함 된 SCIM 코드는 ASP.NET Core 환경을 사용 하 여 개발 및 배포 후에 사용 하기 위한 권한 부여를 제어 합니다. [ASP.NET Core에서 여러 환경 사용](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1)을 참조 하세요.

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>Azure에서 SCIM 끝점 배포

여기에 제공 된 단계는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 및 [Azure 앱 서비스](https://docs.microsoft.com/azure/app-service/)를 사용 하 여 scim 끝점을 서비스에 배포 합니다. SCIM 참조 코드를 로컬로 실행 하거나 온-프레미스 서버에서 호스팅하거나 다른 외부 서비스에 배포할 수도 있습니다. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>솔루션을 열고 Azure App Service에 배포

1. GitHub에서 [참조 코드로](https://github.com/AzureAD/SCIMReferenceCode) 이동 하 고 **복제 또는 다운로드** 를 선택 합니다.

1. **바탕 화면에서 열거나**, 링크를 복사 하 고, **Visual Studio** 를 열고, **코드 복제 또는 체크 아웃** 을 선택 하 여 복사 된 링크를 입력 하 고 로컬 복사본을 만듭니다.

1. **Visual Studio** 에서 호스팅 리소스에 대 한 액세스 권한이 있는 계정에 로그인 해야 합니다.

1. **솔루션 탐색기** 에서 *microsoft* 파일을 열고 *microsoft* 파일을 마우스 오른쪽 단추로 클릭 합니다. **게시** 를 선택합니다.

    ![클라우드 게시](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > 이 솔루션을 로컬로 실행 하려면 프로젝트를 두 번 클릭 하 고 **IIS Express** 를 선택 하 여 로컬 호스트 URL이 있는 웹 페이지로 프로젝트를 시작 합니다.

1. **프로필 만들기** 를 선택 하 고 **App Service** 및 **새로 만들기** 가 선택 되어 있는지 확인 합니다.

    ![클라우드 게시 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. 대화 상자 옵션을 단계별로 실행 하 고 앱 이름을 원하는 이름으로 바꿉니다. 이 이름은 앱과 SCIM 끝점 URL에 모두 사용 됩니다.

    ![클라우드 게시 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 사용할 리소스 그룹을 선택 하 고 **게시** 를 선택 합니다.

1. **Azure 앱 Services** 구성에서 응용 프로그램으로 이동 하  >   고 **새 응용 프로그램 설정** 을 선택 하 여 값을 사용 하 여 *Token__TokenIssuer* 설정을 추가 합니다 `https://sts.windows.net/<tenant_id>/` . 을 `<tenant_id>` AZURE AD tenant_id로 바꾸고 [postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)을 사용 하 여 scim 끝점을 테스트 하려는 경우에는 값을 사용 하 여 *ASPNETCORE_ENVIRONMENT* 설정도 추가 합니다 `Development` . 

   ![appservice 설정](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Azure Portal에서 엔터프라이즈 응용 프로그램을 사용 하 여 끝점을 테스트 하는 경우 환경을 `Development` 테스트 또는 변경 하기 위해 끝점에서 생성 된 토큰을 제공 하 고, `/scim/token` `Production` [Azure Portal](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)엔터프라이즈 응용 프로그램에서 토큰 필드를 빈 상태로 유지 하도록 선택 합니다. 

이것으로 끝입니다. 이제 SCIM 끝점이 게시 되 고 Azure App Service URL을 사용 하 여 SCIM 끝점을 테스트할 수 있습니다.

## <a name="test-your-scim-endpoint"></a>SCIM 끝점 테스트

SCIM 끝점에 대 한 요청은 권한 부여가 필요 하 고 SCIM 표준은 쿠키, 기본 인증, TLS 클라이언트 인증 또는 [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)에 나열 된 방법 등 인증 및 권한 부여에 대 한 여러 옵션을 유지 합니다.

OAuth와 같은 보다 안전한 방법을 사용 하 여 사용자 이름/암호와 같은 안전 하지 않은 메서드를 방지 해야 합니다. Azure AD는 긴 전달자 토큰 (갤러리 및 비 갤러리 응용 프로그램의 경우) 및 OAuth 권한 부여 (앱 갤러리에 게시 된 응용 프로그램)를 지원 합니다.

> [!NOTE]
> 리포지토리에 제공 된 권한 부여 방법은 테스트용 으로만 제공 됩니다. Azure AD와 통합 하는 경우 권한 부여 지침을 검토할 수 있습니다. [SCIM 끝점에 대 한 프로 비전 계획](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery)을 참조 하세요. 

개발 환경에서는 참조 코드와 같이 프로덕션에 안전 하지 않은 기능을 사용 하 여 보안 토큰 유효성 검사의 동작을 제어할 수 있습니다. 토큰 유효성 검사 코드는 자체 서명 된 보안 토큰을 사용 하도록 구성 되 고 서명 키는 구성 파일에 저장 됩니다. *appsettings.Development.js* 파일의 **token: IssuerSigningKey** 매개 변수를 참조 하세요.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> 끝점에 **GET** 요청을 보내면 `/scim/token` 구성 된 키를 사용 하 여 토큰이 발급 되 고 후속 권한 부여를 위한 전달자 토큰으로 사용 될 수 있습니다.

기본 토큰 유효성 검사 코드는 Azure Active Directory에서 발급 한 토큰을 사용 하도록 구성 되며, 파일 *의appsettings.js* 에서 **Token: tokenissuer** 매개 변수를 사용 하 여 발급 테 넌 트를 구성 해야 합니다.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Postman을 사용 하 여 끝점 테스트

SCIM 끝점이 배포 된 후에는 SCIM RFC를 준수 하는지 테스트할 수 있습니다. 이 예제에서는 사용자 및 그룹에 대 한 CRUD 작업의 유효성을 검사 하 고, 필터링 하 고, 그룹 멤버 자격을 업데이트 하 고, **사용자를 비활성화 하기 위한** 테스트 집합을 제공 합니다.

끝점은 디렉터리에 있으며 `{host}/scim/` 표준 HTTP 요청을 사용 하 여 상호 작용할 수 있습니다. 경로를 수정 하려면 `/scim/` *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **scimreferenceapi**  >  **controller** 를 참조 하세요.

> [!NOTE]
> Azure AD 프로 비전 서비스에서 끝점 지원 HTTPS가 필요 하므로 로컬 테스트에 대해서만 HTTP 끝점을 사용할 수 있습니다.

#### <a name="open-postman-and-run-tests"></a>Postman을 열고 테스트를 실행 합니다.

1. [Postman](https://www.getpostman.com/downloads/) 을 다운로드 하 고 응용 프로그램을 시작 합니다.
1. 링크를 복사 하 여 [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) Postman에 붙여넣어 테스트 컬렉션을 가져옵니다.

    ![postman 컬렉션](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 아래 변수를 사용 하 여 테스트 환경을 만듭니다.

   |환경|변수|값|
   |-|-|-|
   |IIS Express를 사용 하 여 프로젝트를 로컬로 실행|||
   ||**서버**|`localhost`|
   ||**포트**|`:44359`*(를 잊지 마세요 **.**)*|
   ||**Api-version**|`scim`|
   |Kestrel을 사용 하 여 프로젝트를 로컬로 실행|||
   ||**서버**|`localhost`|
   ||**포트**|`:5001`*(를 잊지 마세요 **.**)*|
   ||**Api-version**|`scim`|
   |Azure에서 끝점 호스팅|||
   ||**서버**|*(SCIM URL 입력)*|
   ||**포트**|*(비워 둠)*|
   ||**Api-version**|`scim`|

1. Postman Collection의 **Get Key** 를 사용 하 여 토큰 끝점에 **get** 요청을 보내고 이후 요청에 대 한 **토큰** 변수에 저장할 보안 토큰을 검색 합니다. 

   ![postman에서 키를 가져옵니다.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > SCIM 끝점을 안전 하 게 보호 하려면 연결 하기 전에 보안 토큰이 필요 하며, 자습서에서는 끝점을 사용 하 여 `{host}/scim/token` 자체 서명 된 토큰을 생성 합니다.

이것으로 끝입니다. 이제 **Postman** collection을 실행 하 여 scim 끝점 기능을 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

클라이언트에 대 한 상호 운용성을 통해 SCIM 규격 사용자 및 그룹 끝점을 개발 하려면 [scim 클라이언트 구현](http://www.simplecloud.info/#Implementations2)을 참조 하세요.

> [!div class="nextstepaction"]
> [자습서: SCIM 끝점에 대 한 프로 비전 개발 및 계획](use-scim-to-provision-users-and-groups.md) 
>  [자습서: 갤러리 앱에 대 한 프로 비전 구성](configure-automatic-user-provisioning-portal.md)