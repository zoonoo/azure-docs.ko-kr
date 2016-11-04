---
title: Azure AD Connect 동기화로 암호 동기화 구현 | Microsoft Docs
description: 암호 동기화 작동 방식 및 사용하도록 설정하는 방법에 대한 정보를 제공합니다.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: markusvi;andkjell

---
# Azure AD Connect 동기화로 암호 동기화 구현
이 항목에서는 온-프레미스 Active Directory에서 클라우드 기반 Azure Active Directory(Azure AD)로 사용자 암호를 동기화하는 데 필요한 정보를 제공합니다.

## 암호 동기화란 무엇입니까?
암호를 잊어버려서 작업을 수행하지 못할 확률은 기억해야 하는 암호의 수와 관련됩니다. 기억할 암호가 많을 수록 잊을 확률이 높습니다. 암호 다시 설정 및 기타 암호 관련 문제에 대한 질문 및 호출은 많은 helpdesk 리소스가 필요합니다.

암호 동기화는 온-프레미스 Active Directory에서 클라우드 기반 Azure Active Directory(Azure AD)로 사용자 암호를 동기화하는 기능입니다. 이 기능을 사용하면 온-프레미스 Active Directory에 로그인하는 데 사용하는 것과 동일한 암호를 사용하여 Azure Active Directory 서비스(Office 365, Microsoft Intune, CRM Online, Azure AD 도메인 서비스)에 로그인할 수 있습니다.

