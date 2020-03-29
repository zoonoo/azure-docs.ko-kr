---
title: 통합 등록 문제 해결 - Azure Active Directory
description: Azure AD 다단계 인증 및 셀프 서비스 암호 재설정 결합 등록(미리 보기) 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab7c38d23cb1f05e07488810640aeb791ded3d4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847391"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>통합 보안 정보 등록 문제 해결(미리 보기)

이 문서의 정보는 결합된 등록 환경의 사용자가 보고한 문제를 해결하는 관리자를 안내하기 위한 것입니다.

|     |
| --- |
| Azure 다단계 인증 및 Azure Active Directory(Azure AD) 셀프 서비스 암호 재설정에 대한 통합 보안 정보 등록은 Azure AD의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.|
|     |

## <a name="audit-logs"></a>감사 로그

결합된 등록을 위해 기록된 이벤트는 Azure AD 감사 로그의 인증 방법 범주에 있습니다.

![등록 이벤트를 표시하는 Azure AD 감사 로그 인터페이스](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

다음 표에는 결합된 등록으로 생성된 모든 감사 이벤트가 나열됩니다.

| 활동 | 상태 | 이유 | 설명 |
| --- | --- | --- | --- |
| 사용자가 필요한 모든 보안 정보를 등록했습니다. | Success | 사용자는 필요한 모든 보안 정보를 등록했습니다. | 이 이벤트는 사용자가 등록을 성공적으로 완료한 경우에 발생합니다.|
| 사용자가 필요한 모든 보안 정보를 등록했습니다. | 실패 | 사용자가 보안 정보 등록을 취소했습니다. | 이 이벤트는 사용자가 인터럽트 모드에서 등록을 취소할 때 발생합니다.|
| 사용자 등록 보안 정보 | Success | 사용자 등록 *메서드*. | 이 이벤트는 사용자가 개별 메서드를 등록할 때 발생합니다. *방법은* 인증자 응용 프로그램, 전화, 이메일, 보안 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.| 
| 사용자가 보안 정보를 검토했습니다. | Success | 사용자가 보안 정보를 성공적으로 검토했습니다. | 이 이벤트는 사용자가 보안 정보 검토 페이지에서 **상태가 양호한** 것으로 선택할 때 발생합니다.|
| 사용자가 보안 정보를 검토했습니다. | 실패 | 사용자가 보안 정보를 검토하지 못했습니다. | 이 이벤트는 사용자가 보안 정보 검토 페이지에서 **상태가 양호한** 것으로 선택하지만 백 엔드에서 문제가 발생할 때 발생합니다.|
| 사용자가 보안 정보를 삭제했습니다. | Success | 사용자가 *메서드를*삭제했습니다. | 이 이벤트는 사용자가 개별 메서드를 삭제할 때 발생합니다. *방법은* 인증자 응용 프로그램, 전화, 이메일, 보안 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.|
| 사용자가 보안 정보를 삭제했습니다. | 실패 | 사용자가 *메서드를*삭제하지 못했습니다. | 이 이벤트는 사용자가 메서드를 삭제하려고 하지만 어떤 이유로 시도실패할 때 발생합니다. *방법은* 인증자 응용 프로그램, 전화, 이메일, 보안 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.|
| 사용자가 기본 보안 정보를 변경했습니다. | Success | 사용자가 *메서드에*대 한 기본 보안 정보를 변경 했습니다. | 이 이벤트는 사용자가 기본 메서드를 변경할 때 발생합니다. *방법은* 인증자 앱 알림, 내 인증자 앱 또는 토큰의 코드, 전화 +XXXXXXXXXXXXXXXX, +XxxXXXXXXXXXXXX에 대한 코드 텍스트 등이 될 수 있습니다.|
| 사용자가 기본 보안 정보를 변경했습니다. | 실패 | 사용자가 *메서드에*대한 기본 보안 정보를 변경하지 못했습니다. | 이 이벤트는 사용자가 기본 메서드를 변경하려고 하지만 어떤 이유로 시도실패할 때 발생합니다. *방법은* 인증자 앱 알림, 내 인증자 앱 또는 토큰의 코드, 전화 +XXXXXXXXXXXXXXXX, +XxxXXXXXXXXXXXX에 대한 코드 텍스트 등이 될 수 있습니다.|

## <a name="troubleshooting-interrupt-mode"></a>인터럽트 모드 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 내가 볼 것으로 예상 하는 방법을 볼 수 없습니다. | 1. 사용자에게 Azure AD 관리자 역할이 있는지 확인합니다. 그렇다면 SSPR 관리자 정책 차이점을 확인합니다. <br> 2. 다단계 인증 등록 시행 또는 SSPR 등록 시행으로 인해 사용자가 중단되고 있는지 여부를 확인합니다. "결합 등록 모드" 아래의 [순서도를](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) 참조하여 표시할 메서드를 결정합니다. <br> 3. 다단계 인증 또는 SSPR 정책이 변경된 최신 방법을 결정합니다. 변경 이 최신인 경우 업데이트된 정책이 전파되는 데 다소 시간이 걸릴 수 있습니다.|

## <a name="troubleshooting-manage-mode"></a>트러블슈팅 관리 모드

| 증상 | 문제 해결 단계 |
| --- | --- |
| 특정 메서드를 추가 할 수있는 옵션이 없습니다. | 1. 다단계 인증 또는 SSPR에 대해 메서드가 활성화되어 있는지 여부를 확인합니다. <br> 2. 메서드가 활성화된 경우 정책을 다시 저장하고 다시 테스트하기 전에 1-2시간 기다립니다. <br> 3. 메서드가 활성화된 경우 사용자가 설정할 수 있는 메서드의 최대 수를 아직 설정하지 않았는지 확인합니다.|

## <a name="disable-combined-registration"></a>결합된 등록 을 사용하지 않도록 설정

사용자가 새 결합된 환경에서 전화 번호 및/또는 모바일 앱을 등록하면 해당 사용자의 메서드에 대한 플래그 집합(StrongAuthenticationMethod)이 스탬프됩니다. 이 기능을 사용하면 다단계 인증이 필요할 때마다 이러한 방법으로 다단계 인증을 수행할 수 있습니다.

관리자가 미리 보기를 사용 하려면 사용자가 새 환경을 통해 등록 하 고 관리자가 미리 보기를 사용 하지 않도록 설정 하는 경우 사용자가 무의식적으로 다단계 인증에 대 한 등록 될 수 있습니다.

결합 등록을 완료한 사용자가 현재 SSPR(셀프 서비스 암호 재설정) 등록 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)페이지로 이동하면 해당 페이지에 액세스하기 전에 다단계 인증을 수행하라는 메시지가 표시됩니다. 이 단계는 기술적 관점에서 예상되지만 이전에 SSPR에만 등록된 사용자에게는 새로운 단계입니다. 이 추가 단계는 다른 수준의 보안을 제공하여 사용자의 보안 상태를 향상시키지만 관리자는 더 이상 다단계 인증을 수행할 수 없도록 사용자를 롤백하려고 할 수 있습니다.  

