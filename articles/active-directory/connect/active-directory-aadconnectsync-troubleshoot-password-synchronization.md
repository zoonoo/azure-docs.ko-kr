---
title: "Azure AD Connect 동기화로 암호 동기화 문제 해결 | Microsoft Docs"
description: "암호 동기화 문제를 해결하는 방법에 대한 정보를 제공합니다."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 동기화를 사용하여 암호 동기화 문제 해결
이 항목에서는 암호 동기화 문제를 해결하는 방법에 대한 단계를 제공합니다. 암호가 예상대로 동기화되지 않으면 사용자의 하위 집합 또는 모든 사용자 때문일 수 있습니다.

* 암호가 동기화되지 않는 문제가 있으면 [암호가 동기화되지 않은 위치의 문제 해결](#no-passwords-are-synchronized)을 참조하세요.
* 개별 개체에 문제가 있으면 [암호를 동기화하지 않은 한 개체의 문제 해결](#one-object-is-not-synchronizing-passwords)을 참조하세요.

## <a name="no-passwords-are-synchronized"></a>암호가 동기화되지 않음
암호가 동기화되지 않은 이유를 파악하려면 다음 단계를 수행합니다.

1. 연결 서버가 [스테이징 모드](active-directory-aadconnectsync-operations.md#staging-mode)인가요? 스테이징 모드의 서버는 암호를 동기화하지 않습니다.
2. [암호 동기화 설정의 상태 가져오기](#get-the-status-of-password-sync-settings) 섹션에서 스크립트를 실행합니다 암호 동기화 구성의 개요를 제공합니다.  
![암호 동기화 설정에서 PowerShell 스크립트 출력](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. Azure AD에서 이 기능을 사용하지 않거나 동기화 채널 상태를 사용하지 않는 경우 연결 설치 마법사를 실행합니다. **동기화 사용자 지정 옵션**을 선택하고 암호 동기화의 선택을 취소합니다. 이 변경 내용은 기능을 일시적으로 비활성화합니다. 그런 다음 마법사를 다시 실행하고 암호 동기화를 다시 사용합니다. 스크립트를 다시 실행하여 구성이 올바른지 확인합니다.
4. eventLog에서 오류를 찾습니다. 문제를 나타내는 다음 이벤트를 찾습니다.
    1. 소스: "디렉터리 동기화" ID: 0, 611, 652, 655 이러한 메시지가 표시되면 연결에 문제가 있는 것입니다. 이벤트 로그 메시지에는 문제가 있는 위치에 대한 포리스트 정보가 포함됩니다. 자세한 내용은 [연결 문제](#connectivity problem)를 참조하세요.
5. 하트비트가 표시되지 않거나 아무 작동도 진행되지 않으면 [모든 암호의 전체 동기화 트리거](#trigger-a-full-sync-of-all-passwords)를 실행합니다. 이 스크립트는 한 번만 실행해야 합니다.
6. [암호를 동기화하지 않은 한 개체의 문제 해결](#one-object-is-not-synchronizing-passwords) 섹션을 읽어보세요.

### <a name="connectivity-problem"></a>연결 문제

1. Azure AD와 연결되어 있나요?
2. 계정에 모든 도메인에서 암호 해시를 읽는 데 필요한 권한이 있나요? 기본 설정을 사용하여 연결을 설치한 경우 사용 권한은 이미 올바른 상태입니다. 사용자 지정 설치를 사용한 경우에는 사용 권한을 수동으로 설정해야 합니다.
    1. Active Directory Connector에서 사용되는 계정을 찾으려면 **Synchronization Service Manager**를 시작합니다. **커넥터**로 이동한 후 문제를 해결하려는 온-프레미스 Active Directory 포리스트를 찾습니다. 커넥터를 선택하고 **속성**을 클릭합니다. **Active Directory 포리스트에 연결**로 이동합니다.  
    ![AD Connector에서 사용되는 계정](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    계정이 있는 사용자 이름 및 도메인을 적어둡니다.
    2. **Active Directory 사용자 및 컴퓨터**를 시작합니다. 이전 단계에서 찾은 계정이 포리스트에 있는 모든 도메인의 루트에서 설정된 다음 사용 권한을 갖는지 확인합니다.
        * 디렉터리 변경 내용 복제
        * 모든 디렉터리 변경 내용 복제
3. Azure AD Connect에서 도메인 컨트롤러에 연결할 수 있나요? 연결 서버에서 모든 도메인 컨트롤러에 연결할 수는 없으면 **기본 설정 도메인 컨트롤러만 사용**을 구성합니다.  
    ![AD Connect에서 사용되는 도메인 컨트롤러](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    **Synchronization Service Manager** 및 **디렉터리 파티션 구성**으로 돌아갑니다. **디렉터리 파티션 선택**에서 도메인을 선택하고 **기본 설정 도메인 컨트롤러만 사용** 확인란을 선택한 후 **구성**을 클릭합니다. 목록에서 연결이 암호 동기화에 사용해야 하는 도메인 컨트롤러를 입력합니다. 동일한 목록이 가져오기 및 내보내기에도 사용됩니다. 모든 도메인에 대해 이 단계를 수행합니다.
4. 스크립트에서 하트비트가 없다는 것을 보여 주는 경우 [모든 암호의 전체 동기화 트리거](#trigger-a-full-sync-of-all-passwords)에서 스크립트를 실행합니다.

## <a name="one-object-is-not-synchronizing-passwords"></a>한 개체가 암호를 동기화하지 않음
개체의 상태를 검토하여 암호 동기화 문제를 쉽게 해결할 수 있습니다.

1. **Active Directory 사용자 및 컴퓨터**에서 시작합니다. 사용자를 찾고 **사용자가 다음 로그온 시 암호를 변경 해야 합니다** 를 선택했는지 확인합니다.  
![Active Directory 생산성 높은 암호](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
선택한 경우 사용자에게 요청하여 암호로 로그인하고 암호를 변경합니다. 임시 암호는 Azure AD에 동기화되지 않습니다.
2. Active Directory에서 올바르게 보인다면 다음 단계에서는 동기화 엔진에서 사용자를 따릅니다. 온-프레미스 Active Directory에서 Azure AD까지 사용자를 따라 개체에 설명이 포함된 오류가 있는지 확인할 수 있습니다.
    1. **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**를 시작합니다.
    2. **커넥터**를 클릭합니다.
    3. 사용자가 있는 **Active Directory Connector** 를 선택합니다.
    4. **커넥터 공간 검색**을 선택합니다.
    5. **범위**에서 **DN 또는 앵커**를 선택합니다. 문제를 해결하려는 사용자의 전체 DN을 입력합니다.
    ![DN을 사용하여 cs에서 사용자 검색](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. 찾고 있는 사용자를 찾은 후 **속성**을 클릭하여 모든 특성을 확인합니다. 해당 사용자가 검색 결과에 없으면 [필터링 규칙](active-directory-aadconnectsync-configure-filtering.md)을 확인하고 [변경 내용 적용 및 확인](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes)을 실행하여 사용자가 연결에 나타나도록 합니다.
    7. 지난 주에 대한 개체의 암호 동기화 세부 정보를 보려면 **로그...**를 클릭합니다.  
    ![개체 로그 세부 정보](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    개체 로그가 비어 있으면 Active Directory에서 Azure AD Connect를 암호 해시에서 읽을 수 없습니다. [연결 오류](#connectivity-errors) 문제를 계속 해결합니다. **success** 이외의 값이 표시되면 [암호 동기화 로그](#password-sync-log)의 테이블을 참조하세요.
    8. **계보** 탭을 선택하여 최소한 하나 이상의 동기화 규칙에서 **암호 동기화**가 **True**로 표시되는지 확인합니다. 기본 구성에서 동기화 규칙의 이름은 **AD에서 들어오기 - 사용자 AccountEnabled**입니다.  
    ![사용자에 대한 계보 정보](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. **메타버스 개체 속성**을 클릭합니다. 해당 사용자에 특성 목록이 표시됩니다.  
    ![메타버스 정보](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    **cloudFiltered** 특성이 없는지 확인합니다. 도메인 특성(domainFQDN 및 domainNetBios)이 예상 값을 갖는지 확인합니다.
    10. **커넥터** 탭을 클릭합니다. 온-프레미스 AD 및 Azure AD 둘 다에 대한 커넥터가 표시되는지 확인합니다.
    ![메타버스 정보](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. Azure AD를 나타내는 행을 선택하고 **속성**을 클릭합니다. **계보** 탭을 클릭합니다. 커넥터 공간 개체에 **Password Sync** 아웃바운드 규칙이 **True**로 설정되어 있어야 합니다. 기본 구성에서 동기화 규칙의 이름은 **AAD로 나가기 - 사용자 조인**입니다.  
    ![사용자의 커넥터 공간 속성](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>암호 동기화 로그
상태 열에는 다음과 같은 값을 포함할 수 있습니다.

| 가동 상태 | 설명 |
| --- | --- |
| 성공 |암호가 성공적으로 동기화되었습니다. |
| FilteredByTarget |**다음 로그인할 때 반드시 암호 변경**으로 암호가 설정됩니다. 암호가 동기화되지 않았습니다. |
| NoTargetConnection |메타버스에 또는 Azure AD 커넥터 공간에 개체가 없습니다. |
| SourceConnectorNotPresent |개체를 온-프레미스 Active Directory Connector 공간에서 찾을 수 없습니다. |
| TargetNotExportedToDirectory |Azure AD 커넥터 공간에 있는 개체가 아직 내보내지지 않았습니다. |
| MigratedCheckDetailsForMoreInfo |로그 항목 1.0.9125.0 빌드 전에 만들어졌으며 레거시 상태로 표시됩니다. |

## <a name="scripts-to-help-troubleshooting"></a>문제 해결에 도움이 되는 스크립트

### <a name="get-the-status-of-password-sync-settings"></a>암호 동기화 설정의 상태 가져오기
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>모든 암호의 전체 동기화 트리거
> [!NOTE]
> 이 스크립트는 한 번만 실행해야 합니다. 이 스크립트를 두 번 이상 실행해야 한다면 문제가 있는 것입니다. 문제를 해결하려면 Microsoft 지원 서비스에 문의하세요.

다음 스크립트를 사용하여 모든 암호의 전체 동기화를 트리거할 수 있습니다.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect 동기화로 암호 동기화 구현](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

