---
title: Azure Active Directory Domain Services 문제 해결 | Microsoft Docs
description: Azure Active Directory Domain Services를 만들거나 관리할 때 발생하는 일반적인 오류를 해결하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 89b04f86d41f8e4828580f70a9aec8acea3e0053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618453"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services와 관련된 일반적인 오류와 문제 해결 단계

애플리케이션 ID와 인증의 중심적 부분인 Azure AD DS(Azure Active Directory Domain Services)에 문제가 있는 경우가 있습니다. 문제가 발생하는 경우 나타나는 몇 가지 일반적 에러 메시지와 다시 작동하는 데 도움이 되는 관련 문제 해결 단계가 있습니다. 언제든 [Azure 지원 요청을 열고][azure-support] 추가적인 문제 해결 지원을 받을 수도 있습니다.

이 문서에서는 Azure AD DS의 일반적인 문제에 대한 문제 해결 단계를 제공합니다.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Azure AD 디렉터리에 대해 Azure AD Domain Services를 사용할 수 없습니다.

Azure AD DS를 사용하도록 설정하는 데 문제가 있는 경우 다음과 같은 일반적인 오류와 단계를 검토하여 해결합니다.

| **샘플 오류 메시지** | **해결 방법** |
| --- |:--- |
| ‘이름은 이 네트워크에서 이미 사용 중입니다. 사용하고 있지 않은 이름을 지정하세요.’ |[가상 네트워크에서 도메인 이름 충돌](troubleshoot.md#domain-name-conflict) |
| *이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. 이 서비스에는 'Azure AD Domain Services Sync'라는 애플리케이션에 대한 적절한 권한이 없습니다. 라는 애플리케이션을 삭제한 후 테넌트에 대해 Domain Services를 사용하도록 설정하세요.* |[Domain Services에 Azure AD Domain Services Sync 애플리케이션에 대한 적절한 권한이 없음](troubleshoot.md#inadequate-permissions) |
| *이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. Azure AD 테넌트의 Domain Services 애플리케이션에는 Domain Services를 사용하는 데 필요한 권한이 없습니다. 애플리케이션 식별자가 d87dcbc6-a371-462e-88e3-28ad15ec4e64인 애플리케이션을 삭제한 후 테넌트에 대해 Domain Services를 사용하도록 설정하세요.* |[Azure AD 테넌트에서 Domain Services 애플리케이션을 제대로 구성하지 않음](troubleshoot.md#invalid-configuration) |
| *이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. Azure AD 테넌트에서 Microsoft Azure AD 애플리케이션을 사용할 수 없습니다. 00000002-0000-0000-c000-000000000000 애플리케이션 식별자를 사용하여 애플리케이션을 사용하도록 설정한 다음, Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정합니다.* |[Azure AD 테넌트에서 Microsoft Graph 애플리케이션을 사용할 수 없음](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>도메인 이름 충돌

**오류 메시지**

‘이름은 이 네트워크에서 이미 사용 중입니다. 사용하고 있지 않은 이름을 지정하세요.’

**해결 방법**

동일한 또는 피어링된 가상 네트워크에 동일한 도메인 이름을 가진 기존 AD DS 환경이 없는지 확인합니다. 예를 들어 Azure VM에서 실행되는 *aaddscontoso.com* 이라는 AD DS 도메인이 있을 수 있습니다. 가상 네트워크에서 *aaddscontoso.com* 이라는 동일한 도메인 이름을 사용하여 Azure AD DS 관리되는 도메인을 사용하도록 설정하려고 하면 요청된 작업이 실패합니다.

이 오류는 해당 가상 네트워크에서 도메인 이름이 충돌하기 때문에 발생합니다. DNS 조회는 기존 AD DS 환경이 요청된 도메인 이름에 응답하는지 확인합니다. 이 오류를 해결하려면 다른 이름을 사용하여 관리되는 도메인을 설정하거나 기존 AD DS 도메인의 프로비저닝을 해제한 다음, Azure AD DS 사용 설정을 다시 시도합니다.

### <a name="inadequate-permissions"></a>부적절한 권한

**오류 메시지**

*이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. 이 서비스에는 'Azure AD Domain Services Sync'라는 애플리케이션에 대한 적절한 권한이 없습니다. 라는 애플리케이션을 삭제한 후 테넌트에 대해 Domain Services를 사용하도록 설정하세요.*

**해결 방법**

Azure AD 디렉터리에 *Azure AD Domain Services Sync* 라는 애플리케이션이 있는지 확인합니다. 이 애플리케이션이 있는 경우 삭제한 다음, Azure AD DS 사용 설정을 다시 시도합니다. 다음 단계를 완료하여 기존 애플리케이션을 확인하고 필요한 경우 삭제합니다.

1. Azure Portal의 왼쪽 탐색 메뉴에서 **Azure Active Directory** 를 선택합니다.
1. **Enterprise 애플리케이션** 을 선택합니다. *애플리케이션 종류* 드롭다운 메뉴에서 **모든 애플리케이션** 을 선택한 다음, **적용** 을 선택합니다.
1. 검색 상자에 *Azure AD Domain Services Sync* 를 입력합니다. 애플리케이션이 있는 경우 해당 애플리케이션을 선택하고 **삭제** 를 선택합니다.
1. 애플리케이션을 삭제한 후에 Azure AD DS 사용 설정을 다시 시도합니다.

### <a name="invalid-configuration"></a>유효하지 않은 구성

**오류 메시지**

*이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. Azure AD 테넌트의 Domain Services 애플리케이션에는 Domain Services를 사용하는 데 필요한 권한이 없습니다. 애플리케이션 식별자가 d87dcbc6-a371-462e-88e3-28ad15ec4e64인 애플리케이션을 삭제한 후 테넌트에 대해 Domain Services를 사용하도록 설정하세요.*

**해결 방법**

Azure AD 디렉터리에 *AzureActiveDirectoryDomainControllerServices*(애플리케이션 식별자: *d87dcbc6-a371-462e-88e3-28ad15ec4e64*)라는 이름의 애플리케이션이 있는지 확인합니다. 이 애플리케이션이 있는 경우 해당 애플리케이션을 삭제한 다음, Azure AD DS 사용 설정을 다시 시도합니다.

다음 PowerShell 스크립트를 사용하여 기존 애플리케이션 인스턴스를 검색하고 필요한 경우 삭제합니다.

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

*이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. Azure AD 테넌트에서 Microsoft Azure AD 애플리케이션을 사용할 수 없습니다. 00000002-0000-0000-c000-000000000000 애플리케이션 식별자를 사용하여 애플리케이션을 사용하도록 설정한 다음, Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정합니다.*

**해결 방법**

*00000002-0000-0000-c000-000000000000* 식별자를 사용하는 애플리케이션을 사용하지 않도록 설정했는지 확인합니다. 이 애플리케이션은 Microsoft Azure AD 애플리케이션이며 Azure AD 테넌트에 Graph API 액세스를 제공합니다. Azure AD 테넌트를 동기화하려면 이 애플리케이션을 사용하도록 설정해야 합니다.

다음 단계를 완료하여 이 애플리케이션의 상태를 확인하고 필요한 경우 사용하도록 설정합니다.

1. Azure Portal의 왼쪽 탐색 메뉴에서 **Azure Active Directory** 를 선택합니다.
1. **Enterprise 애플리케이션** 을 선택합니다. *애플리케이션 종류* 드롭다운 메뉴에서 **모든 애플리케이션** 을 선택한 다음, **적용** 을 선택합니다.
1. 검색 상자에 *00000002-0000-0000-c000-00000000000* 을 입력합니다. 애플리케이션을 선택한 다음, **속성** 을 선택합니다.
1. **사용자가 로그인할 수 있도록 설정** 이 ‘아니요’로 설정되어 있으면 값을 ‘예’로 설정하고 **저장** 을 선택합니다. 
1. 애플리케이션을 사용하도록 설정한 후에 Azure AD DS 사용 설정을 다시 시도합니다.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>사용자는 Azure AD 도메인 서비스 관리된 도메인에 로그인할 수 없습니다.

Azure AD 테넌트에서 한 명 이상의 사용자가 관리되는 도메인에 로그인할 수 없는 경우 다음 문제 해결 단계를 완료합니다.

* **자격 증명의 형식** - UPN 형식을 사용하여 자격 증명을 지정합니다(예: `dee@aaddscontoso.onmicrosoft.com`). UPN 형식은 Azure AD DS에서 자격 증명을 지정하는 데 권장되는 방식입니다. 이 UPN이 Azure AD에서 올바르게 구성되어 있는지 확인합니다.

    테넌트에서 동일한 UPN 접두사를 사용하는 사용자가 여럿이거나 UPN 접두사가 지나치게 긴 경우 사용자 계정에 대한 *SAMAccountName*(예: *AADDSCONTOSO\driley*)이 자동으로 생성될 수 있습니다. 따라서 사용자 계정에 대한 *SAMAccountName* 형식이 온-프레미스 도메인에서 예상하거나 사용한 것과 다를 수 있습니다.

* **암호 동기화** - [클라우드 전용 사용자][cloud-only-passwords] 또는 [하이브리드 환경에 대해 Azure AD Connect를 사용][hybrid-phs]하여 암호 동기화를 사용하도록 설정했는지 확인합니다.
    * **하이브리드 동기화된 계정:** 영향을 받는 사용자 계정이 온-프레미스 디렉터리에서 동기화되는 경우 다음 영역을 확인합니다.
    
      * [Azure AD Connect의 최신 권장 릴리스](https://www.microsoft.com/download/details.aspx?id=47594)를 배포하거나 업데이트했습니다.
      * [전체 동기화를 수행][hybrid-phs]하도록 Azure AD Connect를 구성했습니다.
      * 디렉터리의 크기에 따라 사용자 계정과 자격 증명 해시가 관리되는 도메인에서 사용 가능해지는 데 시간이 걸릴 수 있습니다. 관리되는 도메인에 인증을 시도하기 전에 충분한 시간 동안 기다려야 합니다.
      * 앞에서 설명한 단계를 확인한 후에도 문제가 지속되면 ‘Microsoft Azure AD Sync 서비스’를 다시 시작해 봅니다. Azure AD Connect 서버에서 명령 프롬프트를 열고 다음 명령을 실행합니다.
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **클라우드 전용 계정**: 영향을 받는 사용자 계정이 클라우드 전용 사용자 계정인 경우 [Azure AD DS를 사용하도록 설정한 후에 사용자가 자신의 암호를 변경][cloud-only-passwords]하도록 합니다. 이 암호 재설정을 수행하면 관리되는 도메인에 필요한 자격 증명 해시가 생성됩니다.

* **사용자 계정이 활성화되어 있는지 확인**: 관리되는 도메인에서 2분 안에 5차례 암호를 잘못 입력하면 사용자 계정이 30분 동안 잠깁니다. 계정이 잠겨 있는 동안에는 사용자가 로그인할 수 없습니다. 30분 후에 사용자 계정이 자동으로 잠금 해제됩니다.
  * 관리되는 도메인에 대해 암호를 잘못 입력해도 Azure AD의 사용자 계정이 잠기지 않습니다. 사용자 계정은 관리되는 도메인 안에서만 잠깁니다. Azure AD가 아니라 ‘ADAC(Active Directory 관리 콘솔)’에서 [관리 VM][management-vm]을 사용하여 사용자 계정 상태를 확인합니다.
  * [세분화된 암호 정책을 구성][password-policy]하여 기본 잠금 임계값과 기간을 변경할 수도 있습니다.

* **외부 계정** - 영향을 받는 사용자 계정이 Azure AD 테넌트에서 외부 계정이 아닌지 확인합니다. 외부 계정의 예로는 Microsoft 계정(예: `dee@live.com`) 또는 외부 Azure AD 디렉터리의 사용자 계정이 있습니다. Azure AD DS는 외부 사용자가 관리되는 도메인에 로그인할 수 없도록 외부 사용자 계정에 대한 자격 증명을 저장하지 않습니다.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>관리되는 도메인에서 하나 이상의 경고 발생

관리되는 도메인에 활성 경고가 있으면 인증 프로세스가 제대로 작동하지 않을 수 있습니다.

활성 경고가 있는지 확인하려면 [관리되는 도메인의 상태를 확인][check-health]합니다. 경고가 표시되면 [경고를 확인하고 문제를 해결][troubleshoot-alerts]합니다.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Azure AD 테넌트에서는 제거되지만 관리되는 도메인에서는 제거되지 않는 사용자

Azure AD에서는 사용자 개체를 실수로 삭제하지 못하도록 보호합니다. Azure AD 테넌트에서 사용자 계정을 삭제하면 해당 사용자 개체가 휴지통으로 이동합니다. 이 삭제 작업이 관리되는 도메인과 동기화되면 해당 사용자 계정이 사용할 수 없는 것으로 표시됩니다. 이 기능을 사용하면 사용자 계정을 복구하거나 삭제를 취소할 수 있습니다.

Azure AD 디렉터리에서 동일한 UPN을 사용하여 사용자 계정을 다시 만든 경우에도 사용자 계정은 관리되는 도메인에서 사용할 수 없는 상태로 유지됩니다. 관리되는 도메인에서 사용자 계정을 제거하려면 Azure AD 테넌트에서 해당 사용자를 강제로 삭제해야 합니다.

관리되는 도메인에서 사용자 계정을 완전히 제거하려면 [Remove-MsolUser][Remove-MsolUser] PowerShell cmdlet과 `-RemoveFromRecycleBin` 매개 변수를 사용하여 Azure AD 테넌트에서 사용자를 영구적으로 삭제합니다.

## <a name="next-steps"></a>다음 단계

문제가 계속 발생하는 경우 추가적인 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다][azure-support].

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
