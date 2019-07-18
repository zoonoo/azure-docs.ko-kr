---
title: Azure AD SSPR 및 Multi-factor Authentication (미리 보기)-Azure Active Directory에 대 한 결합 된 등록 문제 해결
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
ms.openlocfilehash: 40918493071fe0dd694c43e2b087a2bf7eb197d8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60414627"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>결합 된 보안 정보 등록 (미리 보기) 문제 해결

이 문서의 정보는 결합 된 등록 환경 사용자가 보고 한 문제를 해결 하는 관리자 가이드를 것입니다.

|     |
| --- |
| Azure Multi-factor Authentication 및 Azure Active Directory (Azure AD) 셀프 서비스 암호 재설정에 대 한 통합된 보안 정보 등록에는 Azure AD의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="audit-logs"></a>감사 로그

결합 된 등록에 대 한 기록 된 이벤트는 인증 방법 범주에서 Azure AD에서 로그를 감사 합니다.

![Azure AD 감사 로그 등록 이벤트를 보여 주는 하는 인터페이스](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

다음 표에서 결합 된 등록 하 여 생성 된 모든 감사 이벤트를 나열 합니다.

| 작업 | 상태 | 이유 | 설명 |
| --- | --- | --- | --- |
| 사용자가 모든 필요한 보안 정보 등록 | 성공 | 사용자는 모든 필요한 보안 정보를 등록 합니다. | 이 이벤트는 사용자 등록을 성공적으로 완료 되 면 발생 합니다.|
| 사용자가 모든 필요한 보안 정보 등록 | 실패 | 사용자가 보안 정보 등록을 취소 했습니다. | 이 이벤트는 사용자가 인터럽트 모드에서 등록을 취소 하면 발생 합니다.|
| 등록 된 사용자 보안 정보 | 성공 | 등록 된 사용자 *메서드*합니다. | 이 이벤트는 사용자가 개별 메서드를 등록 하면 발생 합니다. *메서드* Authenticator 앱, 전화, 전자 메일, 보안 질문, 앱 암호, 대체 전화 등에 될 수 있습니다.| 
| 검토 하는 사용자 보안 정보 | 성공 | 사용자 보안 정보를 성공적으로 검토합니다. | 사용자가 선택 하는 경우이 이벤트가 발생 **좋으면** 보안 정보 검토 페이지에서.|
| 검토 하는 사용자 보안 정보 | 실패 | 사용자 보안 정보를 검토 하지 못했습니다. | 사용자가 선택 하는 경우이 이벤트가 발생 **좋으면** 백 엔드에서 오류가 발생 하지만 보안 정보 페이지를 검토 합니다.|
| 삭제 된 사용자 보안 정보 | 성공 | 사용자가 삭제 *메서드*합니다. | 이 이벤트는 사용자는 개별 메서드를 삭제할 때 발생 합니다. *메서드* Authenticator 앱, 전화, 전자 메일, 보안 질문, 앱 암호, 대체 전화 등에 될 수 있습니다.|
| 삭제 된 사용자 보안 정보 | 실패 | 사용자를 삭제 하지 못했습니다 *메서드*합니다. | 이 이벤트는 사용자가 메서드를 삭제 하려고 하지만 어떤 이유로 실패 하는 경우에 발생 합니다. *메서드* Authenticator 앱, 전화, 전자 메일, 보안 질문, 앱 암호, 대체 전화 등에 될 수 있습니다.|
| 변경할 사용자 기본 보안 정보 | 성공 | 사용자에 대 한 기본 보안 정보를 변경 *메서드*합니다. | 이 이벤트는 사용자 기본 메서드를 변경할 때 발생 합니다. *메서드* Authenticator 앱 알림, 내 authenticator 앱 또는 토큰, 호출 + X XXXXXXXXXX, 텍스트에서 코드를 코드 + X XXXXXXXXX를 고 수 등에입니다.|
| 변경할 사용자 기본 보안 정보 | 실패 | 사용자에 대 한 기본 보안 정보를 변경 하지 못했습니다 *메서드*합니다. | 이 이벤트는 사용자가 기본 메서드를 변경 하려고 시도 하지만 어떤 이유로 실패 하는 경우에 발생 합니다. *메서드* Authenticator 앱 알림, 내 authenticator 앱 또는 토큰, 호출 + X XXXXXXXXXX, 텍스트에서 코드를 코드 + X XXXXXXXXX를 고 수 등에입니다.|

## <a name="troubleshooting-interrupt-mode"></a>인터럽트 모드 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 참조 해야 하는 경우 메서드 표시 되지 않습니다. | 1. 사용자에 게 Azure AD 관리자 역할이 있는지 확인 합니다. 그렇다면 SSPR 관리자 정책 차이 확인 합니다. <br> 2. 사용자 등록 적용 Multi-factor Authentication 또는 SSPR 등록 적용으로 인해 중단 되 고 있는지 여부를 결정 합니다. 참조 된 [순서도](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) "결합 등록 모드"에서 메서드를 표시 해야 결정할 합니다. <br> 3. Multi-factor Authentication 또는 SSPR 정책을 변경한 경과한 기간을 결정 합니다. 최근 변경 되었으면 전파 하는 업데이트 된 정책을 다소 시간이 걸릴 수 있습니다.|

## <a name="troubleshooting-manage-mode"></a>Mode 관리 문제 해결

| 증상 | 문제 해결 단계 |
| --- | --- |
| 특정 메서드를 추가 하는 옵션이 없습니다. | 1. Multi-factor authentication 또는 SSPR에 대 한 메서드를 사용 되는지 여부를 결정 합니다. <br> 2. 메서드를 사용 하는 경우 정책을 다시 저장 하 고 다시 테스트 하기 전에 1 ~ 2 시간 동안 대기 합니다. <br> 3. 메서드를 사용 하는 경우 설정 되었는지 확인 사용자 않은 이미 최대 수를 설정 하도록 허용 하는 메서드입니다.|

## <a name="disable-combined-registration"></a>결합 된 등록을 사용 하지 않도록 설정

사용자 새 전화 번호 및/또는 모바일 앱을 등록할 때 결합 환경에는 서비스 스탬프 (StrongAuthenticationMethods) 해당 사용자에 해당 메서드에 대 한 플래그 집합입니다. 이 기능을 통해 사용자가 Multi-factor Authentication이 필요할 때마다 해당 메서드를 사용 하 여 Multi-factor Authentication을 수행 합니다.

관리자 미리 보기를 사용 하도록 설정 하 고 새로운 경험을 통해 사용자가 등록 관리자 미리 보기를 비활성화 한 다음, 사용자가 자신도 등록 Multi-factor Authentication에 대 한도입니다.

결합 된 등록을 완료 하는 사용자가 현재 셀프 서비스 암호 재설정 (SSPR) 등록 페이지에서 이동 하는 경우 [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), 사용자에 액세스 하기 전에 Multi-factor Authentication을 수행 하 라는 메시지가 표시 됩니다 해당 페이지입니다. 이 단계는 기술 관점에서 하는데 이전에 등록 된 SSPR에 대 한만 사용자에 대 한 새로운 합니다. 이 추가 단계는 다른 수준의 보안을 제공 하 여 사용자의 보안 태세 개선지 않습니다, 하지만 관리자가 더 이상 Multi-factor Authentication을 수행할 수 있도록 사용자에 게 다시 배포 하려고 합니다.  

### <a name="how-to-roll-back-users"></a>사용자를 롤백하는 방법

관리자로 서 원하는 사용자의 Multi-factor Authentication 설정을 다시 설정 하는 경우 다음 섹션에서 제공 하는 PowerShell 스크립트를 사용할 수 있습니다. 스크립트는 사용자의 모바일 앱 및/또는 전화 번호의 StrongAuthenticationMethods 속성이 지워집니다. 사용자에 게이 스크립트를 실행 하는 경우 필요할 경우 Multi-factor Authentication에 대해 다시 등록 해야 합니다. 모든 영향을 받는 사용자를 롤백하기 전에 하나 이상의 사용자를 사용 하 여 테스트 롤백을 권장 합니다.

다음 단계를 사용자 또는 사용자 그룹을 롤백해야 하는 데 도움이 됩니다.

#### <a name="prerequisites"></a>필수 조건

1. 적절 한 Azure AD PowerShell 모듈을 설치 합니다. PowerShell 창에서 다음 명령을 실행하여 모듈을 설치합니다.

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 컴퓨터에 영향을 받는 사용자 개체 Id 목록을 줄당 하나의 ID 사용 하 여 텍스트 파일로 저장 합니다. 파일 위치를 적어 둡니다.
1. 컴퓨터에 다음 스크립트를 저장 하 고 스크립트의 위치를 기록해 키를 누릅니다.

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

다음 명령을 PowerShell 창에서 스크립트 및 사용자 파일 위치를 제공 합니다. 메시지가 표시되면 글로벌 관리자 자격 증명을 입력합니다. 스크립트가 각 사용자 업데이트 작업의 결과를 출력합니다.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>미리 보기 환경을 사용 하지 않도록 설정

사용자에 대 한 미리 보기 환경을 사용 하지 않으려면, 이러한 단계를 완료 합니다.

1. 사용자 관리자는 Azure portal에 로그인 합니다.
2. 로 이동 **Azure Active Directory** > **사용자 설정** > **액세스 패널 미리 보기 기능에 대 한 설정을 관리**합니다.
3. 아래 **사용자는 등록 하 고 보안 정보 관리에 대 한 미리 보기 기능을 사용할 수 있습니다**, 선택기로 **없음**를 선택한 후 **저장**합니다.

더 이상 메시지가 표시 됩니다 미리 보기 환경을 사용 하 여 등록 합니다.

## <a name="next-steps"></a>다음 단계

* [셀프 서비스 암호 재설정 및 Azure Multi-factor Authentication에 대 한 결합 된 등록의 공개 미리 보기에 자세히 알아보기](concept-registration-mfa-sspr-combined.md)
