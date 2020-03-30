---
title: Azure AD 도메인 서비스에서 서비스 주체 경고 해결 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스에 대한 서비스 주체 구성 경고 문제를 해결하는 방법 알아보기
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257955"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory 도메인 서비스의 서비스 주체 경고

[서비스 주체는](../active-directory/develop/app-objects-and-service-principals.md) Azure 플랫폼이 Azure AD DS 관리 도메인을 관리, 업데이트 및 유지 관리하는 데 사용하는 응용 프로그램입니다. 서비스 주체가 삭제되면 Azure AD DS 관리 도메인의 기능이 영향을 받습니다.

이 문서에서는 서비스 주체 관련 구성 경고를 해결하고 해결하는 데 도움이 됩니다.

## <a name="alert-aadds102-service-principal-not-found"></a>경고 AADDS102: 서비스 주체를 찾을 수 없습니다.

### <a name="alert-message"></a>경고 메시지

*Azure AD 도메인 서비스가 제대로 작동하는 데 필요한 서비스 주체가 Azure AD 디렉터리에서 삭제되었습니다. 이 구성은 관리되는 도메인을 모니터링, 관리, 패치 및 동기화하는 Microsoft의 기능에 영향을 미칩니다.*

필요한 서비스 주체가 삭제되면 Azure 플랫폼에서 자동화된 관리 작업을 수행할 수 없습니다. Azure AD DS 관리 도메인이 업데이트를 올바르게 적용하거나 백업을 수행하지 못할 수 있습니다.

### <a name="check-for-missing-service-principals"></a>누락된 서비스 주체에 대한 확인

어떤 서비스 주체가 누락되어 있고 다시 만들어야 하는지 확인하려면 다음 단계를 완료하십시오.

1. Azure 포털에서 왼쪽 탐색 메뉴에서 **Azure Active Directory를** 선택합니다.
1. **Enterprise 애플리케이션**을 선택합니다. **응용 프로그램 유형** 드롭다운 메뉴에서 모든 응용 *프로그램을* 선택한 다음 **적용을**선택합니다.
1. 각 응용 프로그램 아이디를 검색합니다. 기존 응용 프로그램이 없는 경우 *해결* 단계에 따라 서비스 주체를 만들거나 네임스페이스를 다시 등록합니다.

    | 애플리케이션 UI | 해결 방법 |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [누락된 서비스 주체 다시 만들기](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Microsoft.AAD 네임스페이스 다시 등록](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Microsoft.AAD 네임스페이스 다시 등록](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Microsoft.AAD 네임스페이스 다시 등록](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>누락된 서비스 주체 다시 만들기

응용 프로그램 ID *2565bd9d-da50-47d4-8b85-4c97f6699dc36가* Azure AD 디렉터리에서 누락된 경우 Azure AD PowerShell을 사용하여 다음 단계를 완료합니다. 자세한 내용은 [Azure AD PowerShell 설치를](/powershell/azure/active-directory/install-adv2)참조하십시오.

1. Azure AD PowerShell 모듈을 설치하고 다음과 같이 가져옵니다.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. 이제 [새 AzureAdServicePrincipal][New-AzureAdServicePrincipal] cmdlet을 사용하여 서비스 주체를 다시 만듭니다.

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

### <a name="re-register-the-microsoft-aad-namespace"></a>Microsoft AAD 네임스페이스 다시 등록

신청 ID *443155a6-77f3-45e3-882b-22b3a8d431fb,* *abba844e-bc0e-44b0-947 a-dc74e5d09022,* 또는 *d87dcbc6-a371-462e-88e3-28ad15ec4e64* Azure AD 디렉토리에서 누락된 경우, 다음 단계를 완료하여 *Microsoft.AAD* 리소스 공급자를 다시 등록합니다.

1. Azure 포털에서 구독을 검색하고 **선택합니다.**
1. Azure AD DS 관리 도메인과 연결된 구독을 선택합니다.
1. 왼쪽 탐색에서 **리소스 공급자를**선택합니다.
1. *Microsoft.AAD를*검색한 다음 **다시 등록을 선택합니다.**

Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>경고 AADDS105: 암호 동기화 애플리케이션이 만료됨

### <a name="alert-message"></a>경고 메시지

*응용 프로그램 ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64"가 있는 서비스 주체가 삭제된 후 다시 만들어졌습니다. 레크리에이션은 관리되는 도메인을 서비스하는 데 필요한 Azure AD 도메인 서비스 리소스에 대한 일관되지 않은 권한을 남깁니다. 관리되는 도메인의 암호 동기화에 영향을 줄 수 있습니다.*

Azure AD DS는 Azure AD에서 사용자 계정 및 자격 증명을 자동으로 동기화합니다. 이 프로세스에 사용되는 Azure AD 응용 프로그램에 문제가 있는 경우 Azure AD DS와 Azure AD 간의 자격 증명 동기화가 실패합니다.

### <a name="resolution"></a>해결 방법

자격 증명 동기화에 사용되는 Azure AD 응용 프로그램을 다시 만들려면 Azure AD PowerShell을 사용하여 다음 단계를 완료합니다. 자세한 내용은 [Azure AD PowerShell 설치를](/powershell/azure/active-directory/install-adv2)참조하십시오.

1. Azure AD PowerShell 모듈을 설치하고 다음과 같이 가져옵니다.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. 이제 다음 PowerShell cmdlet을 사용하여 이전 응용 프로그램 및 개체를 삭제합니다.

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

두 응용 프로그램을 모두 삭제하면 Azure 플랫폼이 자동으로 응용 프로그램을 다시 만들고 암호 동기화를 다시 시작하려고 시도합니다. Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

## <a name="next-steps"></a>다음 단계

그래도 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
