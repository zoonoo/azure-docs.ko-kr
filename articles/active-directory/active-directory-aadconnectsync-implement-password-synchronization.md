<properties
	pageTitle="Azure AD Connect 동기화: 구현암호 동기화 | Microsoft Azure"
	description="암호 동기화 작동 방법 및 사용자 환경에서 사용하도록 설정하는 방법을 이해하는데 필요한 정보를 제공합니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/16/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 동기화: 암호 동기화 구현

암호 동기화를 사용하면 사용자가 Azure Active Directory에 로그인하여 온-프레미스에 로그인하는데 사용하는 동일한 암호를 사용할 수 있습니다.

이 항목의 목표는 암호 동기화 작동 방법 및 사용자 환경에서 사용하도록 설정하는 방법을 이해하는데 필요한 정보를 제공하는 것입니다.

## 암호 동기화란 무엇입니까?

암호 동기화는 온 프레미스 Active Directory에서 Azure Active Directory(Azure AD)로 사용자의 암호를 동기화하는 Azure Active Directory Connect 동기화(Azure AD Connect 동기화)의 기능입니다. 이 기능은 사용자가 자신의 Azure Active Directory 서비스 (예: Office 365, Microsoft Intune, CRM Online)에 로그인할 때 온-프레미스 네트워크에 로그인할 때 사용하는 동일한 암호를 사용합니다.

