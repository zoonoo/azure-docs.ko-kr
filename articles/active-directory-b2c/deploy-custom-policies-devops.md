---
title: Azure 파이프라인을 통해 사용자 지정 정책 배포
titleSuffix: Azure AD B2C
description: Azure DevOps 서비스에서 Azure 파이프라인을 사용하여 CI/CD 파이프라인에 Azure AD B2C 사용자 지정 정책을 배포하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188752"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Azure 파이프라인을 통해 사용자 지정 정책 배포

[Azure 파이프라인에서][devops-pipelines]설정한 CI/CD(연속 통합 및 제공) 파이프라인을 사용하여 소프트웨어 제공 및 코드 제어 자동화에 Azure AD B2C 사용자 지정 정책을 포함할 수 있습니다. 개발, 테스트 및 프로덕션과 같은 다른 Azure AD B2C 환경에 배포할 때 는 수동 프로세스를 제거하고 Azure 파이프라인을 사용하여 자동화된 테스트를 수행하는 것이 좋습니다.

Azure AD B2C 내에서 사용자 지정 정책을 관리하도록 설정하는 데 필요한 세 가지 기본 단계가 있습니다.

1. Azure AD B2C 테넌트에서 웹 응용 프로그램 등록 만들기
1. Azure 리포지토리 구성
1. Azure 파이프라인 구성

