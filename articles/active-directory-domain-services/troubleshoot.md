---
title: Azure Active Directory 도메인 서비스 문제 해결 | 마이크로소프트 문서'
description: Azure Active Directory 도메인 서비스를 만들거나 관리할 때 일반적인 오류 문제를 해결하는 방법 알아보기
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 84efe294533186fdcf2e0a3356a7d6b01eccaf5f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654399"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure Active Directory 도메인 서비스에 대한 일반적인 오류 및 문제 해결 단계

응용 프로그램에 대한 ID 및 인증의 핵심 부분으로 Azure Active Directory 도메인 서비스(Azure AD DS)에 문제가 있는 경우가 있습니다. 문제가 발생하면 몇 가지 일반적인 오류 메시지와 관련된 문제 해결 단계가 있어 다시 실행될 수 있습니다. 언제든지 추가 문제 해결 지원을 위해 [Azure 지원 요청을 열][azure-support] 수도 있습니다.

이 문서에서는 Azure AD DS의 일반적인 문제에 대한 문제 해결 단계를 제공합니다.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Azure AD 디렉터리에 대해 Azure AD Domain Services를 사용할 수 없습니다.

Azure AD DS를 사용하도록 설정하는 데 문제가 있는 경우 다음과 같은 일반적인 오류 및 단계를 검토하여 해결합니다.

