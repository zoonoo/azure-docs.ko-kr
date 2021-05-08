---
title: Azure Pipelines를 사용하여 사용자 지정 정책 배포
titleSuffix: Azure AD B2C
description: Azure DevOps Services에서 Azure Pipelines를 사용하여 CI/CD 파이프라인에 Azure AD B2C 사용자 지정 정책을 배포하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913f21b90043209cae1ec9963619389bcb452781
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529427"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Azure Pipelines를 사용하여 사용자 지정 정책 배포

[Azure Pipelines][devops-pipelines]에서 설정하는 CI/CD(지속적인 통합 및 배달) 파이프라인을 사용하여 소프트웨어 제공 및 코드 제어 자동화에 Azure AD B2C 사용자 지정 정책을 포함할 수 있습니다. 개발, 테스트 및 프로덕션과 같은 다양한 Azure AD B2C 환경에 배포하는 경우 Azure Pipelines를 사용하여 수동 프로세스를 제거하고 자동화된 테스트를 수행하는 것이 좋습니다.

Azure AD B2C 내에서 사용자 지정 정책을 관리하는 데 Azure Pipelines를 사용하기 위해 필요한 세 가지 기본 단계는 다음과 같습니다.

1. Azure AD B2C 테넌트에서 새 웹 애플리케이션 등록 만들기
1. Azure 리포지토리 구성
1. Azure 파이프라인 구성