![Azure AD Connect의 정의](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

암호의 수를 줄여서 사용자는 하나의 암호를 유지해야 합니다. 암호 동기화를 사용하면 다음을 수행할 수 있습니다.

* 사용자의 생산성 향상
* 지원 센터 비용 절감

또한 [**AD FS로 페더레이션**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)을 사용하도록 선택하는 경우 필요에 따라 AD FS 인프라가 실패할 경우 백업으로 암호 동기화 기능을 사용할 수 있습니다.

암호 동기화는 Azure AD Connect 동기화를 통한 디렉터리 동기화 기능 구현의 확장판입니다. 사용자 환경에서 암호 동기화를 사용하려면 다음이 필요합니다.

* Azure AD Connect 설치
* 온-프레미스 AD 및 Azure Active Directory 간에 디렉터리 동기화 구성
* 암호 동기화 사용

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.

> [!NOTE]
> FIPS 및 암호 동기화에 대해 Active Directory 도메인 서비스 구성에 대한 자세한 내용은 [암호 동기화 및 FIPS](#password-synchronization-and-fips)를 참조하세요.
> 
> 

## 암호 동기화 작동 방법
Active Directory 도메인 서비스는 실제 사용자 암호의 해시 값 표시 형태로 암호를 저장합니다. 해시 값은 수학 함수("*해시 알고리즘*")의 결과입니다. 암호의 일반 텍스트 버전에 대한 함수의 결과를 되돌릴 수 있는 방법이 없습니다. 암호 해시를 사용하여 온-프레미스 네트워크에 로그인할 수 없습니다.

암호를 동기화 하기 위해, Azure AD Connect 동기화는 온-프레미스 Active Directory에서 사용자 암호 해시를 추출합니다. Azure Active Directory 인증 서비스로 동기화 되기 전에 암호 해시에 추가적인 보안 처리가 적용됩니다. 암호는 각 사용자 기본별로 동기화되고 시간 순서대로 동기화됩니다.

암호 동기화 과정의 실제 흐름은 DisplayName 또는 전자 메일 주소와 같은 사용자 데이터 동기화와 비슷합니다. 그러나 암호는 다른 특성에 대한 표준 디렉터리 동기화 창보다 더 자주 동기화됩니다. 암호 동기화 프로세스는 2분 마다 실행됩니다. 이 프로세스의 빈도를 수정할 수 없습니다. 암호를 동기화할 경우 기존 클라우드 암호를 덮어씁니다.

암호 동기화 기능을 사용하도록 처음으로 설정하면 범위 내 모든 사용자 암호에 대한 초기 동기화를 수행합니다. 동기화할 사용자 암호의 하위 집합을 명시적으로 정의할 수 없습니다.

온-프레미스 암호를 변경하면, 업데이트된 암호는 대개 몇 분 내에 동기화됩니다. 암호 동기화 기능은 실패한 사용자 암호 동기화를 자동으로 다시 시도합니다. 암호를 동기화하는 동안 오류가 발생하면 이벤트 뷰어에 오류가 기록됩니다.

암호 동기화는 현재 로그온한 사용자에게 아무런 영향도 미치지 않습니다. 클라우드 서비스에 로그온해 있는 동안, 동기화된 암호가 변경되더라도 현재 클라우드 세션이 즉시 영향을 받지는 않습니다. 하지만, 클라우드 서비스에서 다시 인증을 요구하면 새 암호를 제공해야 합니다.

> [!NOTE]
> 암호 동기화는 Active Directory의 개체 형식 사용자에만 지원됩니다. iNetOrgPerson 개체 형식에 대해 지원되지 않습니다.
> 
> 

### Azure AD 도메인 서비스와 함께 암호를 동기화하는 방법
암호 동기화 기능을 사용하여 온-프레미스 암호를 [Azure AD 도메인 서비스](../active-directory-domain-services/active-directory-ds-overview.md)에 동기화 하는 할 수도 있습니다. 이 시나리오를 사용하면 Azure AD 도메인 서비스가 온-프레미스 AD에서 사용할 수 있는 모든 방법으로 클라우드의 사용자를 인증할 수 있습니다. 이 시나리오의 환경은 온-프레미스 환경에서 ADMT(Active Directory 마이그레이션 도구)를 사용하는 것과 비슷합니다.

### 보안 고려 사항
암호를 동기화 할 때, 사용자 암호의 일반 텍스트 버전은 암호 동기화 기능, Azure AD 혹은 다른 어떤 관련 서비스에 노출되지 않습니다.

또한 역암호화 형식으로 암호를 저장하는 온-프레미스 Active Directory에는 요구사항이 없습니다. Active Directory 암호 해시의 다이제스트는 온-프레미스 AD와 Azure Active Directory간의 전송에 사용됩니다. 암호 해시의 다이제스트는 고객의 온-프레미스 환경의 리소스에 액세스할 때 사용할 수 없습니다.

### 암호 정책 고려 사항
암호 동기화를 사용하여 영향을 받는 두 가지 정책이 있습니다.

1. 암호 복잡성 정책
2. 암호 만료 정책

**암호 복잡성 정책** 암호 동기화를 사용하는 경우 온-프레미스 Active Directory의 암호 복잡성 정책이 동기화된 사용자에 대한 클라우드의 복잡성 정책을 재정의합니다. 온-프레미스 Active Directory의 유효한 모든 암호를 사용하여 Azure AD 서비스에 액세스할 수 있습니다.

> [!NOTE]
> 클라우드에서 직접 만든 사용자의 암호는 클라우드 내에서 정의된 암호 정책을 계속 따릅니다.
> 
> 

**암호 만료 정책** 사용자가 암호 동기화 범위 내에 있을 경우 클라우드 계정 암호는 "*사용 기간 제한 없음*"으로 설정됩니다. 온-프레미스 환경에서 만료된 동기화된 암호를 사용하여 클라우드 서비스에 계속 로그인할 수 있습니다. 클라우드 암호는 온-프레미스 환경에서 다음에 암호를 변경할 때 업데이트됩니다.

### 동기화된 암호 덮어쓰기
관리자는 Windows PowerShell을 사용하여 사용자의 암호를 수동으로 재설정할 수 있습니다.

이런 경우, 새 암호는 사용자의 동기화된 암호를 재정의하고 클라우드 내에 정의된 모든 암호 정책이 새 암호에 적용됩니다.

사용자가 온-프레미스 암호를 다시 변경하면, 새 암호는 클라우드에 동기화되며, 수동으로 업데이트한 암호를 재정의합니다.

## 암호 동기화를 사용하도록 설정
**Express 설정**을 사용하여 Azure AD Connect를 설치할 경우 암호 동기화를 사용하도록 자동으로 설정됩니다. 자세한 내용은 [기본 설정을 사용하여 Azure AD Connect 시작](active-directory-aadconnect-get-started-express.md)을 참조하세요.

Azure AD Connect를 설치할 때 사용자 지정 설정을 사용하는 경우 사용자 로그인 페이지에서 암호 동기화를 설정할 수 있습니다. 자세한 내용은 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요.

![암호 동기화를 사용하도록 설정](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### 암호 동기화 및 FIPS
서버가 FIPS(Federal Information Processing Standard)에 따라 잠긴 다음 MD5가 비활성화됩니다.

**암호 동기화에 MD5를 사용하려면 다음 단계를 수행합니다.**

1. **%programfiles%\\Azure AD Sync\\Bin**으로 이동합니다.
2. **miiserver.exe.config**를 엽니다.
3. 파일의 끝에서 **구성/런타임 노드**로 이동합니다.
4. 다음 노드를 추가 합니다. `<enforceFIPSPolicy enabled="false"/>`
5. 변경 내용을 저장합니다.

참조하기 위해 이 캡처와 같아야 합니다.

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

보안 및 FIPS에 대한 자세한 내용은 [AAD 암호 동기화, 암호화 및 FIPS 준수](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)를 참조하십시오.

## 암호 동기화 문제 해결
암호가 예상 대로 동기화되지 않으면 사용자의 하위 집합 또는 모든 사용자 때문일 수 있습니다.

* 개별 개체에 문제가 있으면 [암호를 동기화하지 않은 한 개체의 문제 해결](#troubleshoot-one-object-that-is-not-synchronizing-passwords)을 참조하세요.
* 암호가 동기화되지 않는 문제가 있으면 [암호가 동기화화되지 않은 위치의 문제 해결](#troubleshoot-issues-where-no-passwords-are-synchronized)을 참조하세요.

### 암호를 동기화하지 않은 한 개체의 문제 해결
개체의 상태를 검토하여 암호 동기화 문제를 쉽게 해결할 수 있습니다.

**Active Directory 사용자 및 컴퓨터**에서 시작합니다. 사용자를 찾고 **사용자가 다음 로그온 시 암호를 변경 해야 합니다**를 선택했는지 확인합니다. ![Active Directory 생산성 높은 암호](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png) 선택한 경우 사용자에게 요청하여 암호로 로그인하고 암호를 변경합니다. 임시 암호는 Azure AD에 동기화되지 않습니다.

Active Directory에서 올바르게 보인다면 다음 단계에서는 동기화 엔진에서 사용자를 따릅니다. 온-프레미스 Active Directory에서 Azure AD까지 사용자를 따라 개체에 설명이 포함된 오류가 있는지 확인할 수 있습니다.

1. **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**를 시작합니다.
2. **커넥터**를 클릭합니다.
3. 사용자가 있는 **Active Directory Connector**를 선택합니다.
4. **커넥터 공간 검색**을 선택합니다.
5. 찾고자 하는 사용자를 찾습니다.
6. **계보** 탭을 선택하여 최소한 하나 이상의 동기화 규칙에서 **암호 동기화**가 **True**로 표시되는지 확인합니다. 기본 구성에서 동기화 규칙의 이름은 **AD에서 들어오기 - 사용자 AccountEnabled**입니다. ![사용자에 대한 계보 정보](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)
7. 그런 다음 메타버스를 통해 Azure AD 커넥터 공간으로 [사용자를 따릅니다](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system). 커넥터 공간 개체에 **Password Sync** 아웃바운드 규칙이 **True**로 설정되어 있어야 합니다. 기본 구성에서 동기화 규칙의 이름은 **AAD로 나가기 - 사용자 조인**입니다. ![사용자의 커넥터 공간 속성](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)
8. 지난 주에 대한 개체의 암호 동기화 세부 정보를 보려면 **로그...**를 클릭합니다. ![개체 로그 세부 정보](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

상태 열에는 다음과 같은 값을 포함할 수 있습니다.

| 가동 상태 | 설명 |
| --- | --- |
| 성공 |암호가 성공적으로 동기화되었습니다. |
| FilteredByTarget |**다음 로그인할 때 반드시 암호 변경**으로 암호가 설정됩니다. 암호가 동기화되지 않았습니다. |
| NoTargetConnection |메타버스에 또는 Azure AD 커넥터 공간에 개체가 없습니다. |
| SourceConnectorNotPresent |개체를 온-프레미스 Active Directory Connector 공간에서 찾을 수 없습니다. |
| TargetNotExportedToDirectory |Azure AD 커넥터 공간에 있는 개체가 아직 내보내지지 않았습니다. |
| MigratedCheckDetailsForMoreInfo |로그 항목 1.0.9125.0 빌드 전에 만들어졌으며 레거시 상태로 표시됩니다. |

### 암호가 동기화화되지 않은 위치의 문제 해결
[암호 동기화 설정의 상태 가져오기](#get-the-status-of-password-sync-settings) 섹션에서 스크립트를 실행하여 시작합니다. 암호 동기화 구성의 개요를 제공합니다. ![암호 동기화 설정에서 PowerShell 스크립트 출력](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png) Azure AD에서 이 기능을 사용하지 않거나 동기화 채널 상태를 사용하지 않는 경우 연결 설치 마법사를 실행합니다. **동기화 사용자 지정 옵션**을 선택하고 암호 동기화의 선택을 취소합니다. 이 변경 내용은 기능을 일시적으로 비활성화합니다. 그런 다음 마법사를 다시 실행하고 암호 동기화를 다시 사용합니다. 스크립트를 다시 실행하여 구성이 올바른지 확인합니다.

스크립트에서 하트비트가 없다는 것을 보여 주는 경우 [모든 암호의 전체 동기화 트리거](#trigger-a-full-sync-of-all-passwords)에서 스크립트를 실행합니다. 이 스크립트는 구성이 올바르지만 암호가 동기화되지 않은 다른 시나리오에서도 사용할 수 있습니다.

#### 암호 동기화 설정의 상태 가져오기
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Microsoft Azure Active Directory (Microsoft)"}
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

#### 모든 암호의 전체 동기화를 트리거합니다.
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

## 다음 단계
* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->