---
title: 결합된 등록 문제 해결 - Azure Active Directory
description: Azure AD Multi-Factor Authentication과 셀프 서비스 암호 재설정 결합된 등록 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: db87887fc2b51c7cb8cb300eb8e711d3ae9b6ac8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98610790"
---
# <a name="troubleshooting-combined-security-information-registration"></a>결합된 보안 정보 등록 문제 해결

이 문서의 정보는 관리자가 결합된 등록 환경의 사용자로부터 보고받은 문제를 해결하도록 안내하는 정보입니다.

## <a name="audit-logs"></a>감사 로그

결합된 등록에 대한 이벤트 로그는 Azure AD 감사 로그의 Authentication Methods 서비스에 있습니다.

![등록 이벤트를 표시하는 Azure AD 감사 로그 인터페이스](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

다음 표는 결합된 등록에 의해 생성된 모든 감사 이벤트를 보여 줍니다.

| 활동 | 상태 | 이유 | 설명 |
| --- | --- | --- | --- |
| 사용자가 필수 보안 정보를 모두 등록함 | Success | 사용자가 필수 보안 정보를 모두 등록했습니다. | 해당 이벤트는 사용자가 성공적으로 등록을 완료했을 때 발생합니다.|
| 사용자가 필수 보안 정보를 모두 등록함 | 실패 | 사용자가 보안 정보 등록을 취소했습니다. | 이 이벤트는 사용자가 인터럽트 모드에서 등록을 취소할 때 발생합니다.|
| 사용자가 보안 정보를 등록함 | Success | 사용자가 등록한 ‘메서드’입니다. | 이 이벤트는 사용자가 개별 메서드를 등록할 때 발생합니다. ‘메서드’는 Authenticator 앱, 전화, 메일, 본인 확인 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.| 
| 사용자가 보안 정보를 검토함 | Success | 사용자가 보안 정보를 검토했습니다. | 해당 이벤트는 사용자가 보안 정보 검토 페이지에서 **확인** 을 선택하는 경우에 발생합니다.|
| 사용자가 보안 정보를 검토함 | 실패 | 사용자가 보안 정보를 검토하지 못했습니다. | 해당 이벤트는 사용자가 보안 정보 검토 페이지에서 **확인** 을 선택했지만 백 엔드에서 문제가 생긴 경우에 발생합니다.|
| 사용자가 보안 정보를 삭제함 | Success | 사용자가 ‘메서드’를 삭제했습니다. | 해당 이벤트는 사용자가 개별 메서드를 삭제한 경우에 발생합니다. ‘메서드’는 Authenticator 앱, 전화, 메일, 본인 확인 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.|
| 사용자가 보안 정보를 삭제함 | 실패 | 사용자가 ‘메서드’를 삭제하지 못했습니다. | 해당 이벤트는 메서드를 삭제하려는 사용자의 시도가 어떤 이유로 인해 실패한 경우에 발생합니다. ‘메서드’는 Authenticator 앱, 전화, 메일, 본인 확인 질문, 앱 암호, 대체 전화 등이 될 수 있습니다.|
| 사용자가 기본 보안 정보를 변경함 | Success | 사용자가 ‘메서드’에 대한 기본 보안 정보를 변경했습니다. | 해당 이벤트는 사용자가 기본 메서드를 변경한 경우에 발생합니다. ‘메서드’는 Authenticator 앱 알림, 인증자 앱이나 토큰의 코드, +X XXXXXXXXXX에 전화하기, +X XXXXXXXXX에 코드를 문자로 보내기 등이 될 수 있습니다.|
| 사용자가 기본 보안 정보를 변경함 | 실패 | 사용자가 ‘메서드’에 대한 기본 보안 정보를 변경하지 못했습니다. | 해당 이벤트는 메서드를 변경하려는 사용자의 시도가 어떤 이유로 인해 실패한 경우에 발생합니다. ‘메서드’는 Authenticator 앱 알림, 인증자 앱이나 토큰의 코드, +X XXXXXXXXXX에 전화하기, +X XXXXXXXXX에 코드를 문자로 보내기 등이 될 수 있습니다.|

## <a name="troubleshooting-interrupt-mode"></a>인터럽트 모드 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 보여야 하는 메서드가 보이지 않습니다. | 1. 사용자에게 Azure AD 관리자 역할이 있는지 확인합니다. 있다면, SSPR 관리자 정책의 차이점을 확인합니다. <br> 2. Multi-Factor Authentication 등록 적용 또는 SSPR 등록 적용으로 인해 사용자가 중단되었는지 여부를 확인합니다. 보여야 하는 메서드를 확인하려면 “결합된 등록 모드”에서 [순서도](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)를 참조하세요. <br> 3. 최근 Multi-Factor Authentication 또는 SSPR 정책이 어떻게 변경되었는지 확인합니다. 최근에 변경된 경우, 업데이트된 정책이 보급되는 데 다소 시간이 걸릴 수 있습니다.|

## <a name="troubleshooting-manage-mode"></a>관리 모드 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 특정 메서드를 추가할 수 있는 옵션이 없습니다. | 1. Multi-Factor Authentication 또는 SSPR을 위한 메서드 사용이 설정되었는지 확인합니다. <br> 2. 메서드 사용이 설정된 경우, 정책을 다시 저장하고 1~2시간 기다린 후에 다시 테스트합니다. <br> 3. 메서드 사용이 설정된 경우, 사용자가 설정할 수 있는 최대 메서드를 이미 설정하지 않았는지 확인합니다.|

## <a name="disable-combined-registration"></a>결합된 등록 사용하지 않음

사용자가 새롭게 결합된 환경에 전화번호 및/또는 모바일 앱을 등록하면 서비스에서 해당 사용자의 메서드에 플래그 집합(StrongAuthenticationMethods)을 스탬핑합니다. 이 기능을 통해 사용자는 Multi-Factor Authentication이 필요할 때마다 해당 메서드로 Multi-Factor Authentication을 수행할 수 있습니다.

관리자가 미리 보기를 사용하면 사용자는 새 환경을 통해 등록합니다. 그다음, 관리자가 미리 보기 사용을 중지하면 사용자는 알지 못하는 사이에 Multi-Factor Authentication에도 등록될 수 있습니다.

결합된 등록을 완료한 사용자가 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)에서 현재의 SSPR(셀프 서비스 암호 재설정) 등록 페이지로 이동하는 경우, 해당 페이지에 액세스하기 전에 Multi-Factor Authentication을 수행하라는 메시지가 사용자에게 표시됩니다. 기술적인 관점에서는 예상되는 단계이지만, 이전에 SSPR에만 등록한 사용자에게는 새로운 기능입니다. 이 추가 단계는 다른 수준의 보안을 제공하여 사용자의 보안 상태를 개선하지만, 관리자는 사용자가 더 이상 Multi-Factor Authentication을 수행할 수 없도록 사용자를 롤백하고자 할 수도 있습니다.  

