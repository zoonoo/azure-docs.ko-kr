---
title: B2B 공동 작업 사용자 일괄 초대 자습서 - Azure Active Directory | Microsoft Docs
description: 이 자습서에서는 PowerShell 및 CSV 파일을 사용하여 외부 Azure AD B2B 공동 작업 사용자에게 일괄 초대를 보내는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 183a6ddf3fd47be552ba13ce42c1f6e29fca4410
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193954"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>자습서: Azure AD B2B 공동 작업 사용자 일괄 초대

Azure AD(Azure Active Directory) B2B 공동 작업을 사용하여 외부 파트너와 함께 작업하는 경우 동시에 여러 게스트 사용자를 조직에 초대할 수 있습니다. 이 자습서에서는 PowerShell을 사용하여 외부 사용자에게 일괄 초대를 보내는 방법을 알아봅니다. 특히 다음을 수행합니다.

> [!div class="checklist"]
> * 사용자 정보가 포함된 .csv(쉼표로 구분된 값) 파일 준비
> * PowerShell 스크립트를 실행하여 초대 보내기
> * 사용자가 디렉터리에 추가되었는지 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

### <a name="install-the-latest-azureadpreview-module"></a>최신 AzureADPreview 모듈 설치
Graph 모듈에 대한 Azure AD PowerShell의 최신 버전(AzureADPreview)을 설치해야 합니다. 

먼저 설치한 모듈을 확인합니다. 관리자 권한 사용자로 Windows PowerShell을 열고(관리자 권한으로 실행) 다음 명령을 실행합니다.
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

출력에 따라 다음 중 하나를 수행합니다.

- 결과가 반환되지 않으면 다음 명령을 실행하여 AzureADPreview  모듈을 설치합니다.
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- 결과에 하나의 AzureAD 모듈만 표시되면 다음 명령을 실행하여 AzureADPreview 모듈을 설치합니다. 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- AzureADPreview 모듈만 결과에 표시되는데 이후 버전이 있음을 나타내는 메시지를 받을 경우에는 다음 명령을 실행하여 모듈을 업데이트합니다. 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

신뢰할 수 없는 리포지토리에서 모듈을 설치하고 있음을 나타내는 메시지가 표시될 수 있습니다. 이전에 PSGallery 리포지토리를 신뢰할 수 있는 리포지토리로 설정하지 않으면 이 메시지가 표시됩니다. **Y** 키를 눌러 모듈을 설치합니다.

### <a name="get-test-email-accounts"></a>테스트 메일 계정 가져오기

초대를 보낼 수 있는 두 개 이상의 테스트 메일 계정이 필요합니다. 계정은 조직 외부에서 가져와야 합니다. gmail.com 또는 outlook.com 주소와 같은 소셜 계정을 포함하여 모든 계정 유형을 사용할 수 있습니다.

## <a name="prepare-the-csv-file"></a>CSV 파일 준비

Microsoft Excel에서 초대 대상자 사용자 이름 및 메일 주소 목록이 포함된 CSV 파일을 만듭니다. **Name** 및 **InvitedUserEmailAddress** 열 제목을 포함해야 합니다. 

예를 들어 다음 형식으로 워크시트를 만듭니다.


![보류 중인 사용자 수락을 보여 주는 PowerShell 출력](media/tutorial-bulk-invite/AddUsersExcel.png)

파일을 **C:\BulkInvite\Invitations.csv**로 저장합니다. 

Excel이 없는 경우에는 메모장과 같은 모든 텍스트 편집기에서 CSV 파일을 만들 수 있습니다. 각 값을 쉼표로 구분하고 각 행을 새 줄로 구분합니다. 

## <a name="sign-in-to-your-tenant"></a>테넌트에 로그인

다음 명령을 실행하여 테넌트 도메인에 연결합니다.

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
예: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`

메시지가 표시되면 자격 증명을 입력합니다.

## <a name="send-bulk-invitations"></a>일괄 초대 보내기

초대를 보내려면 다음 PowerShell 스크립트를 실행합니다(여기서 **c:\bulkinvite\invitations.csv**는 CSV 파일의 경로임). 

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv
   
$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   
$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."
   
foreach ($email in $invitations) 
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.azure.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```
스크립트는 invitations.csv 파일의 메일 주소로 초대를 보냅니다. 각 사용자에 대한 다음과 비슷한 출력이 표시되어야 합니다.

![보류 중인 사용자 수락을 보여 주는 PowerShell 출력](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>사용자가 디렉터리에 있는지 확인

초대된 사용자가 Azure AD에 추가되었는지 확인하려면 다음 명령을 실행합니다.
```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```
초대한 사용자가 *emailaddress*#EXT#@*domain* 형식의 UPN(사용자 계정 이름)과 함께 나열되어야 합니다. 예를 들어 *lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com*에서 contoso.onmicrosoft.com은 초대를 보낸 조직입니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 디렉터리에서 테스트 사용자 계정을 삭제할 수 있습니다. 다음 명령을 실행하여 사용자 계정을 삭제합니다.

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
예: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>다음 단계
이 자습서에서는 조직 외부의 게스트 사용자에게 일괄 초대를 보냈습니다. 다음으로, 초대 사용 프로세스가 어떻게 진행되는지 알아봅니다.

> [!div class="nextstepaction"]
> [Azure AD B2B 공동 작업 초대 사용 프로세스에 대해 알아보기](redemption-experience.md)