> [!IMPORTANT]
> Azure 파이프라인을 사용하여 Azure AD B2C **preview** 사용자 지정 정책을 관리하는 `/beta` 것은 현재 Microsoft 그래프 API 끝점에서 사용할 수 있는 미리 보기 작업을 사용합니다. 프로덕션 애플리케이션에서는 이러한 API의 사용이 지원되지 않습니다. 자세한 내용은 Microsoft [그래프 REST API 베타 끝점 참조를](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)및 [B2C IEF 정책 관리자](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) 역할을 가진 디렉터리에서 사용자에 대한 자격 증명
* 테넌트에 업로드된 [사용자 지정 정책](custom-policy-get-started.md)
* Microsoft 그래프 API 권한 정책으로 테넌트에 등록된 [관리](microsoft-graph-get-started.md) *앱.ReadWrite.TrustFramework*
* [Azure 파이프라인](https://azure.microsoft.com/services/devops/pipelines/)및 [Azure DevOps 서비스 프로젝트에][devops-create-project] 대한 액세스

## <a name="client-credentials-grant-flow"></a>클라이언트 자격 증명 부여 흐름

여기서 설명된 시나리오는 OAuth 2.0 클라이언트 자격 증명 부여 흐름을 사용하여 Azure 파이프라인과 Azure AD B2C 간의 서비스 간 [호출을](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)사용합니다. 이 권한 부여 흐름은 Azure 파이프라인(기밀 클라이언트)과 같은 웹 서비스가 다른 웹 서비스(이 경우 Microsoft Graph API)를 호출할 때 인증할 사용자를 가장하는 대신 자체 자격 증명을 사용할 수 있도록 합니다. Azure 파이프라인은 비대화형으로 토큰을 얻은 다음 Microsoft 그래프 API에 요청을 합니다.

## <a name="register-an-application-for-management-tasks"></a>관리 작업에 대한 응용 프로그램 등록

[필수 구성 프로그램에서](#prerequisites)설명한 것처럼 Azure 파이프라인에서 실행되는 PowerShell 스크립트가 테넌트의 리소스에 액세스하는 데 사용할 수 있는 응용 프로그램 등록이 필요합니다.

자동화 작업에 사용하는 응용 프로그램 등록이 이미 있는 경우 앱 등록의 API 권한 내에서 **Microsoft 그래프** > **정책** > **정책.ReadWrite.TrustFramework** 권한이 부여되었는지 **확인합니다.**

관리 응용 프로그램 등록에 대한 지침은 [Microsoft 그래프를 사용하는 Azure AD B2C 관리를](microsoft-graph-get-started.md)참조하십시오.

## <a name="configure-an-azure-repo"></a>Azure 리포지토리 구성

관리 응용 프로그램을 등록하면 정책 파일에 대한 리포지토리를 구성할 준비가 된 것입니다.

1. Azure DevOps 서비스 조직에 로그인합니다.
1. [새 프로젝트를 작성하거나][devops-create-project] 기존 프로젝트를 선택합니다.
1. 프로젝트에서 **리포지토리로** 이동하여 **파일** 페이지를 선택합니다. 기존 리포지토리를 선택하거나 이 연습에 대해 저장소를 만듭니다.
1. *B2CAssets라는*폴더를 만듭니다. 필요한 자리 표시자 파일의 이름을 *README.md* 지정하고 파일을 **커밋합니다.** 원하는 경우 나중에 이 파일을 제거할 수 있습니다.
1. Azure AD B2C 정책 파일을 *B2CAssets* 폴더에 추가합니다. 여기에는 *트러스트프레임워크베이스.xml,* *TrustFrameWorkExtensions.xml,* *SignUpOrSignin.xml,* *ProfileEdit.xml,* *PasswordReset.xml*및 기타 정책이 포함됩니다. 이후 단계에서 사용할 각 Azure AD B2C 정책 파일의 파일 이름을 기록합니다(PowerShell 스크립트 인수로 사용).
1. 리포지토리의 루트 디렉토리에 *스크립트라는 폴더를* 만들고 자리 표시자 파일 *DeployToB2c.ps1의*이름을 지정합니다. 이 시점에서 파일을 커밋하지 마십시오, 당신은 나중에 단계에서 그렇게 할 수 있습니다.
1. 다음 PowerShell 스크립트를 *DeployToB2c.ps1에*붙여넣은 다음 파일을 **커밋합니다.** 스크립트는 Azure AD에서 토큰을 획득하고 Microsoft 그래프 API를 호출하여 *B2CAssets* 폴더 내의 정책을 Azure AD B2C 테넌트에 업로드합니다.

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

리포지토리를 초기화하고 사용자 지정 정책 파일로 채워면 릴리스 파이프라인을 설정할 준비가 된 것입니다.

### <a name="create-pipeline"></a>파이프라인 만들기

1. Azure DevOps 서비스 조직에 로그인하고 프로젝트로 이동합니다.
1. 프로젝트에서 **파이프라인 릴리스** > **Releases** > **새 파이프라인을**선택합니다.
1. **템플릿 선택에서** **빈 작업을**선택합니다.
1. 단계 **이름(예:** *사용자 지정 정책 배포)을*입력한 다음 창을 닫습니다.
1. **아티팩트 추가**및 **소스 유형**에서 **Azure 저장소**를 선택합니다.
    1. PowerShell 스크립트로 채워진 스크립트 *폴더가* 포함된 원본 리포지토리를 선택합니다.
    1. 기본 **분기를**선택합니다. 이전 섹션에서 새 리포지토리를 만든 경우 기본 분기는 *마스터*입니다.
    1. 기본 *분기에서 최신의 기본* **버전** 설정을 그대로 둡니다.
    1. 리포지토리에 대한 **소스 별칭을** 입력합니다. 예를 *들어, 정책Repo*. 별칭 이름에 공백을 포함하지 마십시오.
1. **추가** 선택
1. 의도를 반영하도록 파이프라인이름을 바꿉니다. 예를 들어 *사용자 지정 정책 파이프라인을 배포합니다.*
1. 파이프라인 구성을 저장하려면 **저장을** 선택합니다.

### <a name="configure-pipeline-variables"></a>파이프라인 변수 구성

1. 변수 **탭을 선택합니다.**
1. 파이프라인 변수 아래에 다음 변수를 추가하고 지정된 값을 **설정합니다.**

    | 이름 | 값 |
    | ---- | ----- |
    | `clientId` | 이전에 등록한 응용 프로그램의 응용 **프로그램(클라이언트) ID입니다.** |
    | `clientSecret` | 이전에 만든 **클라이언트 보안** 의 값입니다. <br /> 변수 유형을 **비밀로** 변경합니다(잠금 아이콘 선택). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`- *b2c 테넌트가* Azure AD B2C 테넌트의 이름입니다. |

1. 변수를 저장하려면 **저장을** 선택합니다.

### <a name="add-pipeline-tasks"></a>파이프라인 작업 추가

다음으로 정책 파일을 배포하는 작업을 추가합니다.

1. **작업** 탭을 선택합니다.
1. **에이전트 작업을**선택한 다음 에이전트 작업에**+** 작업을 추가하려면 더하기 기호 ()를 선택합니다.
1. **PowerShell을**검색하고 선택합니다. "Azure PowerShell", "대상 컴퓨터에서 PowerShell" 또는 다른 PowerShell 항목을 선택하지 마십시오.
1. 새로 추가된 **PowerShell 스크립트** 작업을 선택합니다.
1. PowerShell 스크립트 작업에 대해 다음 값을 입력합니다.
    * **작업 버전**: 2.*
    * **표시 이름**: 이 작업이 업로드해야 하는 정책의 이름입니다. 예를 들어, *B2C_1A_TrustFrameworkBase*.
    * **유형**: 파일 경로
    * **스크립트 경로**: 타원 ***(...***) 을 선택하고 스크립트 폴더로 이동한 다음 *DeployToB2C.ps1* 파일을 *선택합니다.*
    * **인수:**

        인수에 대한 다음 값을 **입력합니다.** 이전 `{alias-name}` 섹션에서 지정한 별칭으로 바꿉습니다.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        예를 들어 지정한 별칭이 *policyRepo인*경우 인수 줄은 다음과 같이 해야 합니다.

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. 에이전트 작업을 저장하려면 **저장을** 선택합니다.

방금 추가한 작업은 *하나의* 정책 파일을 Azure AD B2C에 업로드합니다. 계속하기 전에 작업을 수동으로**트리거하여**추가 작업을 만들기 전에 작업이 성공적으로 완료되었는지 확인합니다.

작업이 성공적으로 완료되면 각 사용자 지정 정책 파일에 대한 이전 단계를 수행하여 배포 작업을 추가합니다. 각 `-PolicyId` 정책에 `-PathToFile` 대한 및 인수 값을 수정합니다.

는 `PolicyId` TrustFrameworkPolicy 노드 내에서 XML 정책 파일의 시작 부분에 있는 값입니다. 예를 들어 `PolicyId` 다음 정책 XML은 *다음과 B2C_1A_TrustFrameworkBase.*

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

에이전트를 실행하고 정책 파일을 업로드할 때 이 순서대로 업로드되었는지 확인합니다.

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *가입OrSignin.xml*
1. *프로파일편집.xml*
1. *암호 Reset.xml*

ID 환경 프레임워크는 파일 구조가 계층 적 체인에 빌드될 때 이 순서를 적용합니다.

## <a name="test-your-pipeline"></a>파이프라인 테스트

릴리스 파이프라인을 테스트하려면 다음을 수행하십시오.

1. 파이프라인을 선택한 다음 을 **해제합니다.** **Releases**
1. 이전에 만든 파이프라인(예: *사용자 지정 정책 배포)*
1. **릴리스 만들기를**선택한 다음 **만들기를** 선택하여 릴리스를 큐에 대기합니다.

릴리스가 큐에 대기중임을 알려주는 알림 배너가 표시됩니다. 상태를 보려면 알림 배너에서 링크를 선택하거나 **릴리스** 탭의 목록에서 링크를 선택합니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [클라이언트 자격 증명을 사용하여 서비스 간 호출](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure 데브옵스 서비스](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
