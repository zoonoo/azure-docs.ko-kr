---
title: Azure API Management의 개발자 포털 개요
titleSuffix: Azure API Management
description: API 소비자가 Api를 탐색할 수 있는 사용자 지정 가능한 웹 사이트인 API Management의 개발자 포털에 대해 알아봅니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30a6a73768db7b073258487435ddbe6c0daccf16
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317816"
---
# <a name="overview-of-the-developer-portal"></a>개발자 포털 개요

개발자 포털은 Api 설명서를 사용 하 여 자동으로 생성 되 고 완전히 사용자 지정할 수 있는 웹 사이트입니다. API 소비자는 api를 검색 하 고, Api를 사용 하 고, 액세스를 요청 하 고, 사용해 볼 수 있습니다.

이 문서에서는 API Management에서 개발자 포털 자체 호스팅 및 관리 되는 버전 간의 차이점을 설명 합니다. 또한 질문과 대답을 제공 합니다.

![API Management 개발자 포털](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>레거시 포털에서 마이그레이션

> [!IMPORTANT]
> 이전 개발자 포털은 더 이상 사용 되지 않으며 보안 업데이트만 받게 됩니다. 모든 API Management 서비스에서 제거 될 때 2023 평상시와 같이 계속 해 서 사용할 수 있습니다.

새 개발자 포털로 마이그레이션하는 방법은 [전용 설명서 문서](developer-portal-deprecated-migration.md)에 설명 되어 있습니다.

## <a name="customization-and-styling"></a>사용자 지정 및 스타일 지정

개발자 포털은 기본 제공 되는 끌어서 놓기 비주얼 편집기를 통해 사용자 지정 하 고 스타일을 지정할 수 있습니다. 자세한 내용은 [이 자습서](api-management-howto-developer-portal-customize.md) 를 참조 하세요.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> 확장성

API Management 서비스에는 기본 제공 되 고 항상 최신의 **관리 되** 는 개발자 포털이 포함 됩니다. Azure Portal 인터페이스에서 액세스할 수 있습니다.

기본으로 지원 되지 않는 사용자 지정 논리를 사용 하 여 확장 해야 하는 경우 해당 코드 베이스를 수정할 수 있습니다. 포털의 코드 베이스는 [GitHub 리포지토리에서 사용할 수][1]있습니다. 예를 들어 타사 지원 시스템과 통합 되는 새 위젯을 구현할 수 있습니다. 새 기능을 구현 하는 경우 다음 옵션 중 하나를 선택할 수 있습니다.

- API Management 서비스 외부에서 결과 포털을 **자체 호스트** 합니다. 포털을 자체 호스트 하는 경우 해당 관리자가 되며 업그레이드를 담당 하 게 됩니다. Azure 지원 지원은 [리포지토리의 Wiki 섹션][2]에 설명 된 대로 자체 호스팅 포털의 기본 설정 으로만 제한 됩니다.
- API Management 팀에 대 한 끌어오기 요청을 열어 새 기능을 **관리 되** 는 포털의 코드 베이스에 병합 합니다.

확장성 정보 및 지침은 [GitHub 리포지토리][1] 및 [위젯 구현에 대 한 자습서][3]를 참조 하세요. 관리 되는 [포털을 사용자 지정](api-management-howto-developer-portal-customize.md) 하는 자습서는 포털의 관리 패널을 안내 하며,이는 **관리** 되는 및 **자체 호스팅** 버전에 공통적입니다.

## <a name="frequently-asked-questions"></a><a name="faq"></a> 질문과 대답

이 섹션에서는 일반적인 특성의 개발자 포털에 대 한 일반적인 질문에 답변 합니다. 자체 호스팅 버전과 관련 된 질문은 [GitHub 리포지토리의 wiki 섹션](https://github.com/Azure/api-management-developer-portal/wiki)을 참조 하세요.

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> 포털의 미리 보기 버전에서 마이그레이션하려면 어떻게 해야 하나요?

개발자 포털의 미리 보기 버전을 처음으로 시작 하면 API Management 서비스에서 해당 기본 콘텐츠의 미리 보기 버전을 프로 비전 했습니다. 기본 콘텐츠는 일반적으로 사용할 수 있는 버전에서 크게 수정 되었습니다. 예를 들어 기본 콘텐츠의 미리 보기 버전은 로그인 페이지에 OAuth 단추를 포함 하지 않고 Api를 표시 하는 데 서로 다른 위젯을 사용 하며 개발자 포털 페이지를 구조화 하는 데 제한 된 기능을 사용 합니다. 콘텐츠에 차이가 있지만 개발자 포털을 게시할 때마다 포털의 엔진 (기본 위젯 포함)이 자동으로 업데이트 됩니다.

콘텐츠의 미리 보기 버전을 기준으로 포털을 많이 사용자 지정한 경우이를 그대로 그대로 사용 하 고 포털의 페이지에 새 위젯을 수동으로 추가할 수 있습니다. 그렇지 않으면 포털의 콘텐츠를 새 기본 콘텐츠로 바꾸는 것이 좋습니다.

관리 포털에서 콘텐츠를 다시 설정 하려면 **작업** 메뉴 섹션에서 **콘텐츠 다시 설정** 을 선택 합니다. 이 작업을 수행 하면 포털의 모든 콘텐츠가 제거 되 고 새 기본 콘텐츠가 프로 비전 됩니다. 개발자 포털 사용자 지정 및 변경 내용이 모두 손실 됩니다. **이 작업을 실행 취소할 수 없습니다**.

![포털 콘텐츠 다시 설정](media/api-management-howto-developer-portal/reset-content.png)

자체 호스팅 버전을 사용 하는 경우 `scripts.v2/cleanup.bat` `scripts.v2/generate.bat` GitHub 리포지토리에서 및 스크립트를 실행 하 여 기존 콘텐츠를 제거 하 고 새 콘텐츠를 프로 비전 합니다. 포털의 코드를 GitHub 리포지토리의 최신 릴리스로 미리 업그레이드 해야 합니다.

11 월 2019에 일반 공급 발표 후 포털에 처음으로 액세스 한 경우 새 기본 콘텐츠를 이미 사용 하 고 있으므로 추가 작업이 필요 하지 않습니다.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>필요한 기능이 포털에서 지원 되지 않습니다.

[GitHub 리포지토리에서][1] 기능 요청을 열거나 [누락 된 기능을 직접 구현할][3]수 있습니다. 자세한 내용은 위의 **확장성** 섹션을 참조 하세요.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> 포털 배포를 자동화 하려면 어떻게 해야 하나요?

관리 또는 자체 호스팅 버전을 사용 하는 경우와 관계 없이 REST API를 통해 프로그래밍 방식으로 개발자 포털의 콘텐츠를 액세스 하 고 관리할 수 있습니다.

이 API는 [GitHub 리포지토리의 wiki 섹션][2]에 설명 되어 있습니다. 테스트 환경에서 프로덕션 환경으로, 예를 들어 환경 간의 포털 콘텐츠 마이그레이션을 자동화 하는 데 사용할 수 있습니다. 이 프로세스에 대 한 자세한 내용은 GitHub의 [이 문서 문서에서](https://aka.ms/apimdocs/migrateportal) 확인할 수 있습니다.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>관리에서 자체 호스트 된 버전으로 이동 어떻게 할까요??

[GitHub의 개발자 포털 리포지토리의 Wiki 섹션][2]에서 자세한 문서를 참조 하세요.

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>하나의 API Management 서비스에서 여러 개발자 포털을 사용할 수 있나요?

하나의 관리 되는 포털 및 여러 개의 자체 호스팅 포털이 있을 수 있습니다. 모든 포털의 콘텐츠는 동일한 API Management 서비스에 저장 되므로 동일 하 게 됩니다. 포털의 모양과 기능을 구분 하려면 URL을 기반으로 런타임 시 페이지를 동적으로 사용자 지정 하는 고유한 사용자 지정 위젯을 사용 하 여 자체 호스트할 수 있습니다.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>포털이 Azure Resource Manager 템플릿을 지원 하나요? 아니면 API Management DevOps Resource Kit와 호환 되나요?

아니요.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>포털 콘텐츠가 API Management의 백업/복원 기능과 함께 저장 되나요?

아니요.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>관리 되는 포털 종속성에 대 한 추가 VNet 연결을 사용 하도록 설정 해야 하나요?

대부분의 경우-아니요.

API Management 서비스가 내부 VNet에 있는 경우 개발자 포털은 네트워크 내 에서만 액세스할 수 있습니다. 관리 끝점의 호스트 이름은 포털의 관리 인터페이스에 액세스 하는 데 사용 하는 컴퓨터에서 서비스의 내부 VIP로 확인 되어야 합니다. 관리 끝점이 DNS에 등록 되어 있는지 확인 합니다. 잘못 된 구성의 경우 오류가 표시 됩니다 `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

API Management 서비스가 내부 VNet에 있고 인터넷에서 Application Gateway를 통해 액세스 하는 경우 개발자 포털 및 API Management의 관리 끝점에 대 한 연결을 사용 하도록 설정 해야 합니다. 웹 응용 프로그램 방화벽 규칙을 사용 하지 않도록 설정 해야 할 수 있습니다. 자세한 내용은 [이 문서](api-management-howto-integrate-internal-vnet-appgateway.md) 를 참조 하세요.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>사용자 지정 API Management 도메인을 할당 하 고 게시 된 포털이 작동 하지 않음

도메인을 업데이트 한 후 변경 내용을 적용 하려면 [포털](api-management-howto-developer-portal-customize.md#publish) 을 다시 게시 해야 합니다.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Id 공급자를 추가 하 고 포털에서 볼 수 없습니다.

Id 공급자 (예: Azure AD, Azure AD B2C)를 구성한 후에는 [포털을 다시 게시](api-management-howto-developer-portal-customize.md#publish) 하 여 변경 내용을 적용 해야 합니다. 개발자 포털 페이지에 OAuth 단추 위젯이 포함 되어 있는지 확인 합니다.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>위임을 설정 하 고 포털에서 사용 하지 않음

위임을 설정한 후 변경 내용을 적용 하려면 포털을 다시 [게시](api-management-howto-developer-portal-customize.md#publish) 해야 합니다.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>개발자 포털에서 다른 API Management 구성 변경 내용이 전파 되지 않았습니다.

대부분의 구성 변경 (예: VNet, 로그인, 제품 용어)에 [는 포털](api-management-howto-developer-portal-customize.md#publish)을 다시 게시 해야 합니다.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> 대화형 콘솔을 사용 하는 경우 CORS 오류가 발생 합니다.

대화형 콘솔은 브라우저에서 클라이언트 쪽 API 요청을 만듭니다. API에 [cors 정책을](api-management-cross-domain-policies.md#CORS) 추가 하 여 cors 문제를 해결 합니다.

Azure Portal에서 API Management 서비스의 **포털 개요** 섹션에서 CORS 정책의 상태를 확인할 수 있습니다. 경고 상자에는 존재 하지 않거나 잘못 구성 된 정책이 표시 됩니다.

![API Management 개발자 포털](media/api-management-howto-developer-portal/cors-azure-portal.png)

**Cors 사용** 단추를 클릭 하 여 cors 정책을 자동으로 적용 합니다.

CORS를 수동으로 사용 하도록 설정할 수도 있습니다.

1. **전역 수준 링크에서 수동으로 적용** 을 선택 하 여 생성 된 정책 코드를 확인 합니다.
2. Azure Portal API Management 서비스의 **api** 섹션에서 **모든 api** 로 이동 합니다.
3. **</>** **인바운드 처리** 섹션에서 아이콘을 선택 합니다.
4. **<inbound>** XML 파일의 섹션에 정책을 삽입 합니다. **<origin>** 값이 개발자 포털의 도메인과 일치 하는지 확인 합니다.

> [!NOTE]
> 
> API가 헤더를 통해 구독 키 인증을 사용 하는 것이 아니라 제품 범위에 CORS 정책을 적용 하면 콘솔은 작동 하지 않습니다.
>
> 브라우저는 HTTP 요청 옵션을 자동으로 실행 합니다 .이 요청은 구독 키가 있는 헤더를 포함 하지 않습니다. 누락 된 구독 키 API Management로 인해 옵션 호출을 제품에 연결할 수 없으므로 CORS 정책을 적용할 수 없습니다.
>
> 해결 방법으로, 쿼리 매개 변수에서 구독 키를 전달할 수 있습니다.

> [!NOTE]
> 
> CORS 정책은 하나만 실행 됩니다. 여러 CORS 정책을 지정 하는 경우 (예: API 수준 및 모든 Api 수준에서) 대화형 콘솔이 예상 대로 작동 하지 않을 수 있습니다.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>개발자 포털을 편집 하는 데 필요한 권한은 무엇 인가요?

`Oops. Something went wrong. Please try again later.`관리 모드에서 포털을 열 때 오류가 표시 되는 경우 필요한 권한 (AZURE RBAC)이 없을 수 있습니다.

이전 포털에서는 `Microsoft.ApiManagement/service/getssotoken/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` 사용자가 포털에 액세스할 수 있도록 서비스 범위 ()에서 사용 권한이 필요 했습니다. 새 포털에는 범위에서 사용 권한이 필요 합니다 `Microsoft.ApiManagement/service/users/token/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

다음 PowerShell 스크립트를 사용 하 여 필요한 권한이 있는 역할을 만들 수 있습니다. 매개 변수를 변경 해야 `<subscription-id>` 합니다. 

```powershell
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
 
역할을 만든 후에는 Azure Portal에서 **Access Control (IAM)** 섹션의 모든 사용자에 게 역할을 부여할 수 있습니다. 사용자에 게이 역할을 할당 하면 서비스 범위에서 사용 권한이 할당 됩니다. *사용자가 서비스에서 사용자를* 대신 하 여 SAS 토큰을 생성할 수 있습니다. 최소한이 역할은 서비스 관리자에 게 할당 해야 합니다. 다음 PowerShell 명령은 `user1` 사용자에 게 불필요 한 권한이 부여 되지 않도록 사용자에 게 역할을 할당 하 여 가장 낮은 범위에서 사용자에 게 역할을 할당 하는 방법을 보여 줍니다. 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

사용자에 게 사용 권한이 부여 된 후에는 사용자가 로그 아웃 한 후 Azure Portal 다시 로그인 하 여 새 사용 권한을 적용 해야 합니다.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>오류가 표시 됩니다. `Unable to start the portal. See if settings are specified correctly (...)`

에 대 한 호출이 실패 하면이 오류가 표시 됩니다 `GET` `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` . 호출은 포털의 관리 인터페이스에 의해 브라우저에서 실행 됩니다.

API Management 서비스가 VNet에 있는 경우 위의 VNet 연결 질문을 참조 하세요.

사용자 지정 도메인에 할당 되 고 브라우저에서 신뢰 하지 않는 TLS/SSL 인증서로 인해 호출 오류가 발생할 수도 있습니다. 완화를 위해 관리 끝점 사용자 지정 도메인을 제거할 수 있습니다. 신뢰할 수 있는 인증서가 있는 기본 끝점으로 대체 됩니다 API Management.

### <a name="whats-the-browser-support-for-the-portal"></a>포털에 대 한 브라우저 지원 이란?

| 브라우저                     | 지원됨       |
|-----------------------------|-----------------|
| Apple Safari                | 예<sup>1</sup> |
| Google Chrome               | 예<sup>1</sup> |
| Microsoft Edge              | 예<sup>1</sup> |
| Microsoft Internet Explorer | 아니요              |
| Mozilla Firefox             | 예<sup>1</sup> |

 <small><sup>1</sup> 은 최신 프로덕션 버전 두 개에서 지원 됩니다.</small>

## <a name="next-steps"></a>다음 단계

새 개발자 포털에 대해 자세히 알아보세요.

- [관리 되는 개발자 포털 액세스 및 사용자 지정](api-management-howto-developer-portal-customize.md)
- [자체 호스트 된 포털 버전 설정][2]
- [사용자 고유의 위젯 구현][3]

다른 리소스 찾아보기:

- [소스 코드를 포함 하는 GitHub 리포지토리][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
