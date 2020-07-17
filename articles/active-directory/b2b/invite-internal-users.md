---
title: 내부 사용자를 B2B 공동 작업에 초대-Azure AD
description: 파트너, 배포자, 공급자, 공급 업체 및 기타 게스트에 대 한 내부 사용자 계정이 있는 경우 고유한 외부 자격 증명 또는 로그인을 사용 하 여 로그인 하도록 초대 하 여 Azure AD B2B 공동 작업을 변경할 수 있습니다. PowerShell 또는 Microsoft Graph 초대 API를 사용 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551369"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>내부 사용자를 B2B 공동 작업에 초대

> [!NOTE]
> B2B 공동 작업을 사용 하도록 내부 사용자를 초대 하는 것은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure AD B2B 공동 작업을 사용 하기 전에 조직에서 내부 자격 증명을 설정 하 여 배포자, 공급자, 공급 업체 및 기타 게스트 사용자와 공동 작업을 할 수 있습니다. 이와 같은 내부 게스트 사용자가 있는 경우 Azure AD B2B 혜택을 활용할 수 있도록 B2B 공동 작업을 사용 하도록 초대할 수 있습니다. B2B 게스트 사용자는 자신의 id 및 자격 증명을 사용 하 여 로그인 할 수 있으며 암호를 유지 관리 하거나 계정 주기를 관리할 필요가 없습니다.

기존 내부 계정에 초대를 보내면 해당 사용자의 개체 ID, UPN, 그룹 멤버 자격 및 앱 할당을 유지할 수 있습니다. 수동으로 사용자를 삭제 하 고 다시 초대 하거나 리소스를 다시 할당할 필요가 없습니다. 사용자를 초대 하려면 초대 API를 사용 하 여 초대와 함께 내부 사용자 개체와 게스트 사용자의 전자 메일 주소를 모두 전달 합니다. 사용자가 초대를 수락 하면 B2B 서비스는 기존 내부 사용자 개체를 B2B 사용자로 변경 합니다. 앞으로 사용자는 B2B 자격 증명을 사용 하 여 클라우드 리소스 서비스에 로그인 해야 합니다. 내부 자격 증명을 사용 하 여 온-프레미스 리소스에도 액세스할 수 있지만, 내부 계정에서 암호를 다시 설정 하거나 변경 하 여이를 방지할 수 있습니다.

> [!NOTE]
> 초대는 단방향입니다. B2B 공동 작업을 사용 하도록 내부 사용자를 초대할 수 있지만 추가 된 B2B 자격 증명은 제거할 수 없습니다. 사용자를 내부 전용 사용자로 다시 변경 하려면 사용자 개체를 삭제 하 고 새 개체를 만들어야 합니다.

공개 미리 보기에서 내부 사용자를 B2B 공동 작업에 초대 하는 방법에 대 한이 문서에서 설명 하는 방법은 다음과 같은 경우에 사용할 수 없습니다.

- 내부 사용자에 게 이미 Exchange 라이선스가 할당 되었습니다.
- 사용자는 디렉터리에서 직접 페더레이션을 위해 설정 된 도메인에서 온 것입니다.
- 내부 사용자는 클라우드 전용 계정이 며 해당 주 계정은 Azure AD에 없습니다.

이러한 경우 내부 사용자를 B2B 사용자로 변경 해야 하는 경우 내부 계정을 삭제 하 고 사용자에 게 B2B 공동 작업을 위한 초대를 전송 해야 합니다.

온 **-프레미스 동기화 된 사용자**: 온-프레미스와 클라우드 간에 동기화 되는 사용자 계정의 경우, B2B 공동 작업을 사용 하도록 초대 된 후 온-프레미스 디렉터리는 기관의 원본으로 유지 됩니다. 온-프레미스 계정에 대 한 모든 변경 내용은 계정을 비활성화 또는 삭제 하는 작업을 포함 하 여 클라우드 계정에 동기화 됩니다. 따라서 온-프레미스 계정을 삭제 하기만 하면 클라우드 계정을 유지 하면서 사용자가 온-프레미스 계정에 로그인 하는 것을 방지할 수 없습니다. 대신 온-프레미스 계정 암호를 임의 GUID 또는 다른 알 수 없는 값으로 설정할 수 있습니다.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>내부 사용자를 B2B 공동 작업에 초대 하는 방법

PowerShell 또는 초대 API를 사용 하 여 내부 사용자에 게 B2B 초대를 보낼 수 있습니다. 초대에 사용할 전자 메일 주소가 내부 사용자 개체의 외부 전자 메일 주소로 설정 되었는지 확인 합니다.

- 클라우드 전용 사용자의 경우 초대에 대 한 사용자의 OtherMails 속성에서 전자 메일 주소를 사용 합니다.
- 온-프레미스 동기화 된 사용자의 경우에는 초대에 대해 User. Mail 속성의 값을 사용 해야 합니다.
- 사용자의 메일 속성에 있는 도메인은 로그인 하는 데 사용 하 고 있는 계정과 일치 해야 합니다. 그렇지 않으면 팀과 같은 일부 서비스에서 사용자를 인증할 수 없습니다.

기본적으로 초대는 사용자에 게 초대를 알리는 전자 메일을 전송 하지만,이 전자 메일을 표시 하지 않고 사용자 고유의 전자 메일을 보낼 수 있습니다.

> [!NOTE]
> 사용자 고유의 전자 메일 또는 기타 통신을 보내려면-SendInvitationMessage: $false와 함께 AzureADMSInvitation를 사용 하 여 사용자를 자동으로 초대 한 다음 변환 된 사용자에 게 고유한 전자 메일 메시지를 보낼 수 있습니다. [AZURE AD B2B 공동 작업 API 및 사용자 지정을](customize-invitation-api.md)참조 하세요.

## <a name="use-powershell-to-send-a-b2b-invitation"></a>PowerShell을 사용 하 여 B2B 초대 보내기

다음 명령을 사용 하 여 B2B 공동 작업에 사용자를 초대 합니다.

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>초대 API를 사용 하 여 B2B 초대 보내기

아래 샘플에서는 초대 API를 호출 하 여 내부 사용자를 B2B 사용자로 초대 하는 방법을 보여 줍니다.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

API에 대 한 응답은 새 게스트 사용자를 디렉터리에 초대할 때 표시 되는 것과 동일한 응답입니다.

## <a name="next-steps"></a>다음 단계

- [B2B 협업 초대 상환](redemption-experience.md)
