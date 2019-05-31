---
title: B2B 게스트 사용자의 일회성 암호 인증 - Azure Active Directory | Microsoft Docs
description: 메일 일회성 암호를 사용하여 Microsoft 계정을 사용하지 않고 B2B 게스트 사용자를 인증하는 방법입니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13808871d67bb47dce82c5a3493fd89b0dfe1dcd
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952850"
---
# <a name="email-one-time-passcode-authentication-preview"></a>메일 일회성 암호 인증(미리 보기)

|     |
| --- |
| 메일 일회성 암호는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

이 문서에서는 B2B 게스트 사용자에 게 전자 메일 (일회성 암호) 인증을 사용 하도록 설정 하는 방법을 설명 합니다. 메일 일회성 암호 기능은 B2B 게스트 사용자가 Azure AD, Microsoft 계정(MSA) 또는 Google 페더레이션과 같은 다른 수단을 통해 인증할 수 없을 때 해당 사용자를 인증합니다. 일회성 암호 인증을 사용하면 Microsoft 계정을 만들 필요가 없습니다. 게스트 사용자는 초대를 사용하거나 공유 리소스에 액세스할 때 메일 주소로 전송되는 임시 코드를 요청할 수 있습니다. 그런 다음, 이 코드를 입력하여 로그인을 계속합니다.