### <a name="how-to-roll-back-users"></a>사용자를 롤백하는 방법

관리자가 사용자의 다단계 인증 설정을 재설정하려는 경우 다음 섹션에 제공된 PowerShell 스크립트를 사용할 수 있습니다. 스크립트는 사용자의 모바일 앱 및/또는 전화 번호에 대한 강력한 인증 Method 속성을 지웁히 지웁싸습니다. 사용자에 대해 이 스크립트를 실행하는 경우 필요한 경우 다단계 인증에 다시 등록해야 합니다. 영향을 받는 모든 사용자를 롤백하기 전에 한 두 명의 사용자와 롤백을 테스트하는 것이 좋습니다.

다음 단계는 사용자 또는 사용자 그룹을 롤백하는 데 도움이 됩니다.

#### <a name="prerequisites"></a>사전 요구 사항

1. 적절한 Azure AD PowerShell 모듈을 설치합니다. PowerShell 창에서 다음 명령을 실행하여 모듈을 설치합니다.

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 영향을 받는 사용자 개체 ID 목록을 한 줄당 하나의 ID가 있는 텍스트 파일로 컴퓨터에 저장합니다. 파일 위치를 적어 둡니다.
1. 다음 스크립트를 컴퓨터에 저장하고 스크립트의 위치를 기록합니다.

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

PowerShell 창에서 다음 명령을 실행하여 스크립트 및 사용자 파일 위치를 제공합니다. 메시지가 표시되면 글로벌 관리자 자격 증명을 입력합니다. 스크립트가 각 사용자 업데이트 작업의 결과를 출력합니다.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>미리 보기 환경 사용 안 함

사용자에 대한 미리 보기 환경을 사용하지 않도록 설정하려면 다음 단계를 완료합니다.

1. 사용자 관리자로 Azure 포털에 로그인합니다.
2. Azure **Active Directory** > **사용자 설정** > **액세스 패널 미리 보기에 대한 설정 관리로**이동합니다.
3. 사용자 아래에서 **보안 정보를 등록 및 관리하기 위한 미리 보기 기능을 사용하고**선택기를 **없음으로**설정한 다음 **저장을**선택할 수 있습니다.

미리 보기 환경을 사용하여 더 이상 등록하라는 메시지가 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [셀프 서비스 암호 재설정 및 Azure 다단계 인증에 대한 통합 등록의 공개 미리 보기에 대해 자세히 알아보기](concept-registration-mfa-sspr-combined.md)
