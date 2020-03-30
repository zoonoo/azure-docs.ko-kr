---
title: 서비스 주체 - Azure를 사용하여 Windows 가상 데스크톱용 관리 도구 배포
description: PowerShell을 사용하여 Windows 가상 데스크톱용 관리 도구를 배포하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127801"
---
# <a name="deploy-a-management-tool-with-powershell"></a>PowerShell을 사용하여 관리 도구 배포

이 문서에서는 PowerShell을 사용하여 관리 도구를 배포하는 방법을 보여 주었습니다.

## <a name="important-considerations"></a>중요 고려 사항

각 Azure Active Directory(Azure AD) 테넌트의 구독에는 관리 도구의 고유한 별도의 배포가 필요합니다. 이 도구는 Azure AD 비즈니스 간(B2B) 시나리오를 지원하지 않습니다. 

이 관리 도구는 샘플입니다. Microsoft에서 중요한 보안 및 품질 업데이트를 제공할 것입니다. [소스 코드는 GitHub 에서 사용할 수 있습니다.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) 고객이든 파트너이든 비즈니스 요구 사항을 충족하기 위해 도구를 사용자 지정하는 것이 좋습니다.

다음 브라우저는 관리 도구와 호환됩니다.

- Google Chrome 68 이상
- Microsoft Edge 40.15063 이상
- Mozilla Firefox 52.0 이상
- Safari 10 이상(macOS에만 해당)

## <a name="what-you-need-to-deploy-the-management-tool"></a>관리 도구를 배포하는 데 필요한 사항

관리 도구를 배포하기 전에 앱 등록을 만들고 관리 UI를 배포하는 Azure AD(Azure Active Directory) 사용자가 필요합니다. 이 사용자가 다음 조건을 충족해야 합니다.

- Azure 구독에서 리소스를 만드는 데 필요한 권한 보유
- Azure AD 애플리케이션을 만드는 데 필요한 권한 보유 [필요한 권한](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)의 지침에 따라 사용자에게 필요한 권한이 있는지 확인하려면 다음 단계를 수행합니다.

관리 도구를 성공적으로 배포하고 구성하려면 먼저 [RDS-TemplateGitHub 리포지토리에서](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) 다음 PowerShell 스크립트를 다운로드하여 로컬 컴퓨터의 동일한 폴더에 저장해야 합니다.

  - createWvdMgmtUxApp등록.ps1
  - 업데이트WvdMgmtUxApiUrl.ps1

관리 도구를 배포하고 구성한 후에는 사용자에게 관리 UI를 시작하여 모든 기능이 작동하는지 확인하도록 요청하는 것이 좋습니다. 관리 UI를 시작하는 사용자에게는 Windows Virtual Desktop 테넌트를 보거나 편집할 수 있는 역할 할당이 있어야 합니다.

## <a name="set-up-powershell"></a>PowerShell 설정

Az 및 Azure AD PowerShell 모듈모두에 로그인하여 시작하십시오. 로그인하는 방법은 다음과 같습니다.

1. 관리자로 PowerShell을 열고 PowerShell 스크립트를 저장한 디렉토리로 이동합니다.
2. 다음 cmdlet을 실행하여 관리 도구를 만드는 데 사용할 Azure 구독에 대한 소유자 또는 기여자 권한이 있는 계정으로 Azure에 로그인합니다.

    ```powershell
    Login-AzAccount
    ```

3. 다음 cmdlet을 실행하여 Az PowerShell 모듈에 사용한 것과 동일한 계정으로 Azure AD에 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

4. 그런 다음 RDS 템플릿 GitHub 리포지토리에서 두 개의 PowerShell 스크립트를 저장한 폴더로 이동합니다.

로그인하는 데 사용한 PowerShell 창을 열어 두어 로그인하는 동안 추가 PowerShell cmdlet을 실행합니다.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록 만들기

다음 명령을 실행하여 필요한 API 권한이 있는 앱 등록을 만듭니다.

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Azure AD 앱 등록을 완료한 후 관리 도구를 배포할 수 있습니다.

## <a name="deploy-the-management-tool"></a>관리 도구 배포

다음 PowerShell 명령을 실행하여 관리 도구를 배포하고 방금 만든 서비스 주체와 연결합니다.
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

웹 앱을 만든 후에는 사용자를 성공적으로 로그인하려면 Azure AD 응용 프로그램에 리디렉션 URI를 추가해야 합니다.