이 기능은 현재 미리 보기로 제공됩니다(아래 [미리 보기에 옵트인](#opting-in-to-the-preview) 참조). 미리 보기 후에 이 기능은 기본적으로 모든 테넌트에 대해 켜집니다.

> [!NOTE]
> 일회성 암호 사용자는 테넌트 컨텍스트를 포함하는 링크를 사용하여 로그인해야 합니다(예: `https://myapps.microsoft.com/?tenantid=<tenant id>` 또는 `https://portal.azure.com/<tenant id>`, 혹은 확인된 도메인의 경우 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). 애플리케이션 및 리소스에 대한 직접 링크는 테넌트 컨텍스트를 포함하는 한 작동합니다. 게스트 사용자는 현재 테넌트 컨텍스트가 없는 엔드포인트를 사용하여 로그인할 수 없습니다. 예를 들어 `https://myapps.microsoft.com`, `https://portal.azure.com` 또는 팀 공통 엔드포인트를 사용하면 오류가 발생합니다. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>일회성 암호 게스트 사용자를 위한 사용자 환경
일회성 암호 인증을 사용하면 게스트 사용자가 직접 링크를 클릭하거나 초대 메일을 사용하여 초대를 사용할 수 있습니다. 두 경우 모두 코드가 게스트 사용자의 메일 주소로 전송된다는 메시지가 브라우저에 표시됩니다. 게스트 사용자가 **코드 보내기**를 선택합니다.
 
   ![송신 코드 단추를 보여 주는 스크린샷](media/one-time-passcode/otp-send-code.png)
 
암호가 사용자의 메일 주소로 전송됩니다. 사용자가 메일에서 암호를 검색하고 브라우저 창에 입력합니다.
 
   ![Enter 코드 페이지를 보여 주는 스크린샷](media/one-time-passcode/otp-enter-code.png)
 
이제 게스트 사용자가 인증되었고 공유 리소스를 보거나 로그인을 계속할 수 있습니다. 

> [!NOTE]
> 일회성 암호는 30분 동안 유효합니다. 30분이 지나면 특정 일회성 암호가 더 이상 유효하지 않고 사용자는 새 암호를 요청해야 합니다. 사용자 세션은 24시간 후에 만료됩니다. 해당 시간이 지나면 게스트 사용자는 리소스에 액세스할 때 새 암호를 받습니다. 특히 게스트 사용자가 회사를 떠나거나 더 이상 액세스할 필요가 없는 경우 세션 만료로 보안을 강화합니다.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>게스트 사용자가 일회성 암호를 얻을 때는 언제인가요?

게스트 사용자가 초대를 사용하거나 공유된 리소스의 링크를 사용할 때 일회성 암호를 받는 경우는 다음과 같습니다.
- Azure AD 계정이 없는 경우 
- Microsoft 계정이 없는 경우 
- 초대하는 테넌트가 @gmail.com 및 @googlemail.com 사용자에 대해 Google 페더레이션을 설정하지 않은 경우 

초대할 때 초대를 받는 사용자가 일회성 암호 인증을 사용할 것이라는 표시는 없습니다. 그러나 게스트 사용자가 로그인할 때 다른 인증 방법을 사용할 수 없다면 일회성 암호 인증이 대체 방법이 됩니다. 

**Azure Active Directory** > **조직 관계** > **다른 조직의 사용자**로 이동하여 Azure Portal에서 일회성 암호로 인증하는 게스트 사용자를 볼 수 있습니다.

![원본 값이 OTP (일회성 암호) 사용자를 보여 주는 스크린샷](media/one-time-passcode/otp-users.png)

> [!NOTE]
> 사용자가 일회성 암호를 사용하고 나중에 MSA, Azure AD 계정 또는 기타 페더레이션 계정을 얻을 경우 일회성 암호를 사용하여 계속 인증됩니다. 인증 방법을 업데이트하려면 해당 게스트 사용자 계정을 삭제하고 사용자를 다시 초대하면 됩니다.

### <a name="example"></a>예
게스트 사용자 alexdoe@gmail.com이 Google 페더레이션이 설정되지 않은 Fabrikam에 초대되었습니다. Alex는 Microsoft 계정이 없습니다. 인증에 대 한 일회용 암호를 받게 됩니다.

## <a name="opting-in-to-the-preview"></a>미리 보기에 옵트인 
옵트인 작업을 적용하는 데 몇 분 정도 걸릴 수 있습니다. 적용된 후에는 위의 조건을 충족하는 새로 초대된 사용자만 일회성 암호 인증을 사용합니다. 이전에 초대를 사용한 게스트 사용자는 동일한 인증 방법을 계속 사용합니다.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Azure AD 포털을 사용하여 옵트인하려면
1.  Azure AD 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  탐색 창에서 **Azure Active Directory**를 선택합니다.
3.  **관리**에서 **조직 관계**를 선택합니다.
4.  **설정**을 선택합니다.
5.  **게스트에 메일 일회용 암호 사용(미리 보기)** 에서 **예**를 선택합니다.
 
### <a name="to-opt-in-using-powershell"></a>PowerShell을 사용하여 옵트인하려면

먼저 Graph 모듈에 대한 Azure AD PowerShell의 최신 버전(AzureADPreview)을 설치해야 합니다. 그런 다음, B2B 정책이 이미 존재하는지 확인하고 적절한 명령을 실행합니다.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>필수 조건: 최신 AzureADPreview 모듈 설치
먼저 설치한 모듈을 확인합니다. 관리자 권한 사용자로 Windows PowerShell을 열고(관리자 권한으로 실행) 다음 명령을 실행합니다.
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

이후 버전이 있음을 나타내는 메시지 없이 AzureADPreview 모듈이 표시되면 제대로 설정된 것입니다. 그렇지 않으면 출력에 따라 다음 중 하나를 수행합니다.

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

#### <a name="check-for-existing-policies-and-opt-in"></a>기존 정책 확인 및 옵트인

이어서 다음을 실행하여 B2BManagementPolicy가 현재 존재하는지 확인합니다.

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- 출력이 False이면 현재 정책이 존재하지 않습니다. 다음을 실행하여 새 B2BManagementPolicy를 만들고 미리 보기에 옵트인합니다.

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- 출력이 True이면 현재 B2BManagementPolicy 정책이 존재합니다. 정책을 업데이트하고 미리 보기에 옵트인하려면 다음을 실행합니다.
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>옵트인한 후 미리 보기에서 옵트아웃
옵트아웃 작업을 적용하는 데 몇 분 정도 걸릴 수 있습니다. 미리 보기를 끄면 일회성 암호를 사용한 모든 게스트 사용자가 로그인할 수 없습니다. 게스트 사용자를 삭제하고 다른 인증 방법을 사용하여 다시 로그인할 수 있도록 사용자를 다시 초대할 수 있습니다.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Azure AD 포털을 사용하여 미리 보기를 끄려면
1.  Azure AD 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  탐색 창에서 **Azure Active Directory**를 선택합니다.
3.  **관리**에서 **조직 관계**를 선택합니다.
4.  **설정**을 선택합니다.
5.  **게스트에 메일 일회용 암호 사용(미리 보기)** 에서 **아니요**를 선택합니다.

### <a name="to-turn-off-the-preview-using-powershell"></a>PowerShell을 사용하여 미리 보기를 끄려면
최신 AzureADPreview 모듈이 없는 경우 모듈을 설치합니다(위의 [필수 구성 요소: 최신 AzureADPreview 모듈 설치](#prerequisite-install-the-latest-azureadpreview-module) 참조). 그런 다음, 다음을 실행하여 현재 일회성 암호 미리 보기 정책이 존재하는지 확인합니다.

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

출력이 True이면 다음을 실행하여 미리 보기에서 옵트아웃합니다.

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