> [!IMPORTANT]
> 현재 Azure 파이프라인을 사용하여 Azure AD B2C 사용자 지정 정책을 관리하는 데는 Microsoft Graph API `/beta` 엔드포인트에서 사용할 수 있는 **미리 보기** 작업이 사용됩니다. 프로덕션 애플리케이션에서는 이러한 API의 사용이 지원되지 않습니다. 자세한 내용은 [Microsoft Graph REST API 베타 엔드포인트 참조](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure AD B2C 테넌트](tutorial-create-tenant.md) 및 [B2C IEF 정책 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) 역할의 디렉터리 사용자에 대한 자격 증명
* 테넌트에 업로드된 [사용자 지정 정책](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
* Microsoft Graph API 권한 *Policy.ReadWrite.TrustFramework* 를 사용하여 테넌트에 등록된 [관리 앱](microsoft-graph-get-started.md)
* [Azure 파이프라인](https://azure.microsoft.com/services/devops/pipelines/) 및 [Azure DevOps Services 프로젝트][devops-create-project]에 대한 액세스

## <a name="client-credentials-grant-flow"></a>클라이언트 자격 증명 부여 흐름

여기에 설명된 시나리오에서는 OAuth 2.0 [클라이언트 자격 증명 부여 흐름](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)을 사용하여 Azure Pipelines와 Azure AD B2C 사이에서 서비스 간 호출을 사용합니다. 이 권한 부여 흐름은 다른 웹 서비스(이 경우에는 Microsoft Graph API)를 호출할 때 사용자인 것처럼 가장하여 인증하지 않고 자체 자격 증명을 사용하는 기밀 클라이언트인 Azure Pipelines와 같은 웹 서비스를 허용합니다. Azure Pipelines는 비대화형 방식으로 토큰을 가져온 다음 Microsoft Graph API에 요청합니다.

## <a name="register-an-application-for-management-tasks"></a>관리 작업을 위한 애플리케이션 등록

[필수 구성 요소](#prerequisites)에 설명된 대로, Azure Pipelines에서 실행되는 PowerShell 스크립트가 테넌트의 리소스에 액세스하는 데 사용할 수 있는 애플리케이션 등록이 필요합니다.

자동화 작업에 사용하는 애플리케이션 등록이 이미 있는 경우 앱 등록의 **API 권한** 내에서 **Microsoft Graph** > **정책** > **Policy.ReadWrite.TrustFramework** 권한이 부여되었는지 확인합니다.

관리 애플리케이션 등록 지침은 [Microsoft Graph를 사용하여 Azure AD B2C 관리](microsoft-graph-get-started.md)를 참조하세요.

## <a name="configure-an-azure-repo"></a>Azure 리포지토리 구성

관리 애플리케이션이 등록되면 정책 파일에 대한 리포지토리를 구성할 준비가 된 것입니다.

1. Azure DevOps Services 조직에 로그인합니다.
1. [새 프로젝트를 만들거나][devops-create-project] 기존 프로젝트를 선택합니다.
1. 프로젝트에서 **리포지토리** 로 이동하여 **파일** 페이지를 선택합니다. 기존 리포지토리를 선택하거나 이 연습을 위해 새로운 리포지토리를 만드세요.
1. 이름이 *B2CAssets* 인 폴더를 만듭니다. 필요한 자리 표시자 파일의 이름을 *README.md* 로 표시하고 파일을 **커밋** 합니다. 원할 경우 나중에 이 파일을 제거할 수 있습니다.
1. *B2CAssets* 폴더에 Azure AD B2C 정책 파일을 추가합니다. 여기에는 *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* 및 사용자가 만든 모든 다른 정책이 포함됩니다. 이후 단계에서 사용할 각 Azure AD B2C 정책 파일의 파일 이름을 기록합니다(PowerShell 스크립트 인수로 사용됨).
1. 리포지토리의 루트 디렉터리에 *Scripts* 라는 폴더를 만들고 자리 표시자 파일의 이름을 *DeployToB2c.ps1* 로 지정합니다. 이 시점에서 파일을 커밋하지 마세요. 이후 단계에서 커밋을 수행합니다.
1. 다음 PowerShell 스크립트를 *DeployToB2c.ps1* 에 붙여넣고 파일을 **커밋** 합니다. 이 스크립트는 Azure AD에서 토큰을 획득하고 Microsoft Graph API를 호출하여 *B2CAssets* 폴더 내의 정책을 Azure AD B2C 테넌트에 업로드합니다.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Azure 파이프라인 구성

리포지토리를 초기화하여 사용자 지정 정책 파일로 채우면 릴리스 파이프라인을 설정할 준비가 된 것입니다.

### <a name="create-pipeline"></a>파이프라인 만들기

1. Azure DevOps Services 조직에 로그인하여 프로젝트로 이동합니다.
1. 프로젝트에서 **파이프라인** > **릴리스** > **새 파이프라인** 을 선택합니다.
1. **템플릿 선택** 에서 **빈 작업** 을 선택합니다.
1. **단계 이름**(예: *DeployCustomPolicies*)을 입력하고 창을 닫습니다.
1. **아티팩트 추가** 를 선택하고 **원본 형식** 에서 **Azure 리포지토리** 를 선택합니다.
    1. PowerShell 스크립트로 채운 *Scripts* 폴더가 포함된 원본 리포지토리를 선택합니다.
    1. **기본 분기** 를 선택합니다. 이전 섹션에서 새 리포지토리를 만든 경우 기본 분기는 *마스터* 입니다.
    1. *기본 분기에서 최신* 의 **기본 버전** 설정을 그대로 둡니다.
    1. 리포지토리의 **원본 별칭** 을 입력합니다. 예: *policyRepo* 별칭 이름에 공백을 포함하지 않습니다.
1. **추가** 를 선택합니다.
1. 의도를 반영하도록 파이프라인의 이름을 바꿉니다. 예: *사용자 지정 정책 파이프라인 배포*
1. **저장** 을 선택하여 파이프라인 구성을 저장합니다.

### <a name="configure-pipeline-variables"></a>파이프라인 변수 구성

1. **변수** 탭을 선택합니다.
1. **파이프라인 변수** 아래에 다음 변수를 추가하고 지정된 대로 값을 설정합니다.

    | Name | 값 |
    | ---- | ----- |
    | `clientId` | 이전에 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다. |
    | `clientSecret` | 이전에 만든 **클라이언트 암호** 의 값입니다. <br /> 잠금 아이콘을 선택하여 변수 유형을 **비밀** 로 변경합니다. |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`에서 *your-b2c-tenant* 는 Azure AD B2C 테넌트의 이름입니다. |

1. **저장** 을 선택하여 변수를 저장합니다.

### <a name="add-pipeline-tasks"></a>파이프라인 작업 추가

다음으로, 정책 파일을 배포하는 작업을 추가합니다.

1. **작업** 탭을 선택합니다.
1. **에이전트 작업** 을 선택하고 더하기 기호( **+** )를 선택하여 에이전트 작업(Agent job)에 작업(task)을 추가합니다.
1. **PowerShell** 을 검색하고 선택합니다. "Azure PowerShell," "대상 컴퓨터에서 PowerShell" 또는 다른 PowerShell 항목을 선택하지 않습니다.
1. 새로 추가된 **PowerShell 스크립트** 작업을 선택합니다.
1. PowerShell 스크립트 작업에 대해 다음 값을 입력합니다.
    * **작업 버전**: 2.*
    * **표시 이름**: 이 작업에서 업로드할 정책의 이름입니다. 예: *B2C_1A_TrustFrameworkBase*
    * **형식**: 파일 경로입니다.
    * **스크립트 경로**: 줄임표(**_..._* _)를 선택하여 _Scripts* 폴더로 이동한 다음 *DeployToB2C.ps1* 파일을 선택합니다.
    * **인수:**

        **인수** 에 다음 값을 입력합니다. `{alias-name}`을 이전 섹션에서 받은 ID로 바꿉니다.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        예를 들어 지정한 별칭이 *policyRepo* 인 경우 인수 줄은 다음과 같아야 합니다.

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. **저장** 을 선택하여 에이전트 작업을 저장합니다.

방금 추가한 작업은 Azure AD B2C에 *하나의* 정책 파일을 업로드합니다. 계속하기 전에 작업을 수동으로 트리거(**릴리스 만들기**)하여 추가 작업을 만들기 전에 성공적으로 완료되었는지 확인합니다.

작업이 성공적으로 완료되면 각 사용자 지정 정책 파일에 대해 앞의 단계를 수행하여 배포 작업을 추가합니다. 각 정책에 대한 `-PolicyId` 및 `-PathToFile` 인수 값을 수정합니다.

`PolicyId`는 TrustFrameworkPolicy 노드 내에서 XML 정책 파일의 시작 부분에 있는 값입니다. 예를 들어 다음 정책 XML의 `PolicyId`는 *B2C_1A_TrustFrameworkBase* 입니다.

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

에이전트를 실행하고 정책 파일을 업로드하는 경우 다음 순서로 업로드되었는지 확인합니다.

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

파일 구조가 계층 구조 체인으로 빌드되기 때문에 Identity Experience Framework에는 이 순서가 적용됩니다.

## <a name="test-your-pipeline"></a>파이프라인 테스트

릴리스 파이프라인을 테스트하려면:

1. **파이프라인** 을 선택한 다음 **릴리스** 를 선택합니다.
1. 앞서 만든 파이프라인(예: *DeployCustomPolicies*)을 선택합니다.
1. **릴리스 만들기**, **만들기** 를 차례로 선택하여 릴리스 큐에 대기합니다.

릴리스가 대기 중임을 알리는 알림 배너가 표시됩니다. 해당 상태를 보려면 알림 배너에서 링크를 선택하거나 **릴리스** 탭의 목록에서 해당 링크를 선택합니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [클라이언트 자격 증명을 사용하여 서비스 간 호출](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