## <a name="set-the-redirect-uri"></a>리디렉션 URI 설정

다음 PowerShell 명령을 실행하여 웹 앱 URL을 검색하고 인증 리디렉션 URI(회신 URL이라고도 함)로 설정합니다.

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

리디렉션 URI를 추가한 후 관리 도구가 API 백 엔드 서비스와 상호 작용할 수 있도록 API URL을 업데이트해야 합니다.

## <a name="update-the-api-url-for-the-web-application"></a>웹 응용 프로그램의 API URL 업데이트

다음 스크립트를 실행하여 웹 응용 프로그램 프런트 엔드에서 API URL 구성을 업데이트합니다.

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

관리 도구 웹 앱을 완전히 구성한 이제 Azure AD 응용 프로그램을 확인하고 동의를 제공할 차례입니다.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Azure AD 응용 프로그램 확인 및 동의 제공

Azure AD 응용 프로그램 구성을 확인하고 동의를 제공하려면 다음을 수행하십시오.

1. 인터넷 브라우저를 열고 관리 계정으로 [Azure 포털에](https://portal.azure.com/) 로그인합니다.
2. Azure 포털 상단의 검색 창에서 앱 등록을 검색하고 **서비스**에서 항목을 **선택합니다.**
3. **모든 응용 프로그램을** 선택하고 Azure Active [Directory 앱 등록 만들기에서](#create-an-azure-active-directory-app-registration)PowerShell 스크립트에 대해 제공한 고유한 앱 이름을 검색합니다.
4. 브라우저 왼쪽패널에서 **인증을** 선택하고 다음 이미지와 같이 리디렉션 URI가 관리 도구의 웹 앱 URL과 동일한지 확인합니다.
   
   [![입력된 리디렉션 URI가](media/management-ui-redirect-uri-inline.png) 있는 인증 페이지](media/management-ui-redirect-uri-expanded.png#lightbox)

5. 왼쪽 패널에서 **API 권한을** 선택하여 사용 권한이 추가되었는지 확인합니다. 글로벌 관리자인 경우 버튼에 **대한 `tenantname` 권한 부여 관리자 동의를** 선택하고 대화 상자 프롬프트를 따라 조직에 대한 관리자 동의를 제공합니다.
    
    [![API 권한 페이지](media/management-ui-permissions-inline.png)](media/management-ui-permissions-expanded.png#lightbox)

이제 관리 도구를 사용할 수 있습니다.

## <a name="use-the-management-tool"></a>관리 도구 사용

이제 언제든지 관리 도구를 설정했기 때문에 언제 어디서나 시작할 수 있습니다. 도구를 시작하는 방법은 다음과 같습니다.

1. 웹 브라우저에서 웹 앱의 URL을 엽니다. URL이 기억나지 않으면 Azure에 로그인하고 관리 도구에 배포한 앱 서비스를 찾은 다음 URL을 선택할 수 있습니다.
2. Windows Virtual Desktop 자격 증명을 사용하여 로그인합니다.
   
   > [!NOTE]
   > 관리 도구를 구성하는 동안 관리자동의를 부여하지 않은 경우 이 도구를 사용하려면 서명하는 각 사용자가 자신의 사용자 동의를 제공해야 합니다.

3. 테넌트 그룹을 선택하라는 메시지가 표시되면 드롭다운 목록에서 **기본 테넌트 그룹을** 선택합니다.
4. **기본 테넌트 그룹**을 선택하면 창의 왼쪽에 메뉴가 나타납니다. 이 메뉴에서 테넌트 그룹의 이름을 찾아서 선택합니다.
   
   > [!NOTE]
   > 사용자 지정 테넌트 그룹이 있는 경우 드롭다운 목록에서 선택하지 말고 수동으로 이름을 입력합니다.

## <a name="report-issues"></a>문제 보고

관리 도구 또는 기타 Windows Virtual Desktop 도구에 문제가 발생하는 경우 [Remote Desktop Services용 Azure Resource Manager 템플릿](https://github.com/Azure/RDS-Templates/blob/master/README.md)의 지침에 따라 GitHub에 보고합니다.

## <a name="next-steps"></a>다음 단계

관리 도구를 배포하고 연결하는 방법을 배웠으므로, 이제 Azure Service Health를 사용하여 서비스 이슈와 상태 권고를 모니터링하는 방법을 알아볼 수 있습니다. 자세한 내용은 [서비스 경고 설정 자습서](./set-up-service-alerts.md)를 참조하세요.
