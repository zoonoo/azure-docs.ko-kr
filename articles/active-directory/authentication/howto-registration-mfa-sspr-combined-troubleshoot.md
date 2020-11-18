---
title: 결합 된 등록 문제 해결-Azure Active Directory
description: Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정 결합 등록 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac03ff017f8beefe7cc487cdc32741ac1c5a35
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838181"
---
# <a name="troubleshooting-combined-security-information-registration"></a>결합 된 보안 정보 등록 문제 해결

이 문서의 정보는 결합 된 등록 환경의 사용자가 보고 한 문제를 해결 하는 관리자를 안내 하기 위한 것입니다.

## <a name="audit-logs"></a>감사 로그

결합 된 등록에 대해 기록 된 이벤트는 Azure AD 감사 로그의 인증 방법 범주에 있습니다.

![등록 이벤트를 표시 하는 Azure AD Audit logs 인터페이스](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

다음 표에서는 결합 된 등록에 의해 생성 된 모든 감사 이벤트를 보여 줍니다.

| 활동 | 상태 | 이유 | 설명 |
| --- | --- | --- | --- |
| 사용자가 필요한 모든 보안 정보를 등록 했습니다. | 성공 | 사용자가 필요한 모든 보안 정보를 등록 했습니다. | 이 이벤트는 사용자가 성공적으로 등록을 완료 했을 때 발생 합니다.|
| 사용자가 필요한 모든 보안 정보를 등록 했습니다. | 실패 | 사용자가 보안 정보 등록을 취소 했습니다. | 이 이벤트는 사용자가 인터럽트 모드에서 등록을 취소할 때 발생 합니다.|
| 사용자 등록 보안 정보 | 성공 | 사용자가 등록 한 *메서드입니다*. | 이 이벤트는 사용자가 개별 메서드를 등록할 때 발생 합니다. *메서드* 는 인증자 앱, 전화, 전자 메일, 보안 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.| 
| 사용자가 검토 한 보안 정보 | 성공 | 사용자가 보안 정보를 검토 했습니다. | 이 이벤트는 사용자가 보안 정보 검토 페이지에서 선택 하는 **것 처럼 보이는** 경우에 발생 합니다.|
| 사용자가 검토 한 보안 정보 | 실패 | 사용자가 보안 정보를 검토 하지 못했습니다. | 이 이벤트는 사용자가 보안 정보 검토 페이지에서 **양호** 하지만 백 엔드에서 실패 한 항목을 선택할 때 발생 합니다.|
| 사용자가 보안 정보를 삭제 함 | 성공 | 사용자가 *메서드* 를 삭제 했습니다. | 이 이벤트는 사용자가 개별 메서드를 삭제할 때 발생 합니다. *메서드* 는 인증자 앱, 전화, 전자 메일, 보안 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.|
| 사용자가 보안 정보를 삭제 함 | 실패 | 사용자가 *메서드* 를 삭제 하지 못했습니다. | 이 이벤트는 사용자가 메서드를 삭제 하려고 하지만 어떤 이유로 인해 시도가 실패 한 경우에 발생 합니다. *메서드* 는 인증자 앱, 전화, 전자 메일, 보안 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.|
| 사용자가 기본 보안 정보를 변경 했습니다. | 성공 | 사용자가 *메서드에* 대 한 기본 보안 정보를 변경 했습니다. | 이 이벤트는 사용자가 기본 메서드를 변경할 때 발생 합니다. *메서드* 는 인증자 앱 알림, 내 authenticator 앱 또는 토큰의 코드, + x XXXXXXXXXX 호출, 텍스트 A 코드를 + x XXXXXXXXX 등으로 지정할 수 있습니다.|
| 사용자가 기본 보안 정보를 변경 했습니다. | 실패 | 사용자가 *메서드에* 대 한 기본 보안 정보를 변경 하지 못했습니다. | 이 이벤트는 사용자가 기본 메서드를 변경 하려고 하지만 어떤 이유로 시도에 실패 하는 경우에 발생 합니다. *메서드* 는 인증자 앱 알림, 내 authenticator 앱 또는 토큰의 코드, + x XXXXXXXXXX 호출, 텍스트 A 코드를 + x XXXXXXXXX 등으로 지정할 수 있습니다.|

## <a name="troubleshooting-interrupt-mode"></a>인터럽트 모드 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 기대 하는 방법이 표시 되지 않습니다. | 1. 사용자에 게 Azure AD 관리자 역할이 있는지 확인 합니다. 그렇다면 SSPR admin 정책 차이를 확인 합니다. <br> 2. Multi-Factor Authentication 등록 적용 또는 SSPR 등록 적용으로 인해 사용자가 중단 되었는지 여부를 확인 합니다. 표시 되어야 하는 메서드를 확인 하려면 "결합 된 등록 모드"에서 [순서도](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) 를 참조 하세요. <br> 3. 최근 Multi-Factor Authentication 또는 SSPR 정책이 변경 된 정도를 확인 합니다. 변경 내용이 최근 인 경우 업데이트 된 정책이 전파 되는 데 약간의 시간이 걸릴 수 있습니다.|

## <a name="troubleshooting-manage-mode"></a>관리 모드 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 특정 메서드를 추가할 수 있는 옵션이 없습니다. | 1. 메서드가 Multi-Factor Authentication 또는 SSPR에 대해 사용 하도록 설정 되었는지 확인 합니다. <br> 2. 메서드를 사용 하는 경우 정책을 다시 저장 하 고 테스트 하기 전에 1-2 시간을 기다립니다. <br> 3. 메서드를 사용 하는 경우 사용자가 설정할 수 있는 최대 메서드 수를 아직 설정 하지 않았는지 확인 합니다.|

## <a name="disable-combined-registration"></a>결합 된 등록 사용 안 함

사용자가 새로운 결합 환경에서 전화 번호 및/또는 모바일 앱을 등록 하는 경우 서비스는 해당 사용자의 해당 메서드에 대 한 플래그 집합 (StrongAuthenticationMethods)을 스탬프 처리 합니다. 이 기능을 통해 사용자는 Multi-Factor Authentication 필요할 때마다 이러한 메서드로 Multi-Factor Authentication을 수행할 수 있습니다.

관리자가 미리 보기를 사용 하도록 설정 하면 사용자가 새 환경을 등록 한 다음 관리자가 미리 보기를 사용 하지 않도록 설정 합니다. 사용자는 Multi-Factor Authentication에 대해 모르고 등록할 수도 있습니다.

결합 된 등록을 완료 한 사용자가에서 현재 SSPR (셀프 서비스 암호 재설정) 등록 페이지로 이동 하는 경우 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 해당 페이지에 액세스 하기 전에 Multi-Factor Authentication을 수행 하 라는 메시지가 사용자에 게 표시 됩니다. 이 단계는 기술적인 관점에서 예상 되지만 이전에 SSPR에 등록 한 사용자에 게는 새로운 기능입니다. 이 추가 단계는 다른 수준의 보안을 제공 하 여 사용자의 보안 상태를 개선 하지만, 관리자가 더 이상 Multi-Factor Authentication를 수행할 수 없도록 사용자를 롤백하는 것이 좋습니다.  

### <a name="how-to-roll-back-users"></a>사용자를 롤백하는 방법

관리자 권한으로 사용자의 Multi-Factor Authentication 설정을 다시 설정 하려면 다음 섹션에서 제공 하는 PowerShell 스크립트를 사용할 수 있습니다. 이 스크립트는 사용자의 모바일 앱 및/또는 전화 번호에 대 한 StrongAuthenticationMethods 속성을 지웁니다. 사용자에 대해이 스크립트를 실행 하는 경우 필요에 따라 Multi-Factor Authentication를 다시 등록 해야 합니다. 영향을 받는 모든 사용자를 롤백하려면 한 명 또는 두 명의 사용자로 롤백을 테스트 하는 것이 좋습니다.

다음 단계는 사용자 또는 사용자 그룹을 롤백하는 데 도움이 됩니다.

#### <a name="prerequisites"></a>전제 조건

1. 적절 한 Azure AD PowerShell 모듈을 설치 합니다. PowerShell 창에서 다음 명령을 실행하여 모듈을 설치합니다.

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 영향을 받는 사용자 개체 Id 목록을 한 줄에 하나의 ID로 텍스트 파일로 저장 합니다. 파일 위치를 적어 둡니다.
1. 다음 스크립트를 컴퓨터에 저장 하 고 스크립트의 위치를 적어둡니다.

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

PowerShell 창에서 다음 명령을 실행 하 여 스크립트 및 사용자 파일 위치를 제공 합니다. 메시지가 표시되면 글로벌 관리자 자격 증명을 입력합니다. 스크립트가 각 사용자 업데이트 작업의 결과를 출력합니다.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>업데이트 된 환경 사용 안 함

사용자를 위해 업데이트 된 환경을 사용 하지 않도록 설정 하려면 다음 단계를 완료 합니다.

1. 사용자 관리자 권한으로 Azure Portal에 로그인 합니다.
2. **Azure Active Directory**  >  **사용자 설정**  >  **액세스 패널 미리 보기 기능에 대 한 설정 관리** 로 이동 합니다.
3. **사용자는 미리 보기 기능을 사용 하 여 보안 정보를 등록 하 고 관리할 수 있으며** 선택기를 **없음** 으로 설정한 다음 **저장** 을 선택 합니다.

업데이트 된 환경을 사용 하 여 사용자에 게 더 이상 등록 하 라는 메시지가 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [셀프 서비스 암호 재설정 및 Azure AD Multi-Factor Authentication에 대 한 결합 된 등록에 대해 자세히 알아보세요.](concept-registration-mfa-sspr-combined.md)
