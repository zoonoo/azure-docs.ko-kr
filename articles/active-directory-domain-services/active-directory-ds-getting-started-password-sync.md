<properties
	pageTitle="Azure Active Directory 도메인 서비스 미리 보기: 시작 | Microsoft Azure"
	description="Azure Active Directory 도메인 서비스 시작"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스*(미리 보기)* - 시작

## 5단계: 암호 동기화 사용
Azure AD 테넌트에 Azure AD 도메인 서비스를 사용하도록 설정한 후 수행할 단계는 암호 동기화를 사용하도록 설정하는 것입니다. 이렇게 하면 사용자가 회사 자격 증명을 사용하여 도메인에 로그인할 수 있습니다.

관련된 단계는 조직이 클라우드 전용 Azure AD 테넌트인지, 아니면 Azure AD Connect를 사용하여 온-프레미스 디렉터리와 동기화되도록 설정되었는지에 따라 다릅니다.

### 클라우드 전용 테넌트 - Azure AD에서 NTLM 및 Kerberos 자격 증명 해시를 생성하도록 설정
조직이 클라우드 전용 Azure AD 테넌트인 경우 Azure AD 도메인 서비스를 사용해야 하는 사용자는 암호를 변경해야 합니다. 이 단계를 수행하면 Kerberos 및 NTLM 인증을 위해 Azure AD 도메인 서비스에 필요한 자격 증명 해시가 Azure AD에서 생성됩니다. Azure AD 도메인 서비스를 사용해야 하는 테넌트의 모든 사용자에 대한 암호를 만료시키거나, 암호를 변경하도록 이러한 사용자에게 지시할 수 있습니다.

암호를 변경하기 위해 사용자에게 제공해야 하는 지침은 다음과 같습니다.

1. 조직의 Azure AD 액세스 패널 페이지로 이동합니다. 이 페이지는 일반적으로 [http://myapps.microsoft.com](http://myapps.microsoft.com)에 있습니다.
2. 이 페이지에서 **프로필** 탭을 선택합니다.
3. 이 페이지에서 **암호 변경** 타일을 클릭하여 암호 변경을 시작합니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. **암호 변경** 페이지가 표시됩니다. 사용자가 기존(이전) 암호를 입력하고 암호 변경을 진행할 수 있습니다.

    ![Azure AD 도메인 서비스에 대한 가상 네트워크를 만듭니다.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

사용자가 암호를 변경한 후 새 암호를 즉시 Azure AD 도메인 서비스에 사용할 수 있습니다. 몇 분 후 사용자가 새로 변경한 암호를 사용하여 관리되는 도메인에 가입된 컴퓨터에 로그인할 수 있습니다.


### 동기화된 테넌트 - Azure AD로 NTLM 및 Kerberos 자격 증명 해시를 동기화하도록 설정
조직의 Azure AD 테넌트가 Azure AD Connect를 사용하여 온-프레미스 디렉터리와 동기화되도록 설정된 경우 NTLM 및 Kerberos 인증에 필요한 자격 증명 해시를 동기화하도록 Azure AD Connect를 구성해야 합니다. 이러한 해시는 기본적으로 Azure AD에 동기화되지 않으며 다음 단계를 통해 Azure AD 테넌트에 해시를 동기화할 수 있도록 설정됩니다.

#### Azure AD Connect 설치 또는 업데이트

도메인에 가입한 컴퓨터에 Azure AD Connect의 최신 권장 릴리스를 설치해야 합니다. Azure AD Connect 설치의 기존 인스턴스가 있는 경우 Azure AD Connect GA 빌드를 사용하도록 업데이트해야 합니다. 알려진 문제/버그를 피하려면 최신 버전의 Azure AD Connect를 사용해야 합니다.

**[Azure AD Connect 다운로드](http://www.microsoft.com/download/details.aspx?id=47594)**

최소 권장 버전: **1.0.9131** - 2015년 12월 3일에 게시되었습니다.

  >[AZURE.WARNING] Azure AD 테넌트에 동기화할 레거시 암호 자격 증명(NTLM 및 Kerberos 인증에 필요)을 사용하도록 설정하려면 Azure AD Connect의 최신 권장 릴리스를 설치해야 합니다. 이 기능은 Azure AD Connect의 이전 릴리스 또는 레거시 DirSync 도구에서 사용할 수 없습니다.

Azure AD Connect에 대한 설치 지침은 [Azure AD Connect 시작](../active-directory/active-directory-aadconnect.md) 문서에 나와 있습니다.


#### Azure AD에 전체 암호 동기화 강제 적용

전체 암호 동기화를 강제로 적용하고 모든 온-프레미스 사용자의 암호 해시(NTLM/Kerberos 인증에 필요한 자격 증명 해시 포함)가 Azure AD 테넌트에 동기화될 수 있도록 설정하려면 각 AD 포리스트에서 다음 PowerShell 스크립트를 실행합니다.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

디렉터리의 크기(사용자, 그룹 등의 수)에 따라 자격 증명을 Azure AD에 동기화하는 데는 시간이 걸립니다. 자격 증명 해시를 Azure AD에 동기화한 직후 Azure AD 도메인 서비스 관리되는 도메인에서 암호를 사용할 수 있습니다.

<!---HONumber=AcomDC_0128_2016-->