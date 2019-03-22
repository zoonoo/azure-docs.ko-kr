---
title: Azure AD SSPR 및 MFA (미리 보기)-Azure Active Directory에 대 한 결합 된 등록 문제 해결
description: Azure AD 다단계 인증 및 셀프 서비스 암호 재설정 결합 등록 (미리 보기) 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f799b671e6216b402aa988aa66da4c2cfc44693
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317530"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>결합 된 보안 정보 등록 (미리 보기) 문제 해결

이 문서에 제공 된 정보를 최종 사용자에 게 보고 하 여 결합 된 등록 환경을 사용 하 여 문제를 해결 하는 관리자 가이드 수 있습니다.

|     |
| --- |
| Azure Multi-factor Authentication 및 Azure AD 셀프 서비스 암호 재설정에 대 한 통합된 보안 정보 등록에는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="audit-logs"></a>감사 로그

결합 된 등록에 대 한 기록 된 이벤트에 "인증 방법" 범주 아래에 있는 Azure AD 감사 로그 합니다.

![Azure AD 감사 로그 이벤트를 보여 주는 몇 가지 보안 정보 등록 새 사용자에 대 한 디렉터리에 인터페이스](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

다음은 결합 된 등록 하 여 생성 된 모든 감사 이벤트에 대 한 목록입니다.

| 작업 | 상태 | 이유 | 설명 |
| --- | --- | --- | --- |
| 사용자가 모든 필요한 보안 정보 등록 | 성공 | 사용자는 모든 필요한 보안 정보를 등록 합니다. | 이 이벤트는 사용자 등록을 성공적으로 완료 되 면 발생 합니다.|
| 사용자가 모든 필요한 보안 정보 등록 | 실패 | 사용자가 보안 정보 등록을 취소 했습니다. | 이 이벤트는 사용자가 인터럽트 모드에서 등록을 취소 하면 발생 합니다.|
| 등록 된 사용자 보안 정보 | 성공 | 사용자는 "메서드"를 등록 합니다. | 이 이벤트는 사용자가 개별 메서드를 등록 하면 발생 합니다. "메서드" Authenticator 앱, 전화, 전자 메일 수 등, 대체 전화, 앱 암호, 보안 질문입니다.| 
| 검토 하는 사용자 보안 정보 | 성공 | 사용자 보안 정보를 성공적으로 검토합니다. | 이 이벤트는 보안 정보 검토 페이지에서 "정상"를 클릭할 때 발생 합니다.|
| 검토 하는 사용자 보안 정보 | 실패 | 사용자 보안 정보를 검토 하지 못했습니다. | 이 이벤트 페이지를 검토 "양호" 보안 정보에만 백 엔드에 실패 하면 클릭할 때 발생 합니다.|
| 삭제 된 사용자 보안 정보 | 성공 | 사용자는 "메서드"를 삭제 합니다. | 이 이벤트는 사용자는 개별 메서드를 삭제할 때 발생 합니다. "메서드" Authenticator 앱, 전화, 전자 메일 수 등, 대체 전화, 앱 암호, 보안 질문입니다.|
| 삭제 된 사용자 보안 정보 | 실패 | 사용자는 "메서드"를 삭제 하지 못했습니다. | 이 이벤트는 사용자가 메서드를 삭제 하려고 하지만 어떤 이유로 실패 하는 경우에 발생 합니다. "메서드" Authenticator 앱, 전화, 전자 메일 수 등, 대체 전화, 앱 암호, 보안 질문입니다.|
| 변경할 사용자 기본 보안 정보 | 성공 | 사용자는 "방법" 기본 보안 정보를 변경 합니다. | 이 이벤트는 사용자 자신의 기본 메서드를 변경할 때 발생 합니다. "메서드" Authenticator 앱 알림 수를 내 authenticator 앱 또는 토큰을 호출 + X XXXXXXXXXX, 텍스트 등 XXXXXXXXX X +에 코드를 코드 수 있습니다.|
| 변경할 사용자 기본 보안 정보 | 실패 | 사용자는 "방법" 기본 보안 정보를 변경 하지 못했습니다. | 이 이벤트는 사용자가 자신의 기본 메서드를 변경 하려고 시도 하지만 어떤 이유로 실패 하는 경우에 발생 합니다. Authenticator 앱 알림, authenticator 앱 또는 토큰, 호출 내에서 코드 + X XXXXXXXXXX, 텍스트 등 XXXXXXXXX X +에 코드를 "메서드" 일 수 있습니다.|

## <a name="troubleshooting-interrupt-mode"></a>인터럽트 모드 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 참조 해야 하는 경우 메서드 표시 되지 않습니다. | 1. 사용자를 Azure AD 관리자 역할에 있는지 확인 합니다. 그렇다면 SSPR 관리자 정책 차이점을 검토 합니다. <br> 2. 사용자 MFA 등록 적용 또는 SSPR 등록 적용으로 인해 중단 되 고 있는지 여부를 결정 합니다. 메서드를 표시 해야 결정할 결합 된 등록 모드에서 순서도 검토 합니다. <br> 3. MFA 또는 SSPR 정책을 변경한 경과한 기간을 결정 합니다. 최근 변경 되었으면 전파 하는 업데이트 된 정책을 다소 시간이 걸릴 수 있습니다.|

## <a name="troubleshooting-manage-mode"></a>Mode 관리 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 특정 메서드를 추가 하는 옵션이 없습니다. | 1. 메서드 또는 SSPR MFA에 대 한 사용 되는지 여부를 결정 합니다. <br> 2. 메서드를 사용 하는 경우 정책을 다시 저장 하 고 다시 테스트 하기 전에 1 ~ 2 시간 동안 대기 합니다. <br> 3. 메서드를 사용 하는 경우 설정 되었는지 확인 사용자 않은 이미 최대 수를 설정 하도록 허용 하는 메서드입니다.|

## <a name="disable-combined-registration"></a>결합 된 등록을 사용 하지 않도록 설정

사용자 새 해당 전화 번호 및/또는 모바일 앱을 등록할 때 결합 환경에는 서비스 스탬프 (StrongAuthenticationMethods) 해당 사용자에 해당 메서드에 대 한 플래그 집합입니다. 이 기능을 통해 사용자는 MFA가 필요할 때마다 본인의 방법을 사용하여 MFA(Multi-Factor Authentication)를 수행할 수 있습니다.

사용자가 새 환경을 통해 등록하는 방법에 StrongAuthenticationMethods 속성이 설정됩니다. 관리자가 미리 보기를 사용하도록 설정하면 사용자는 새 경험을 통해 등록하고, 관리자가 미리 보기를 사용하지 않도록 설정하면 사용자는 자신도 모르게 MFA에 등록될 수 있습니다.

완료 하는 사용자 등록을 결합 하는 경우에 현재 셀프 서비스 암호 재설정 (SSPR) 등록 페이지를 탐색 [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), 메시지가 표시 됩니다 해당 페이지에 액세스 하기 전에 MFA를 수행 합니다. 이 단계는 기술적 관점에서 예상되는 동작이지만, 이전에 SSPR에만 등록한 사용자에게는 이 단계가 새로운 동작입니다. 이 추가 단계는 추가 보안 수준을 제공하여 사용자의 보안을 강화하지만, 관리자는 사용자가 더 이상 MFA를 수행할 수 없도록 사용자를 롤백할 수 있습니다.  

### <a name="how-to-roll-back-users"></a>사용자를 롤백하는 방법

관리자가 사용자의 MFA 설정을 재설정할 수 있도록, 사용자의 모바일 앱 및/또는 전화 번호에 대한 StrongAuthenticationMethods 속성을 지울 수 있는 PowerShell 스크립트를 만들어 두었습니다. 필요한 경우 사용자에 대해 이 스크립트를 실행하면 사용자가 MFA에 다시 등록해야 합니다. 영향을 받는 모든 사용자를 롤백하기 전에 사용자 한 명 또는 두 명으로 롤백을 테스트하는 것이 좋습니다.

이어지는 단계는 사용자 또는 사용자 그룹을 롤백하는 데 도움이 됩니다.

#### <a name="prerequisites"></a>필수 조건

1. 적절한 Azure AD PowerShell 모듈을 설치해야 합니다. PowerShell 창에서 다음 명령을 실행하여 모듈을 설치합니다.

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 영향을 받는 사용자 개체 ID/ID 목록을 머신에 한 줄당 ID 하나씩 텍스트 파일로 저장합니다. 파일 위치를 적어 둡니다.
1. 머신에 다음 스크립트를 저장하고 스크립트 위치를 기록해 둡니다.

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

#### <a name="rollback"></a>롤백

PowerShell 창에서 강조 표시된 위치를 업데이트한 후 다음 명령을 실행합니다. 메시지가 표시되면 글로벌 관리자 자격 증명을 입력합니다. 스크립트가 각 사용자 업데이트 작업의 결과를 출력합니다.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>미리 보기 환경을 사용하지 않도록 설정

사용자에게 미리 보기 환경을 사용하지 않으려면 다음 단계를 수행합니다.

1. 글로벌 관리자 또는 사용자 관리자로 Azure Portal에 로그인합니다.
2. **Azure Active Directory**, **사용자 설정**, **액세스 패널 미리 보기 기능의 설정 관리**로 이동합니다.
3. **사용자가 보안 정보를 등록하고 관리하기 위한 미리 보기 기능을 사용 가능**에서 선택기를 **없음**으로 설정하고 **저장**을 클릭합니다.

사용자에게 미리 보기 환경을 사용하여 등록하라는 메시지가 더 이상 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

[셀프 서비스 암호 재설정 및 Azure Multi-factor Authentication에 대 한 결합 된 등록의 공개 미리 보기에 자세히 알아보기](concept-registration-mfa-sspr-combined.md)