| **샘플 오류 메시지** | **해상도** |
| --- |:--- |
| *aaddscontoso.com 이름은 이 네트워크에서 이미 사용 중입니다. 사용하지 않는 이름을 지정합니다.* |[가상 네트워크에서 도메인 이름 충돌](troubleshoot.md#domain-name-conflict) |
| *이 Azure AD 테넌트에서 도메인 서비스를 사용할 수 없습니다. 서비스에는 'Azure AD 도메인 서비스 동기화'라는 응용 프로그램에 대한 적절한 사용 권한이 없습니다. 'Azure AD 도메인 서비스 동기화'라는 응용 프로그램을 삭제한 다음 Azure AD 테넌트에 대한 도메인 서비스를 사용하도록 설정합니다.* |[도메인 서비스에 Azure AD 도메인 서비스 동기화 응용 프로그램에 대 한 적절 한 권한이 없습니다.](troubleshoot.md#inadequate-permissions) |
| *이 Azure AD 테넌트에서 도메인 서비스를 사용할 수 없습니다. Azure AD 테넌트의 도메인 서비스 응용 프로그램에도메인 서비스를 사용하도록 설정하는 데 필요한 권한이 없습니다. 응용 프로그램 식별자 d87dcbc6-a371-462e-88e3-28ad15ec4e64를 사용하여 응용 프로그램을 삭제한 다음 Azure AD 테넌트에 대한 도메인 서비스를 사용하도록 설정합니다.* |[Azure AD 테넌트에서 도메인 서비스 응용 프로그램이 제대로 구성되지 않았습니다.](troubleshoot.md#invalid-configuration) |
| *이 Azure AD 테넌트에서 도메인 서비스를 사용할 수 없습니다. Azure AD 테넌트에서 Microsoft Azure AD 응용 프로그램이 비활성화됩니다. 응용 프로그램 식별자 00000000-00000-c0000-000000000000000을 사용하여 응용 프로그램을 활성화한 다음 Azure AD 테넌트에 대한 도메인 서비스를 사용하도록 설정합니다.* |[Azure AD 테넌트에서 Microsoft Graph 애플리케이션을 사용할 수 없음](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>도메인 이름 충돌

**오류 메시지**

*aaddscontoso.com 이름은 이 네트워크에서 이미 사용 중입니다. 사용하지 않는 이름을 지정합니다.*

**해상도**

동일한 도메인 이름을 가진 기존 AD DS 환경이 없는지 또는 피어있는 가상 네트워크가 없는지 확인합니다. 예를 들어 Azure VM에서 실행되는 *aaddscontoso.com라는* AD DS 도메인이 있을 수 있습니다. 가상 네트워크에서 *aaddscontoso.com 동일한* 도메인 이름을 가진 Azure AD DS 관리 도메인을 사용하도록 설정하려고 하면 요청된 작업이 실패합니다.

이 오류는 가상 네트워크의 도메인 이름에 대한 이름 충돌로 인해 발생합니다. DNS 조회는 기존 AD DS 환경이 요청된 도메인 이름에 응답하는지 확인합니다. 이 오류를 해결하려면 다른 이름을 사용하여 Azure AD DS 관리 도메인을 설정하거나 기존 AD DS 도메인의 프로비저닝을 해제한 다음 다시 시도하여 Azure AD DS를 사용하도록 설정합니다.

### <a name="inadequate-permissions"></a>부적절한 권한

**오류 메시지**

*이 Azure AD 테넌트에서 도메인 서비스를 사용할 수 없습니다. 서비스에는 'Azure AD 도메인 서비스 동기화'라는 응용 프로그램에 대한 적절한 사용 권한이 없습니다. 'Azure AD 도메인 서비스 동기화'라는 응용 프로그램을 삭제한 다음 Azure AD 테넌트에 대한 도메인 서비스를 사용하도록 설정합니다.*

**해상도**

Azure AD 디렉터리에서 *Azure AD 도메인 서비스 동기화라는* 응용 프로그램이 있는지 확인합니다. 이 응용 프로그램이 있는 경우 삭제한 다음 다시 시도하여 Azure AD DS를 사용하도록 설정합니다. 기존 응용 프로그램을 확인하고 필요한 경우 삭제하려면 다음 단계를 완료하십시오.

1. Azure 포털에서 왼쪽 탐색 메뉴에서 **Azure Active Directory를** 선택합니다.
1. **Enterprise 애플리케이션**을 선택합니다. **응용 프로그램 유형** 드롭다운 메뉴에서 모든 응용 *프로그램을* 선택한 다음 **적용을**선택합니다.
1. 검색 상자에서 *Azure AD 도메인 서비스 동기화를*입력합니다. 응용 프로그램이 있는 경우 응용 프로그램을 선택하고 **삭제를**선택합니다.
1. 응용 프로그램을 삭제한 후에는 Azure AD DS를 다시 사용하도록 설정해 보십시오.

### <a name="invalid-configuration"></a>유효하지 않은 구성

**오류 메시지**

*이 Azure AD 테넌트에서 도메인 서비스를 사용할 수 없습니다. Azure AD 테넌트의 도메인 서비스 응용 프로그램에도메인 서비스를 사용하도록 설정하는 데 필요한 권한이 없습니다. 응용 프로그램 식별자 d87dcbc6-a371-462e-88e3-28ad15ec4e64를 사용하여 응용 프로그램을 삭제한 다음 Azure AD 테넌트에 대한 도메인 서비스를 사용하도록 설정합니다.*

**해상도**

Azure AD 디렉터리에서 *d87dcbc6-a371-462e-88e3-28ad15ec4e64의* 응용 프로그램 식별자가 있는 *AzureActiveDirectoryDomainControllerServices라는* 기존 응용 프로그램이 있는지 확인합니다. 이 응용 프로그램이 있는 경우 해당 응용 프로그램을 삭제한 다음 다시 시도하여 Azure AD DS를 사용하도록 설정합니다.

다음 PowerShell 스크립트를 사용하여 기존 응용 프로그램 인스턴스를 검색하고 필요한 경우 삭제합니다.

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph 사용 안 함

**오류 메시지**

*이 Azure AD 테넌트에서 도메인 서비스를 사용할 수 없습니다. Azure AD 테넌트에서 Microsoft Azure AD 응용 프로그램이 비활성화됩니다. 응용 프로그램 식별자 00000000-00000-c0000-000000000000000을 사용하여 응용 프로그램을 활성화한 다음 Azure AD 테넌트에 대한 도메인 서비스를 사용하도록 설정합니다.*

**해상도**

식별자 *00000002-0000-0000-0000-000000000000000000000000000으로*응용 프로그램을 사용하지 않도록 설정했는지 확인합니다. 이 애플리케이션은 Microsoft Azure AD 애플리케이션이며 Azure AD 테넌트에 Graph API 액세스를 제공합니다. Azure AD 테넌트를 동기화하려면 이 응용 프로그램을 사용하도록 설정해야 합니다.

이 응용 프로그램의 상태를 확인하고 필요한 경우 활성화하려면 다음 단계를 완료하십시오.

1. Azure 포털에서 왼쪽 탐색 메뉴에서 **Azure Active Directory를** 선택합니다.
1. **Enterprise 애플리케이션**을 선택합니다. **응용 프로그램 유형** 드롭다운 메뉴에서 모든 응용 *프로그램을* 선택한 다음 **적용을**선택합니다.
1. 검색 상자에 *00000002-0000-0000-c0000-0000000000000000000000을*입력합니다. 응용 프로그램을 선택한 다음 **속성**을 선택합니다.
1. **사용자가 로그인할 수 있도록 활성화된** 경우 *아니요로*설정되어 있는 경우 값을 *예로*설정한 다음 **저장을**선택합니다.
1. 응용 프로그램을 활성화한 후에는 Azure AD DS를 다시 사용하도록 설정해 보십시오.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>사용자는 Azure AD 도메인 서비스 관리된 도메인에 로그인할 수 없습니다.

Azure AD 테넌트의 사용자 중 하나 이상이 Azure AD DS 관리 도메인에 로그인할 수 없는 경우 다음 문제 해결 단계를 완료합니다.

* **자격 증명 형식** - UPN 형식을 사용하여 자격 `dee@aaddscontoso.onmicrosoft.com`증명(예: 자격 증명을 지정해 보십시오.) UPN 형식은 Azure AD DS에서 자격 증명을 지정하는 데 권장되는 방법입니다. 이 UPN이 Azure AD에서 올바르게 구성되었는지 확인합니다.

    테넌트에 동일한 UPN 접두사를 가진 사용자가 여러 명이 있거나 UPN 접두사가 지나치게 긴 경우 *AADDSCONTOSO\driley와* 같은 계정의 *SAMAccountName이* 자동 생성될 수 있습니다. 따라서 계정의 *SAMAccountName* 형식은 온-프레미스 도메인에서 예상하거나 사용하는 것과 다를 수 있습니다.

* **암호 동기화** - Azure [AD Connect를 사용하여][hybrid-phs] [클라우드 전용 사용자][cloud-only-passwords] 또는 하이브리드 환경에 암호 동기화를 사용하도록 설정했는지 확인합니다.
    * **하이브리드 동기화 계정:** 영향을 받는 사용자 계정이 온-프레미스 디렉터리에서 동기화되는 경우 다음 영역을 확인합니다.
    
      * [Azure AD Connect의 최신 권장 릴리스를](https://www.microsoft.com/download/details.aspx?id=47594)배포하거나 업데이트했습니다.
      * [전체 동기화를 수행하도록][hybrid-phs]Azure AD Connect를 구성했습니다.
      * 디렉터리 크기에 따라 Azure AD DS에서 사용자 계정 및 자격 증명 해시를 사용할 수 있는 데 시간이 걸릴 수 있습니다. 관리되는 도메인에 대해 인증을 시도하기 전에 충분히 기다려야 합니다.
      * 이전 단계를 확인한 후에도 문제가 지속되면 *Microsoft Azure AD 동기화 서비스를*다시 시작해 보십시오. Azure AD Connect 서버에서 명령 프롬프트를 열고 다음 명령을 실행합니다.
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **클라우드 전용 계정**: 영향을 받는 사용자 계정이 클라우드 전용 사용자 계정인 경우 [Azure AD DS를 사용하도록 설정한 후 사용자가 암호를 변경했는지][cloud-only-passwords]확인합니다. 이 암호 재설정으로 인해 Azure AD 도메인 서비스에 필요한 자격 증명 해시가 생성됩니다.

* **사용자 계정이 활성 상태인지 확인:** 기본적으로 관리 되는 도메인에서 2 분 이내에 5 개의 유효 하지 않은 암호 시도 30 분 동안 사용자 계정이 잠깁니다. 계정이 잠겨 있는 동안에는 사용자가 로그인할 수 없습니다. 30분 후에는 사용자 계정이 자동으로 잠금 해제됩니다.
  * Azure AD DS 관리 도메인에서 잘못된 암호 시도는 Azure AD의 사용자 계정을 잠그지 않습니다. 사용자 계정은 관리되는 도메인 내에서만 잠겨 있습니다. Azure AD가 아닌 [관리 VM을][management-vm]사용하여 *ADAC(활성 디렉터리 관리 콘솔)의* 사용자 계정 상태를 확인합니다.
  * 기본 잠금 임계값 및 기간을 변경하도록 [세분화된 암호 정책을 구성할][password-policy] 수도 있습니다.

* **외부 계정** - 영향을 받는 사용자 계정이 Azure AD 테넌트의 외부 계정이 아닌지 확인합니다. 외부 계정의 예로는 외부 `dee@live.com` Azure AD 디렉터리에서 Microsoft 계정 또는 사용자 계정이 있습니다. Azure AD DS는 관리되는 도메인에 로그인할 수 없도록 외부 사용자 계정에 대한 자격 증명을 저장하지 않습니다.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>관리되는 도메인에서 하나 이상의 경고 발생

Azure AD DS 관리 도메인에 활성 경고가 있는 경우 인증 프로세스가 제대로 작동하지 않을 수 있습니다.

활성 경고가 있는지 확인하려면 [Azure AD DS 관리 도메인의 상태 상태를 확인합니다.][check-health] 경고가 표시되면 [문제를 해결하고 해결합니다.][troubleshoot-alerts]

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Azure AD 테넌트에서는 제거되지만 관리되는 도메인에서는 제거되지 않는 사용자

Azure AD는 사용자 개체의 실수로 삭제되지 않도록 보호합니다. Azure AD 테넌트에서 사용자 계정을 삭제하면 해당 사용자 개체가 휴지통으로 이동됩니다. 이 삭제 작업이 Azure AD DS 관리 도메인과 동기화되면 해당 사용자 계정이 비활성화된 것으로 표시됩니다. 이 기능은 사용자 계정을 복구하거나 삭제취소하는 데 도움이 됩니다.

Azure AD 디렉터리에서 동일한 UPN을 가진 사용자 계정을 다시 만드는 경우에도 사용자 계정은 Azure AD DS 관리 도메인의 사용 안 됨 상태로 유지됩니다. Azure AD DS 관리 도메인에서 사용자 계정을 제거하려면 Azure AD 테넌트에서 강제로 삭제해야 합니다.

Azure AD DS 관리 도메인에서 사용자 계정을 완전히 제거하려면 `-RemoveFromRecycleBin` 매개 변수가 있는 [제거-MsolUser][Remove-MsolUser] PowerShell cmdlet을 사용하여 Azure AD 테넌트에서 사용자를 영구적으로 삭제합니다.

## <a name="next-steps"></a>다음 단계

문제가 계속 발생하면 Azure 지원 요청을 열어 추가 문제 해결 지원을 [제공합니다.][azure-support]

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
