---
title: 'Azure Active Directory Domain Services: 문제 해결 가이드 | Microsoft Docs'
description: Azure AD 도메인 서비스에 대한 문제 해결 가이드
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 9e20bdee11b9f07b262d284936bf0c2d95de586b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246646"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services - 문제 해결 가이드
이 문서는 Azure AD(Active Directory) 도메인 서비스를 설치하거나 관리할 때 발생할 수 있는 문제에 대한 문제 해결 힌트를 제공합니다.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Azure AD 디렉터리에 대해 Azure AD Domain Services를 사용할 수 없습니다.
이 섹션에서는 디렉터리에 Azure AD Domain Services를 사용하도록 설정하려고 할 때 발생하는 오류를 해결하는 데 도움이 됩니다.

발생하는 오류 메시지에 해당하는 문제 해결 단계를 선택합니다.

| **오류 메시지** | **해결 방법** |
| --- |:--- |
| *contoso100.com 이름은 이 네트워크에서 이미 사용 중입니다. 사용하지 않는 이름을 지정하십시오.* |[가상 네트워크에서 도메인 이름 충돌](troubleshoot.md#domain-name-conflict) |
| *이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. 이 서비스에는 'Azure AD Domain Services Sync'라는 애플리케이션에 대한 적절한 권한이 없습니다. 'Azure AD Domain Services Sync'라는 애플리케이션을 삭제한 다음 Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정하십시오.* |[Domain Services에 Azure AD Domain Services Sync 애플리케이션에 대한 적절한 권한이 없음](troubleshoot.md#inadequate-permissions) |
| *이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. Azure AD 테넌트의 Domain Services 애플리케이션에는 Domain Services를 사용하는 데 필요한 권한이 없습니다. d87dcbc6-a371-462e-88e3-28ad15ec4e64 애플리케이션 식별자를 사용하여 애플리케이션을 삭제한 다음 Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정하십시오.* |[테넌트에서 Domain Services 애플리케이션을 제대로 구성하지 않음](troubleshoot.md#invalid-configuration) |
| *이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. Azure AD 테넌트에서 Microsoft Azure AD 애플리케이션을 사용할 수 없습니다. 00000002-0000-0000-c000-000000000000 애플리케이션 식별자를 사용하여 애플리케이션을 사용하도록 설정한 다음 Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정하십시오.* |[Azure AD 테넌트에서 Microsoft Graph 애플리케이션을 사용할 수 없음](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>도메인 이름 충돌
**오류 메시지:**

*contoso100.com 이름은 이 네트워크에서 이미 사용 중입니다. 사용하지 않는 이름을 지정하십시오.*

**재구성:**

해당 가상 네트워크에서 동일한 도메인 이름을 사용하는 기존 도메인이 없는지 확인합니다. 예를 들어, 선택한 가상 네트워크에서 'contoso.com'이라는 도메인을 이미 사용한다고 가정합니다. 나중에 해당 가상 네트워크에서 동일한 도메인 이름(즉, 'contoso.com')을 가진 Azure AD Domain Services 관리되는 도메인을 사용하도록 설정하려고 합니다. Azure AD 도메인 서비스를 사용하도록 설정하면 오류가 발생합니다.

이 오류는 해당 가상 네트워크에서 도메인 이름이 충돌하기 때문입니다. 이 경우 다른 이름을 사용하여 Azure AD 도메인 서비스 관리되는 도메인을 설정해야 합니다. 또는 기존 도메인을 프로비전 해제한 후 Azure AD 도메인 서비스를 사용하도록 설정할 수 있습니다.

### <a name="inadequate-permissions"></a>부적절한 권한
**오류 메시지:**

*이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. 이 서비스에는 'Azure AD Domain Services Sync'라는 애플리케이션에 대한 적절한 권한이 없습니다. 'Azure AD Domain Services Sync'라는 애플리케이션을 삭제한 다음 Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정하십시오.*

**재구성:**

Azure AD 디렉터리에 'Azure AD Domain Services Sync'라는 이름의 애플리케이션이 있는지 확인합니다. 이 애플리케이션이 있으면 삭제한 다음, Azure AD Domain Services를 사용하도록 다시 설정해야 합니다.

다음 단계를 수행하여 애플리케이션이 있는지 확인하고 있는 경우 삭제합니다.

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)의 Azure AD 디렉터리에 있는 **애플리케이션** 섹션으로 이동합니다.
2. **표시** 드롭다운에서 **모든 애플리케이션**을 선택합니다. **애플리케이션 상태** 드롭다운에서 **모두**를 선택합니다. **애플리케이션 표시** 드롭다운에서 **모두**를 선택합니다.
3. 검색 상자에 **Azure AD Domain Services 동기화**를 입력합니다. 애플리케이션이 있는 경우 클릭한 다음, 도구 모음에서 **삭제** 단추를 클릭하여 삭제합니다.
4. 애플리케이션을 삭제한 후에는 Azure AD 도메인 서비스를 다시 사용하도록 설정합니다.

### <a name="invalid-configuration"></a>유효하지 않은 구성
**오류 메시지:**

*이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. Azure AD 테넌트의 Domain Services 애플리케이션에는 Domain Services를 사용하는 데 필요한 권한이 없습니다. d87dcbc6-a371-462e-88e3-28ad15ec4e64 애플리케이션 식별자를 사용하여 애플리케이션을 삭제한 다음 Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정하십시오.*

**재구성:**

Azure AD 디렉터리에 'AzureActiveDirectoryDomainControllerServices'(애플리케이션 식별자가 d87dcbc6-a371-462e-88e3-28ad15ec4e64임)라는 이름의 애플리케이션이 있는지 확인합니다. 이 애플리케이션이 있는 경우 삭제한 다음, Azure AD Domain Services를 다시 설정해야 합니다.

다음 PowerShell 스크립트를 사용하여 애플리케이션을 찾아 삭제합니다.

> [!NOTE]
> 이 스크립트는 **Azure AD PowerShell 버전 2** cmdlet을 사용합니다. 사용 가능한 모든 cmdlet의 전체 목록을 보고 모듈을 다운로드하려면 [AzureAD PowerShell 참조 설명서](https://msdn.microsoft.com/library/azure/mt757189.aspx)를 참조하세요.
>
>

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
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph 사용 안 함
**오류 메시지:**

이 Azure AD 테넌트에서는 Domain Services를 사용할 수 없습니다. Azure AD 테넌트에서 Microsoft Azure AD 애플리케이션을 사용할 수 없습니다. 00000002-0000-0000-c000-000000000000 애플리케이션 식별자를 사용하여 애플리케이션을 사용하도록 설정한 다음, Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정합니다.

**재구성:**

00000002-0000-0000-c000-000000000000 식별자를 사용하는 애플리케이션을 사용하지 않도록 설정했는지 확인합니다. 이 애플리케이션은 Microsoft Azure AD 애플리케이션이며 Azure AD 테넌트에 Graph API 액세스를 제공합니다. Azure AD Domain Services는 이 애플리케이션을 사용하여 Azure AD 테넌트와 관리되는 도메인을 동기화 할 수 있어야 합니다.

이 오류를 해결하려면 이 애플리케이션을 사용하도록 설정한 다음, Azure AD 테넌트에 대해 Domain Services를 사용하도록 설정합니다.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>사용자는 Azure AD 도메인 서비스 관리된 도메인에 로그인할 수 없습니다.
Azure AD 테넌트에서 하나 이상의 사용자가 새로 만든 관리되는 도메인에 로그인할 수 없는 경우 다음 문제 해결 단계를 수행합니다.

* **UPN 형식을 사용하여 로그인:** SAMAccountName 형식('CONTOSO\joeuser') 대신 UPN 형식(예: 'joeuser@contoso.com')을 사용하여 로그인하려고 합니다. UPN 접두사가 너무 길거나 관리되는 도메인의 다른 사용자와 동일한 사용자에 대해 SAMAccountName이 자동으로 생성될 수 있습니다. UPN 형식은 Azure AD 테넌트 내에서 고유하도록 보장됩니다.

> [!NOTE]
> Azure AD Domain Services 관리되는 도메인에 로그인하는 데 UPN 형식을 사용하는 것이 좋습니다.
>
>

* 시작 가이드에 설명된 단계에 따라 [암호 동기화를 사용하도록 설정](active-directory-ds-getting-started-password-sync.md) 했는지 확인합니다.
* **외부 계정:** 영향을 받는 사용자 계정이 Azure AD 테넌트에서 외부 계정이 아닌지 확인합니다. 외부 계정의 예는 Microsoft 계정(예: 'joe@live.com') 또는 외부 Azure AD 디렉터리에서 사용자 계정을 포함합니다. Azure AD 도메인 서비스에는 이러한 사용자 계정에 대한 자격 증명이 없으므로 이러한 사용자는 관리된 도메인에 로그인할 수 없습니다.
* **동기화된 계정:** 영향을 받는 사용자 계정이 온-프레미스 디렉터리에서 동기화되는 경우 다음을 확인합니다.

  * [Azure AD Connect의 최신 권장 사항](https://www.microsoft.com/download/details.aspx?id=47594)으로 배포하거나 업데이트했습니다.
  * [전체 동기화를 수행](active-directory-ds-getting-started-password-sync.md)하도록 Azure AD Connect를 구성했습니다.
  * 디렉터리의 크기에 따라 사용자 계정 및 해시 자격 증명이 Azure AD 도메인 서비스에서 사용할 수 있도록 하는 데 시간이 걸릴 수 있습니다. 충분히 오래 기다렸다가 인증을 다시 시도해야 합니다.
  * 앞의 단계를 확인한 후에도 문제가 지속되면 Microsoft Azure AD Sync 서비스를 다시 시작해 봅니다. 동기화 컴퓨터에서 명령 프롬프트를 시작하고 다음 명령을 실행합니다.

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **클라우드 전용 계정**: 영향을 받는 사용자 계정이 클라우드 전용 사용자 계정인 경우 Azure AD Domain Services를 사용하도록 설정한 후에 사용자가 암호를 변경했는지 확인합니다. 이 단계를 수행하면 Azure AD 도메인 서비스가 생성되는 데 필요한 자격 증명 해시가 발생합니다.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>관리되는 도메인에서 하나 이상의 경고 발생

[경고 문제 해결](troubleshoot-alerts.md) 문서를 방문하여 관리되는 도메인의 경고를 해결하는 방법을 참조하세요.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Azure AD 테넌트에서는 제거되지만 관리되는 도메인에서는 제거되지 않는 사용자
Azure AD에서는 사용자 개체를 실수로 삭제하지 못하도록 보호합니다. Azure AD 테넌트에서 사용자 계정을 삭제하면 해당 사용자 개체가 휴지통으로 이동합니다. 이 삭제 작업이 관리되는 도메인과 동기화하면 해당 사용자 계정을 사용할 수 없는 것으로 표시됩니다. 이 기능을 사용하면 나중에 사용자 계정을 복구하거나 삭제를 취소할 수 있습니다.

Azure AD 디렉터리에서 동일한 UPN을 사용하여 사용자 계정을 다시 만든 경우에도 사용자 계정은 관리되는 도메인에서 사용할 수 없는 상태로 유지됩니다. 관리되는 도메인에서 사용자 계정을 제거하려면 Azure AD 테넌트에서 해당 사용자를 강제로 삭제해야 합니다.

관리되는 도메인에서 사용자 계정을 완전히 제거하려면 Azure AD 테넌트에서 사용자를 영구적으로 삭제합니다. 이 [MSDN 문서](/previous-versions/azure/dn194132(v=azure.100))에 설명된 대로 `Remove-MsolUser` PowerShell cmdlet을 `-RemoveFromRecycleBin` 옵션과 함께 사용합니다.


## <a name="contact-us"></a>문의처
[지원이 필요하거나 피드백을 공유하려면](contact-us.md)Azure Active Directory Domain Services 제품 팀에 문의하세요.