> [AZURE.NOTE]FIPS 및 암호 동기화에 대해 Active Directory 도메인 서비스 구성에 대한 자세한 내용은 [암호 동기화 및 FIPS](#password-synchronization-and-fips)를 참조하세요.

### 암호 동기화의 가용성

Azure Active Directory의 모든 고객은 암호 동기화를 실행할 수 있습니다. 암호 동기화의 호환성 및 페더레이션 인증과 같은 다른 기능에 대한 정보는 아래를 참조하세요.

### 암호 동기화 작동 방법

암호 동기화는 Azure AD Connect 동기화를 통한 디렉터리 동기화 기능 구현의 확장판입니다. 따라서, 이 기능은 온-프레미스와 구성할 Azure Active Directory간에 디렉터리 동기화를 요구합니다.

Active Directory 도메인 서비스는 실제 사용자 암호의 해시 값 표시 형태로 암호를 저장합니다. 암호 해시는 온-프레미스 네트워크에 로그인할 때 사용할 수 없습니다. 또한 사용자의 일반 텍스트 암호에 대한 액세스 권한을 얻기위해 reverse할 수 없도록 설계 되었습니다. 암호를 동기화 하기 위해, Azure AD Connect 동기화가 사용자의 암호 해시를 온-프레미스 Active Directory에서 추출합니다. Azure Active Directory 인증 서비스로 동기화 되기 전에 암호 해시에 추가적인 보안 처리가 적용됩니다. 암호 동기화 과정의 실제 흐름은 DisplayName 또는 전자 메일 주소와 같은 사용자 데이터 동기화와 비슷합니다.

암호는 다른특성에 대한 표준 디렉터리 동기화 창 보다 더 자주 동기화 됩니다. 암호는 각 사용자 기본별로 동기화되고 일반적으로는 시간 순서대로 동기화됩니다. 사용자의 암호가 온-프레미스 AD에서 클라우드로 동기화 될 때, 기존 클라우드 암호는 덮여쓰여집니다.

처음 암호 동기화 기능을 실행하면 온-프레미스 Active Directory부터 Azure Active Directory까지의 모든 범위 내의 사용자 암호의 초기 동기화를 수행합니다. 클라우드에 동기화된 암호를 갖게될 사용자의 집합을 명시적으로 정의할 수 없습니다. 온-프레미스 사용자가 암호를 변경한 이후에, 암호 동기화 기능은 짧은 시간 동안 가장 자주 변경된 암호를 검색하고 동기화합니다. 암호 동기화 기능은 사용자 암호 동기화가 실패할 경우 자동으로 다시 시도합니다. 암호를 동기화하는 동안 오류가 발생하면 이벤트 뷰어에 오류가 기록됩니다.

암호 동기화는 현재 로그온한 사용자에게 아무런 영향도 미치지 않습니다. 클라우드 서비스에 로그인한 사용자가 온-프레미스 암호를 바꿔도, 클라우드 서비스 세션을 중단없이 계속됩니다. 그러나, 새 암호를 제공 받으려면, 사용자는 클라우드 서비스에 빨리 재인증을 해야합니다. 이 시점에서 사용자는 새 암호 즉, 온-프레미스 Active Directory에서 클라우드로 최근에 동기화된 암호를 제공하도록 요구받습니다.

> [AZURE.NOTE]암호 동기화는 Active Directory의 개체 형식 사용자에만 지원됩니다. iNetOrgPerson 개체 형식에 대해 지원되지 않습니다.

### Azure AD 도메인 서비스와 함께 암호를 동기화하는 방법

Azure AD에서 이 서비스를 사용하는 경우 암호 동기화 옵션에서 Single-Sign On 환경을 가져와야 합니다. 이 서비스를 사용하도록 설정하면 암호 동기화에 대한 동작이 변경되고 암호 해시도 온-프레미스 Active Directory에서 Azure AD 도메인 서비스까지 현재 상태로 동기화될 것입니다. 이 기능은 ADMT(Active Directory Migration Tool: Active Directory 마이그레이션 도구)와 비슷하며 Azure AD 도메인 서비스를 통해 온-프레미스 AD에서 사용할 수 있는 모든 방법으로 사용자를 인증할 수 있습니다.

### 보안 고려 사항

암호를 동기화 할 때, 사용자 암호의 일반 텍스트 버전은 암호 동기화 기능이나 Azure AD 혹은 다른 어떤 관련 서비스에 노출되지 않아야 합니다.

또한 역방향으로 암호화 된 형식으로 암호를 저장하는 온-프레미스 Director에서는 요구사항이 없습니다. Active Directory 암호 해시의 다이제스트는 온-프레미스 AD와 Azure Active Directory간의 전송에 사용됩니다. 암호 해시의 다이제스트는 고객의 온-프레미스 환경의 원본에 액세스 할 때 사용할 수 없습니다.

### 암호 정책 고려 사항

암호 동기화를 사용하여 영향을 받는 두가지 정책이 있습니다.

1. 암호 복잡성 정책
2. 암호 만료 정책

**암호 복잡성 정책**

암호 동기화를 사용하도록 설정하면, 온-프레미스 Active Directory 내에서 구성된 암호 복잡성 정책은 동기화된 사용자의 클라우드 내에서 정의되었을 수 있는 모든 복잡성 정책을 재정의합니다. 즉, 고객의 온-프레미스 Active Directory 환경에서 유효한 모든 암호는 Azure AD 서비스에 액세스하는데 사용할 수 있습니다.

> [AZURE.NOTE]클라우드에서 직접 만든 사용자의 암호는 클라우드 내에서 정의된 암호 정책을 계속 따릅니다.

**암호 만료 정책**

사용자가 암호 동기화 범위 내에 있을 경우 클라우드 계정 암호는 "*사용 기간 제한 없음*"으로 설정됩니다. 즉, 사용자의 암호가 온-프레미스 환경에서 만료되어도 만료된 암호를 사용하여 클라우드 서비스에 계속 로그인할 수 있습니다.

클라우드 암호는 사용자가 온-프레미스 환경의 암호를 변경한 후에 업데이트됩니다.

### 동기화된 암호 덮어쓰기

관리자가 Azure Active Directory PowerShell을 사용하여 사용자의 암호를 수동으로 재설정할 수 있습니다.

이 경우, 새 암호는 사용자의 동기화 암호를 재정의하며, 클라우드 내에 정의된 모든 암호 정책이 새 암호에 적용됩니다.

사용자가 온-프레미스 암호를 다시 변경하는 경우, 새 암호는 클라우드에 동기화되며, 수동으로 업데이트한 암호를 재정의합니다.

## 암호 동기화를 위한 준비


### 암호 동기화를 사용하도록 설정

Azure AD Connect를 설치할 때 기본 설정을 사용하는 경우 암호 동기화는 기본값으로 설정됩니다.

Azure AD Connect를 설치할 때 사용자 지정 설정을 사용하는 경우 사용자 로그인 페이지에서 암호 동기화를 설정할 수 있습니다. ![usersignin](./media/active-directory-aadsync-implement-password-synchronization/usersignin.png)

**AD FS로 페더레이션** 사용을 선택하는 경우 필요에 따라 AD FS 인프라가 실패할 경우 백업으로 암호 동기화 기능을 사용할 수 있습니다. Azure AD 도메인 서비스를 사용하려는 경우 이 기능을 설정할 수도 있습니다.

### 암호 동기화 및 FIPS

서버가 FIPS(Federal Information Processing Standard)에 따라 잠긴 다음 MD5가 비활성화됩니다. 암호 동기화에 대한 기능을 사용하려면 C:\\Program Files\\Azure AD Sync\\Bin의 miiserver.exe.config에서 enforceFIPSPolicy 키를 추가합니다.

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

구성 파일의 끝에서 구성/런타임 노드를 찾을 수 있습니다.

보안 및 FIPS에 대한 자세한 내용은 [AAD 암호 동기화, 암호화 및 FIPS 준수](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx)를 참조하십시오.

## 암호 동기화 관리

### 암호 동기화 문제 해결

**동기화 서비스 관리자**를 시작하여 **커넥터**를 열고 해당 사용자가 있는 Active Directory Connector를 선택하고 **커넥터 공간 검색**을 선택하여 찾고 있는 사용자를 검색합니다.

![csuser](./media/active-directory-aadsync-implement-password-synchronization/cspasswordsync.png)

사용자가 **계보** 탭을 선택하여 최소한 하나 이상의 동기화 규칙에서 **암호 동기화**가 **True**로 표시되는지 확인합니다. 기본 구성을 사용하면 이 규칙은 **In from AD - User AccountEnabled**로 명명된 동기화 규칙입니다.

해당 개체에 대한 암호 동기화 세부 정보를 보려면 이 페이지 하단의 **로그...** 단추를 클릭하세요. 그러면 해당 사용자의 지난 주 암호 동기화 상태에 대한 기록 보기가 포함된 이 페이지가 생성됩니다.

![개체 로그](./media/active-directory-aadsync-implement-password-synchronization/csobjectlog.png)

상태 열은 암호가 동기화되지 않은 이유 및 문제가 있음을 나타내는 다음 값을 가질 수 있습니다.

| 상태 | 설명 |
| ---- | ----- |
| 성공 | 암호가 성공적으로 동기화되었습니다. |
| FilteredByTarget | **다음 로그인할 때 반드시 암호 변경**으로 암호가 설정됩니다. 암호가 동기화되지 않았습니다. |
| NoTargetConnection | 메타버스에 또는 Azure AD 커넥터 공간에 개체가 없습니다. |
| SourceConnectorNotPresent | 개체를 온-프레미스 Active Directory Connector 공간에서 찾을 수 없습니다. |
| TargetNotExportedToDirectory | Azure AD 커넥터 공간에 있는 개체가 아직 내보내지지 않았습니다. |
| MigratedCheckDetailsForMoreInfo | 로그 항목 1.0.9125.0 빌드 전에 만들어졌으며 레거시 상태로 표시됩니다. |


### 모든 암호의 전체 동기화를 트리거합니다.
모든 암호의 전체 동기화를 강제로 적용할 필요는 없지만 어떤 이유로 실행해야 하는 경우 동기화를 위한 PowerShell은 다음과 같습니다.

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## 추가 리소스

* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=AcomDC_1203_2015-->