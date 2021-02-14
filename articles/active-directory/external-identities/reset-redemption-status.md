---
title: 게스트 사용자의 상환 상태 다시 설정-Azure AD
description: Azure AD 외부 Id에서 B2B 게스트 사용자 Azure Active Directory에 대 한 초대 상환 상태를 다시 설정 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: d843755847d074e00aec9ed2830cb873b6bb3382
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365415"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>게스트 사용자의 상환 상태 다시 설정

게스트 사용자가 B2B 공동 작업 초대를 사용한 후에는 다음과 같은 경우에 로그인 정보를 업데이트 해야 하는 경우가 있을 수 있습니다.

- 사용자가 다른 전자 메일 및 id 공급자를 사용 하 여 로그인 하려고 합니다.
- 해당 홈 테 넌 트의 사용자 계정이 삭제 되 고 다시 생성 되었습니다.
- 사용자가 다른 회사로 이동 했지만 여전히 리소스에 대 한 동일한 액세스 권한이 있어야 합니다.
- 사용자의 책임이 다른 사용자에 게 전달 되었습니다.

이러한 시나리오를 이전에 관리 하려면 디렉터리에서 게스트 사용자의 계정을 수동으로 삭제 하 고 사용자를 reinvite 합니다. 이제 PowerShell 또는 Microsoft Graph 초대 API를 사용 하 여 사용자의 상환 상태를 다시 설정 하 고 사용자의 개체 ID, 그룹 멤버 자격 및 앱 할당을 유지 하면서 사용자를 reinvite 수 있습니다. 사용자가 새 초대를 교환 사용자의 UPN은 변경 되지 않지만 사용자의 로그인 이름이 새 전자 메일로 변경 됩니다. 사용자는 이후에 새 전자 메일 또는 `otherMails` 사용자 개체의 속성에 추가한 전자 메일을 사용 하 여 로그인 할 수 있습니다.

## <a name="use-powershell-to-reset-redemption-status"></a>PowerShell을 사용 하 여 상환 상태 다시 설정

최신 AzureADPreview PowerShell 모듈을 설치 하 고를 `InvitedUserEMailAddress` 새 전자 메일 주소로 설정 하 여 새 초대를 만들고 `ResetRedemption` 를로 설정 `true` 합니다.

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Microsoft Graph API를 사용 하 여 상환 상태 다시 설정

[Microsoft Graph 초대 API](/graph/api/resources/invitation?view=graph-rest-1.0)를 사용 하 여 속성을로 설정 하 `resetRedemption` `true` 고 속성에 새 전자 메일 주소를 지정 합니다 `invitedUserEmailAddress` .

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용하여 Azure Active Directory B2B 협업 사용자 추가](customize-invitation-api.md#powershell)
- [Azure AD B2B 게스트 사용자의 속성](user-properties.md)
