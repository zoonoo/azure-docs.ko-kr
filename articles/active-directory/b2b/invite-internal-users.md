---
title: 내부 사용자를 B2B 공동 작업 - Azure AD에 초대합니다.
description: 파트너, 배포자, 공급업체, 공급업체 및 기타 게스트에 대한 내부 사용자 계정이 있는 경우 Azure AD B2B 공동 작업으로 변경하여 자체 외부 자격 증명 또는 로그인을 초대할 수 있습니다. PowerShell 또는 Microsoft 그래프 초대 API를 사용합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 097de14a3451e8d352dceb17436ae8423aa06533
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265942"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>내부 사용자를 B2B 공동 작업으로 초대

|     |
| --- |
| 내부 사용자에게 B2B 공동 작업을 사용하도록 초대하는 것은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오. |
|     |

Azure AD B2B 공동 작업을 사용할 수 있게 되기 전에 조직은 배포자, 공급업체, 공급업체 및 기타 게스트 사용자와 공동 자격 증명을 설정하여 공동 작업할 수 있습니다. 이와 같은 내부 게스트 사용자가 있는 경우 Azure AD B2B 혜택을 활용할 수 있도록 B2B 공동 작업을 사용하도록 초대할 수 있습니다. B2B 게스트 사용자는 자신의 ID와 자격 증명을 사용하여 로그인할 수 있으며 암호를 유지하거나 계정 수명 주기를 관리할 필요가 없습니다.

기존 내부 계정에 초대를 보내면 해당 사용자의 개체 ID, UPN, 그룹 구성원 자격 및 앱 할당을 유지할 수 있습니다. 수동으로 사용자를 삭제하고 다시 초대하거나 리소스를 다시 할당할 필요가 없습니다. 사용자를 초대하려면 초대 API를 사용하여 초대와 함께 내부 사용자 개체와 게스트 사용자의 전자 메일 주소를 모두 전달합니다. 사용자가 초대를 수락하면 B2B 서비스는 기존 내부 사용자 개체를 B2B 사용자로 변경합니다. 앞으로 사용자는 B2B 자격 증명을 사용하여 클라우드 리소스 서비스에 로그인해야 합니다. 내부 자격 증명을 사용하여 온프레미스 리소스에 액세스할 수 있지만 내부 계정의 암호를 재설정하거나 변경하여 이를 방지할 수 있습니다.

> [!NOTE]
> 초대는 단방향입니다. 내부 사용자에게 B2B 공동 작업을 사용하도록 초대할 수 있지만 추가된 후에는 B2B 자격 증명을 제거할 수 없습니다. 사용자를 내부 전용 사용자로 다시 변경하려면 사용자 개체를 삭제하고 새 개체를 만들어야 합니다.

공개 미리 보기에서는 내부 사용자를 B2B 공동 작업으로 초대하기 위해 이 문서에서 설명하는 메서드를 이러한 인스턴스에서 사용할 수 없습니다.

- 내부 사용자에게 이미 Exchange 라이선스가 할당되었습니다.
- 사용자는 디렉터리에서 직접 페더레이션을 위해 설정된 도메인에서 가져옵니다.
- 내부 사용자는 클라우드 전용 계정이며 기본 계정은 Azure AD에 없습니다.

이러한 경우 내부 사용자를 B2B 사용자로 변경해야 하는 경우 내부 계정을 삭제하고 사용자에게 B2B 공동 작업 초대장을 보내야 합니다.

**온-프레미스 동기화 된 사용자**: 온-프레미스와 클라우드 간에 동기화된 사용자 계정의 경우 온-프레미스 디렉터리(On-premises directory)는 B2B 공동 작업을 사용하도록 초대된 후에도 권한의 원천으로 유지됩니다. 온-프레미스 계정에 대한 모든 변경 사항은 계정을 비활성화하거나 삭제하는 것을 포함하여 클라우드 계정에 동기화됩니다. 따라서 온-프레미스 계정을 삭제하기만 하면 클라우드 계정을 유지하면서 사용자가 온-프레미스 계정에 로그인하는 것을 막을 수 없습니다. 대신 온-프레미스 계정 암호를 임의의 GUID 또는 기타 알 수 없는 값으로 설정할 수 있습니다.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>내부 사용자를 B2B 공동 작업으로 초대하는 방법

PowerShell 또는 초대 API를 사용하여 내부 사용자에게 B2B 초대를 보낼 수 있습니다. 초대에 사용할 전자 메일 주소가 내부 사용자 개체의 외부 전자 메일 주소로 설정되어 있는지 확인합니다.

- 클라우드 전용 사용자의 경우 초대에 대한 User.OtherMails 속성의 전자 메일 주소를 사용합니다.
- 온-프레미스 동기화 된 사용자의 경우 초대에 대 한 User.Mail 속성에서 값을 사용 해야 합니다.
- 사용자의 Mail 속성의 도메인은 로그인하는 데 사용하는 계정과 일치해야 합니다. 그렇지 않으면 Teams와 같은 일부 서비스는 사용자를 인증할 수 없습니다.

기본적으로 초대장은 초대받은 사실을 알리는 이메일을 사용자에게 보내지만 이 이메일을 표시하지 않고 직접 보낼 수 있습니다.

> [!NOTE]
> 사용자 고유의 전자 메일 또는 기타 통신을 보내려면 New-AzureADMSInvitationwith with -SendInvitationMessage:$false 사용하여 사용자를 자동으로 초대한 다음 변환된 사용자에게 자신의 전자 메일 메시지를 보낼 수 있습니다. [Azure AD B2B 공동 작업 API 및 사용자 지정을](customize-invitation-api.md)참조하십시오.

## <a name="use-powershell-to-send-a-b2b-invitation"></a>PowerShell을 사용하여 B2B 초대를 보냅니다.

다음 명령을 사용하여 사용자를 B2B 공동 작업으로 초대합니다.

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>초대 API를 사용하여 B2B 초대보내기

아래 샘플에서는 초대 API를 호출하여 내부 사용자를 B2B 사용자로 초대하는 방법을 보여 줍니다.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>"",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>""
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {"id": "<<ID for the user you want to convert>>"}
}
```

API에 대한 응답은 디렉터리에 새 게스트 사용자를 초대할 때 얻는 것과 동일한 응답입니다.

## <a name="next-steps"></a>다음 단계

- [B2B 협업 초대 상환](redemption-experience.md)