### <a name="how-to-roll-back-users"></a>사용자를 롤백하는 방법

관리자 권한으로 사용자의 Multi-Factor Authentication 설정을 초기화하려면 다음 섹션에서 제공하는 PowerShell 스크립트를 사용할 수 있습니다. 이 스크립트는 사용자의 모바일 앱 및/또는 전화번호에 대한 StrongAuthenticationMethods 속성을 지웁니다. 사용자를 대상으로 해당 스크립트를 실행하는 경우, 필요에 따라 사용자는 Multi-Factor Authentication을 다시 등록해야 합니다. 영향을 받는 모든 사용자를 롤백하기 전에 한두 명의 사용자로 롤백을 테스트하는 것이 좋습니다.

다음 단계는 사용자 또는 사용자 그룹을 롤백하는 데 도움을 줍니다.

#### <a name="prerequisites"></a>필수 구성 요소

1. 적절한 Azure AD PowerShell 모듈을 설치합니다. PowerShell 창에서 다음 명령을 실행하여 모듈을 설치합니다.

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 영향을 받는 사용자 개체 ID 목록을 컴퓨터에 텍스트 파일로 한 줄에 ID 하나씩 저장합니다. 파일 위치를 적어 둡니다.
1. 컴퓨터에 다음 스크립트를 저장하고 스크립트 위치를 기록해 둡니다.

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

PowerShell 창에서 다음 명령을 실행하여 스크립트와 사용자 파일 위치를 제공합니다. 메시지가 표시되면 글로벌 관리자 자격 증명을 입력합니다. 스크립트가 각 사용자 업데이트 작업의 결과를 출력합니다.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>업데이트된 환경 사용하지 않음

사용자의 업데이트된 환경을 사용하지 않으려면 다음 단계를 수행합니다.

1. 관리자 권한으로 Azure Portal에 로그인합니다.
2. **Azure Active Directory** > **사용자 설정** > **액세스 패널 미리 보기 기능의 설정 관리** 로 이동합니다.
3. **보안 정보 등록 및 관리를 위한 미리 보기 기능을 사용자가 사용 가능** 에서 선택기를 **없음** 으로 설정한 다음, **저장** 을 선택합니다.

사용자에게 업데이트된 환경을 사용하여 등록하라는 메시지가 더 이상 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [셀프 서비스 암호 재설정 및 Azure AD Multi-Factor Authentication에 대한 결합된 등록에 대해 자세히 알아보기](concept-registration-mfa-sspr-combined.md)
