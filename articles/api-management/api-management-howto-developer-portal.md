---
title: Azure API 관리 개발자 포털 개요
titleSuffix: Azure API Management
description: API 관리에서 개발자 포털에 대해 알아봅니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335903"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API Management 개발자 포털 개요

개발자 포털은 API 설명서를 통해 자동으로 생성되고 완전히 사용자 지정가능한 웹 사이트입니다. API 소비자가 API를 검색하고, API를 사용하는 방법을 알아보고, 액세스를 요청하고, 시도할 수 있는 곳입니다.

이 문서에서는 API 관리에서 개발자 포털의 자체 호스팅 버전과 관리되는 버전 간의 차이점에 대해 설명합니다. 또한 아키텍처에 대해 설명하고 자주 묻는 질문에 대한 답변을 제공합니다.

![API Management 개발자 포털](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>관리 및 자체 호스팅 버전

다음 두 가지 방법으로 개발자 포털을 빌드할 수 있습니다.

- **관리되는 버전** - API 관리 인스턴스에 내장되어 있고 URL을 `<your-api-management-instance-name>.developer.azure-api.net`통해 액세스할 수 있는 포털을 편집하고 사용자 지정하여 관리됨 버전입니다. 관리되는 포털에 액세스하고 사용자 지정하는 방법을 알아보려면 [이 설명서 문서를](api-management-howto-developer-portal-customize.md) 참조하십시오.
- **자체 호스팅 버전** - API 관리 인스턴스 외부에서 포털을 배포하고 자체 호스팅합니다. 이 방법을 사용하면 포털의 코드베이스를 편집하고 제공된 핵심 기능(예: 타사 시스템과의 통합을 위한 사용자 지정 위젯 구현)을 확장할 수 있습니다. 이 시나리오에서는 포털의 메인테이너이며 포털을 최신 버전으로 업그레이드해야 합니다. 자세한 내용 및 지침은 포털의 소스 코드와 [위젯 구현에 대한 자습서를][3] [사용하여 GitHub 리포지토리를][1] 참조하십시오. [관리되는 버전에 대한 자습서는](api-management-howto-developer-portal-customize.md) 관리되는 버전과 자체 호스팅 버전에 공통되는 포털의 관리 패널을 안내합니다.

## <a name="portal-architectural-concepts"></a>포털 아키텍처 개념

포털 구성 요소는 논리적으로 *코드와* *콘텐츠의*두 가지 범주로 나눌 수 있습니다.

*코드는* [GitHub 리포지토리에서][1] 유지 관리되며 다음을 포함합니다.

- 위젯 - 시각적 요소를 나타내고 HTML, 자바 스크립트, 스타일링 기능, 설정 및 콘텐츠 매핑을 결합합니다. 예를 들어 이미지, 텍스트 단락, 양식, API 목록 등이 있습니다.
- 스타일 정의 - 위젯의 스타일을 지정하는 방법
- 엔진 - 포털 콘텐츠에서 정적 웹 페이지를 생성하고 자바 스크립트로 작성
- 비주얼 에디터 - 브라우저 내 사용자 지정 및 제작 환경을 허용합니다.

*콘텐츠는* *포털 콘텐츠와* *API 관리 콘텐츠의*두 하위 범주로 나뉩니다.

*포털 콘텐츠는* 포털에 만연하며 다음을 포함합니다.

- 페이지 - 예를 들어 방문 페이지, API 자습서, 블로그 게시물
- 미디어 - 이미지, 애니메이션 및 기타 파일 기반 콘텐츠
- 레이아웃 - URL과 일치하고 페이지가 표시되는 방법을 정의하는 템플릿
- 스타일 - 글꼴, 색상, 테두리 와 같은 스타일 정의에 대한 값
- 설정 - 구성(예: 파비슨, 웹사이트 메타데이터)

미디어를 제외한 *포털 콘텐츠는*JSON 문서로 표현됩니다.

*API 관리 콘텐츠에는* API, 운영, 제품, 구독과 같은 엔터티가 포함됩니다.

포털은 [Paperbits 프레임워크의](https://paperbits.io/)적응된 포크를 기반으로 합니다. 원래 Paperbits 기능은 API 관리 전용 위젯(예: API 목록, 제품 목록)과 콘텐츠를 저장하고 검색하기 위한 API 관리 서비스에 대한 커넥터를 제공하기 위해 확장되었습니다.

## <a name="frequently-asked-questions"></a><a name="faq"></a>자주 묻는 질문

이 섹션에서는 일반적인 특성인 개발자 포털에 대한 일반적인 질문에 답합니다. 자체 호스팅 버전과 관련된 질문은 [GitHub 리포지토리의 위키 섹션을](https://github.com/Azure/api-management-developer-portal/wiki)참조하십시오.

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>포털의 미리 보기 버전에서 마이그레이션하는 방법은 무엇입니까?

개발자 포털의 미리 보기 버전을 사용하여 API 관리 서비스에서 미리 보기 콘텐츠를 프로비전했습니다. 기본 콘텐츠는 더 나은 사용자 환경을 위해 일반적으로 사용 가능한 버전에서 크게 수정되었습니다. 새로운 위젯도 포함되어 있습니다.

관리되는 버전을 사용하는 경우 **운영** 메뉴 섹션에서 **콘텐츠 재설정을** 클릭하여 포털의 콘텐츠를 재설정합니다. 이 작업을 확인하면 포털의 모든 콘텐츠가 제거되고 새 기본 콘텐츠가 프로비전됩니다. 포털의 엔진이 API 관리 서비스에서 자동으로 업데이트되었습니다.

![포털 콘텐츠 재설정](media/api-management-howto-developer-portal/reset-content.png)

자체 호스팅 버전을 사용하는 경우 GitHub `scripts/cleanup.bat` `scripts/generate.bat` 리포지토리의 을 사용하여 기존 콘텐츠를 제거하고 새 콘텐츠를 프로비전합니다. 포털 코드를 GitHub 리포지토리의 최신 릴리스로 미리 업그레이드해야 합니다.

포털의 콘텐츠를 재설정하지 않으려면 페이지 전체에서 새로 사용할 수 있는 위젯을 사용하는 것이 좋습니다. 기존 위젯이 최신 버전으로 자동으로 업데이트되었습니다.

일반 공급 공지 후 포털에 프로비전된 경우 새 기본 콘텐츠가 이미 있어야 합니다. 옆에서 아무런 조치도 취할 필요가 없습니다.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>이전 개발자 포털에서 개발자 포털로 마이그레이션하려면 어떻게 해야 합니까?

포털은 호환되지 않으며 콘텐츠를 수동으로 마이그레이션해야 합니다.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>포털에 이전 포털의 모든 기능이 있습니까?

개발자 포털은 더 이상 응용 프로그램 및 *문제를*지원하지 *않습니다.*

대화형 개발자 콘솔에서 OAuth를 통한 인증은 아직 지원되지 않습니다. [GitHub 문제를](https://github.com/Azure/api-management-developer-portal/issues/208)통해 진행 상황을 추적할 수 있습니다.

### <a name="has-the-old-portal-been-deprecated"></a>이전 포털이 더 이상 사용되지?

이전 개발자 및 게시자 포털은 이제 *레거시* 기능이며 보안 업데이트만 받게 됩니다. 새 기능은 새 개발자 포털에서만 구현됩니다.

레거시 포털의 사용 중단은 별도로 공지됩니다. 질문, 우려 사항 또는 의견이 있는 경우 [전용 GitHub 문제로](https://github.com/Azure/api-management-developer-portal/issues/121)문제를 제기하십시오.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>필요한 기능이 포털에서 지원되지 않습니다.

[기능 요청을](https://aka.ms/apimwish) 열거나 [누락된 기능을 직접 구현할][3]수 있습니다. 이 기능을 직접 구현하는 경우 개발자 포털을 자체 호스팅하거나 GitHub에서 끌어오기 요청을 열어 관리되는 버전에 변경 내용을 포함할 수 있습니다.

### <a name="how-can-i-automate-portal-deployments"></a>포털 배포를 자동화하는 방법은 무엇입니까?

관리되는 버전이나 자체 호스팅 버전을 사용하는지 여부에 관계없이 REST API를 통해 개발자 포털의 콘텐츠에 프로그래밍 방식으로 액세스하고 관리할 수 있습니다.

API는 [GitHub 리포지토리의 위키 섹션에][2]설명되어 있습니다. 예를 들어 테스트 환경에서 프로덕션 환경으로 의 한 환경 간 포털 콘텐츠 마이그레이션을 자동화하는 데 사용할 수 있습니다. 이 프로세스에 대한 자세한 내용은 GitHub의 [이 문서에서](https://aka.ms/apimdocs/migrateportal) 확인할 수 있습니다.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>포털이 Azure 리소스 관리자 템플릿을 지원합니까 아니면 API 관리 DevOps 리소스 키트와 호환됩니까?

아니요.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>관리되는 포털 종속성에 대해 추가 VNet 연결을 사용하도록 설정해야 합니까?

대부분의 경우 - 아니요.

API 관리 서비스가 내부 VNet에 있는 경우 개발자 포털은 네트워크 내에서만 액세스할 수 있습니다. 관리 끝점의 호스트 이름은 포털의 관리 인터페이스에 액세스하는 데 사용하는 컴퓨터에서 서비스의 내부 VIP로 확인되어야 합니다. 관리 끝점이 DNS에 등록되어 있는지 확인합니다. 구성이 잘못되면 다음과 같은 오류가 `Unable to start the portal. See if settings are specified correctly in the configuration (...)`표시됩니다.

API 관리 서비스가 내부 VNet에 있고 인터넷에서 응용 프로그램 게이트웨이를 통해 액세스하는 경우 개발자 포털 및 API 관리의 관리 끝점에 대한 연결을 사용하도록 설정해야 합니다.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>사용자 지정 API 관리 도메인을 할당했으며 게시된 포털이 작동하지 않습니다.

도메인을 업데이트한 후 변경 사항이 적용될 수 있도록 [포털을 다시 게시해야](api-management-howto-developer-portal-customize.md#publish) 합니다.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>ID 공급자를 추가했으며 포털에서 볼 수 없습니다.

ID 공급자(예: AAD, AAD B2C)를 구성한 후 변경 사항이 적용될 수 있도록 [포털을 다시 게시해야](api-management-howto-developer-portal-customize.md#publish) 합니다.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>위임을 설정했습니다만 포털에서 위임을 사용하지 않습니다.

위임을 설정한 후 변경 사항이 적용될 수 있도록 [포털을 다시 게시해야](api-management-howto-developer-portal-customize.md#publish) 합니다.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>개발자 포털에서 다른 API 관리 구성 변경 내용이 전파되지 않았습니다.

대부분의 구성 변경(예: VNet, 로그인 및 제품 용어)은 [포털을 다시 게시해야](api-management-howto-developer-portal-customize.md#publish)합니다.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>대화형 콘솔을 사용할 때 CORS 오류가 발생했습니다.

대화형 콘솔은 브라우저에서 클라이언트 측 API 요청을 합니다. API에 CORS 정책을 추가하여 [CORS](api-management-cross-domain-policies.md#CORS) 문제를 해결합니다.

Azure 포털에서 API 관리 서비스의 **포털 개요** 섹션에서 CORS 정책의 상태를 확인할 수 있습니다. 경고 상자는 부재 또는 잘못 구성된 정책을 나타냅니다.

![API Management 개발자 포털](media/api-management-howto-developer-portal/cors-azure-portal.png)

CORS 사용 단추를 클릭하여 CORS 정책을 자동으로 **적용합니다.**

CORS를 수동으로 활성화할 수도 있습니다.

1. 수동으로 **전역 수준 링크에 적용하여** 생성된 정책 코드를 확인합니다.
2. Azure 포털의 API 관리 서비스의 **API** 섹션에 있는 **모든 API로** 이동합니다.
3. 인바운드 **</>** 처리 섹션의 아이콘을 **클릭합니다.**
4. XML 파일의 **<inbound>** 섹션에 정책을 삽입합니다. **<origin>** 값이 개발자 포털의 도메인과 일치하는지 확인합니다.

> [!NOTE]
> 
> API(들) 범위 대신 제품 범위에 CORS 정책을 적용하고 API가 헤더를 통해 구독 키 인증을 사용하는 경우 콘솔이 작동하지 않습니다.
>
> 브라우저는 구독 키가 있는 헤더를 포함하지 않는 OPTIONS HTTP 요청을 자동으로 발급합니다. 구독 키가 누락되어 API 관리에서 OPTIONS 호출을 제품과 연결할 수 없으므로 CORS 정책을 적용할 수 없습니다.
>
> 해결 방법으로 쿼리 매개 변수에서 구독 키를 전달할 수 있습니다.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>개발자 포털을 편집하려면 어떤 권한이 필요합니까?

관리 모드에서 포털을 `Oops. Something went wrong. Please try again later.` 열 때 오류가 표시되면 필요한 권한(RBAC)이 부족할 수 있습니다.

레거시 포털은 사용자 `Microsoft.ApiManagement/service/getssotoken/action` 관리자가 포털에`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`액세스할 수 있도록 서비스 범위() 에서 권한을 요구했습니다. 새 포털에는 범위 `Microsoft.ApiManagement/service/users/token/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`의 사용 권한이 필요합니다.

다음 PowerShell 스크립트를 사용하여 필요한 권한이 있는 역할을 만들 수 있습니다. 매개 변수를 `<subscription-id>` 변경해야 합니다. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
역할이 만들어지면 Azure 포털의 **IAM(액세스 제어)** 섹션에서 모든 사용자에게 부여할 수 있습니다. 사용자에게 이 역할을 할당하면 서비스 범위에서 사용 권한을 할당합니다. 사용자는 서비스의 *모든* 사용자를 대신하여 SAS 토큰을 생성할 수 있습니다. 최소한 이 역할은 서비스 관리자에게 할당되어야 합니다. 다음 PowerShell 명령은 사용자에게 불필요한 사용 권한을 부여하지 않도록 가장 낮은 범위에서 사용자에게 `user1` 역할을 할당하는 방법을 보여 줍니다. 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

사용자에게 사용 권한이 부여된 후 사용자는 새 사용 권한이 적용되려면 로그아웃하고 Azure Portal에 다시 로그인해야 합니다.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>오류가 표시되었습니다. `Unable to start the portal. See if settings are specified correctly (...)`

이 오류는 호출이 `GET` `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` 실패할 때 표시됩니다. 호출은 포털의 관리 인터페이스에 의해 브라우저에서 발행됩니다.

API 관리 서비스가 VNet에 있는 경우 위의 VNet 연결 질문을 참조하십시오.

호출 실패는 사용자 지정 도메인에 할당되고 브라우저에서 신뢰할 수 없는 TLS/SSL 인증서로 인해 발생할 수도 있습니다. 완화로 관리 끝점 사용자 지정 도메인을 제거할 수 있습니다-API 관리 신뢰할 수 있는 인증서를 사용 하 여 기본 끝점으로 대체 됩니다.

### <a name="whats-the-browser-support-for-the-portal"></a>포털에 대한 브라우저 지원은 무엇입니까?

| 브라우저                     | 지원됨       |
|-----------------------------|-----------------|
| Apple Safari                | 예<sup>1</sup> |
| Google Chrome               | 예<sup>1</sup> |
| Microsoft Edge              | 예<sup>1</sup> |
| Microsoft Internet Explorer | 예              |
| Mozilla Firefox             | 예<sup>1</sup> |

 <small><sup>1</sup> 두 개의 최신 프로덕션 버전에서 지원됩니다.</small>

## <a name="next-steps"></a>다음 단계

새로운 개발자 포털에 대해 자세히 알아보세요.

- [관리되는 개발자 포털에 액세스하고 사용자 지정](api-management-howto-developer-portal-customize.md)
- [포털의 자체 호스팅 버전 설정][2]
- [나만의 위젯 구현][3]

다른 리소스 찾아보기:

- [소스 코드가 있는 GitHub 리포지토리][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend