---
title: Azure Active Directory B2B 공동 작업에서 PowerShell을 사용하여 게스트 사용자 추가 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 PowerShell을 사용하여 외부 Azure AD B2B 공동 작업 사용자에게 초대를 보내는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.openlocfilehash: f0dc63d84ec7583e721b116b450c890d46524622
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986565"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>빠른 시작: PowerShell을 사용하여 게스트 사용자 추가

Azure Active Directory B2B 공동 작업을 사용하여 앱 및 서비스에 외부 파트너를 초대할 수 있는 다양한 방법이 있습니다. 이전 빠른 시작에서는 Azure Active Directory 관리 포털에서 게스트 사용자를 직접 추가하는 방법을 살펴보았습니다. PowerShell을 사용하여 게스트 사용자를 한 번에 한 명씩 또는 일괄로 추가할 수 있습니다. 이 빠른 시작에서는 New-AzureADMSInvitation 명령을 사용하여 게스트 사용자 한 명을 Azure 테넌트에 추가합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

### <a name="install-the-latest-azureadpreview-module"></a>최신 AzureADPreview 모듈 설치
Graph 모듈에 대한 Azure AD PowerShell의 최신 버전(AzureADPreview)을 설치해야 합니다. 

먼저 설치한 모듈을 확인합니다. 관리자 권한 사용자로 Windows PowerShell을 열고(관리자 권한으로 실행) 다음 명령을 실행합니다.
 
````powershell  
Get-Module -ListAvailable AzureAD*
````

이후 버전이 있음을 나타내는 메시지 없이 AzureADPreview 모듈이 표시되면 제대로 설정된 것입니다. 그렇지 않으면 출력에 따라 다음 중 하나를 수행합니다.

- 결과가 반환되지 않으면 다음 명령을 실행하여 AzureADPreview  모듈을 설치합니다.
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- 결과에 하나의 AzureAD 모듈만 표시되면 다음 명령을 실행하여 AzureADPreview 모듈을 설치합니다. 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- AzureADPreview 모듈만 결과에 표시되는데 이후 버전이 있음을 나타내는 메시지를 받을 경우에는 다음 명령을 실행하여 모듈을 업데이트합니다. 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ````

신뢰할 수 없는 리포지토리에서 모듈을 설치하고 있음을 나타내는 메시지가 표시될 수 있습니다. 이전에 PSGallery 리포지토리를 신뢰할 수 있는 리포지토리로 설정하지 않으면 이 메시지가 표시됩니다. **Y** 키를 눌러 모듈을 설치합니다.

### <a name="get-a-test-email-account"></a>테스트 메일 계정 가져오기

초대를 보낼 수 있는 테스트 메일 계정이 필요합니다. 계정은 조직 외부에서 가져와야 합니다. gmail.com 또는 outlook.com 주소와 같은 소셜 계정을 포함하여 모든 계정 유형을 사용할 수 있습니다.

## <a name="sign-in-to-your-tenant"></a>테넌트에 로그인

다음 명령을 실행하여 테넌트 도메인에 연결합니다.

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
예: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`

메시지가 표시되면 자격 증명을 입력합니다.

## <a name="send-an-invitation"></a>초대 보내기

1. 테스트 메일 계정에 초대를 보내려면 다음 PowerShell 명령을 실행합니다(**“Sanda”** 및 **sanda@fabrikam.com**을 테스트 메일 계정 이름 및 메일 주소로 바꾸기). 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.azure.com -SendInvitationMessage $true
   ```
2. 이 명령은 지정된 메일 주소에 초대를 보냅니다. 다음과 같이 표시되어야 하는 출력을 확인합니다.

   ![보류 중인 사용자 수락을 보여 주는 PowerShell 출력](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>사용자가 디렉터리에 있는지 확인

1. 초대된 사용자가 Azure AD에 추가되었는지 확인하려면 다음 명령을 실행합니다.
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. 출력에서 초대한 사용자가 *emailaddress*#EXT#@*domain* 형식의 UPN(사용자 계정 이름)과 함께 나열되는지 확인합니다. 예를 들어 *sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com*에서 contoso.onmicrosoft.com은 초대를 보낸 조직입니다.

   ![추가된 게스트 사용자를 보여 주는 PowerShell 출력](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 디렉터리에서 테스트 사용자 계정을 삭제할 수 있습니다. 다음 명령을 실행하여 사용자 계정을 삭제합니다.

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
예: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 PowerShell을 사용하여 디렉터리에 단일 게스트 사용자를 초대하고 추가했습니다. 다음으로, PowerShell을 사용하여 게스트 사용자를 일괄 초대하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Azure AD B2B 공동 작업 사용자 일괄 초대](tutorial-bulk-invite.md)
