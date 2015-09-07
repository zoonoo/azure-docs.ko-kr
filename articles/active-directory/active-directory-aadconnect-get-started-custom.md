<properties 
	pageTitle="Azure AD Connect의 사용자 지정 설치"
	description="이 문서는 Azure AD Connect에 대한 사용자 지정 설치 옵션에 대해 자세히 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Azure AD Connect의 사용자 지정 설치


다음 설명서는 Azure AD Connect에 대한 사용자 지정 설치 옵션을 사용하여 정보를 제공합니다. 추가 구성 옵션이 있거나 빠른 설치에서 해결되지 않은 선택적 기능이 필요한 경우 이 옵션을 사용할 수 있습니다.

빠른 설치에 대한 자세한 내용은 [빠른 설치](active-directory-aadconnect.md#getting-started-with-azure-ad-connect)를 참조하세요. 디렉터리 동기화에서 Azure AD Connect로 업그레이드에 대한 내용은 [디렉터리 동기화를 Azure Active Directory 연결로 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md)를 참조하세요.



## 필요한 구성 요소 설치

동기화 서비스를 설치할 때는 선택적 구성 섹션을 선택 취소된 채로 두면 됩니다. 그러면 Azure AD Connect에서 모든 사항이 자동 설정됩니다. SQL Server 2012 Express 설정, 적절한 그룹 만들기, 그룹에 권한 할당 등이 자동으로 설정됩니다. 기본값을 변경하려는 경우 다음 테이블을 참조하여 사용할 수 있는 선택적 구성 옵션을 이해할 수 있습니다.

![필수 구성 요소](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)


선택적 구성 | 설명 
------------- | ------------- |
SQL Server 이름 |SQL Server 이름 및 인스턴스 이름을 지정할 수 있습니다. 사용하려는 AD 데이터베이스 서버가 이미 있는 경우 이 옵션을 선택합니다.
서비스 계정 |기본적으로 Azure AD Connect는 사용할 동기화 서비스에 대한 로컬 서비스 계정을 만듭니다. 발생하는 문제는 암호가 자동으로 생성되어 Azure AD Connect를 설치하는 사람이 암호를 알 수 없다는 점입니다. 대부분의 시나리오에서 무방하지만 동기화되는 부서의 범위를 지정하는 등의 고급 구성을 수행하려는 경우에는 계정을 만들고 고유 암호를 선택하고자 합니다. 그러나 원격 SQL 서버를 사용하는 경우 도메인에 서비스 계정이 필요하며 암호를 알아야 합니다. 이러한 경우에 사용할 서비스 계정을 입력합니다. |
권한 | 동기화 서비스가 설치되면 기본적으로 Azure AD Connect에서는 서버에 대해 4개의 그룹 로컬을 만듭니다. 이 그룹은 Administrators 그룹, Operators 그룹, Browse 그룹, Password Reset 그룹입니다. 고유한 그룹을 지정하려면 여기서 지정하면 됩니다.


## 사용자 로그인
필수 구성 요소를 설치한 후 사용자가 SSO 메서드를 사용하는 방법을 지정해야 합니다. 다음 테이블에서 사용 가능한 옵션에 대한 간략한 설명을 제공합니다.

![사용자 로그인](./media/active-directory-aadconnect-get-started-custom/usersignin.png)



SSO(Single Sign-On) 옵션 | 설명 
------------- | ------------- |
암호 동기화 |사용자는 온-프레미스 네트워크에 로그인하는 데 사용하는 것과 동일한 암호를 사용하여 Office 365, Dynamics CRM 및 Windows InTune과 같은 Microsoft 클라우드 서비스에 로그인할 수 있습니다. 사용자 암호는 암호 해시를 통해 Azure에 동기화되며 인증이 클라우드에서 발생합니다.
AD FS로 페더레이션|사용자는 온-프레미스 네트워크에 로그인하는 데 사용하는 것과 동일한 암호를 사용하여 Office 365, Dynamics CRM 및 Windows InTune과 같은 Microsoft 클라우드 서비스에 로그인할 수 있습니다. 사용자는 로그인을 위해 해당 온-프레미스 ad fs 인스턴스로 리디렉션되며 인증은 온-프레미스에서 수행됩니다.
구성하지 않음| 설치되어 구성된 기능이 없습니다. 이미 타사 페더레이션 서버 또는 다른 기존 솔루션이 있는 경우 이 옵션을 선택합니다.



## Azure에 연결
Azure AD에 연결 화면에서, 전역 관리자 계정 및 암호를 입력합니다. 이 계정으로 다단계 인증을 사용할 수 없는지 확인합니다. 이렇게 하면 인증에 실패할 수 있습니다. 이 계정은 Azure AD에서 서비스 계정을 만드는 데에만 사용되며 마법사를 완료 한 후 사용되지 않습니다.

![사용자 로그인](./media/active-directory-aadconnect-get-started-custom/connectaad.png)


### 디렉터리에 연결
Active Directory 도메인 서비스에 연결하려면, Azure AD Connect는 충분한 권한이 있는 계정의 자격 증명이 필요합니다. 기본 읽기 권한만 필요하기 때문에 이 계정은 일반 사용자 계정일 수 있습니다. 그러나 시나리오에 따라 추가 권한이 필요할 수 있습니다. 자세한 내용은 [Azure AD Connect 계정 요약](active-directory-aadconnect-account-summary.md)을 참조하세요.

![사용자 로그인](./media/active-directory-aadconnect-get-started-custom/connectdir.png)


### 사용자를 고유하게 식별

포리스트 기능 간에 일치를 사용하여 AD DS 포리스트의 사용자가 Azure AD에서 표현되는 방법을 정의할 수 있습니다. 사용자는 포리스트 전반에 걸쳐 한번만 표시할 수 있거나 활성화된 계정과 비활성화된 계정의 조합으로 이루어집니다.

![사용자 로그인](./media/active-directory-aadconnect-get-started-custom/unique.png)


설정 | 설명 
------------- | ------------- |
사용자는 모든 포리스트에 걸쳐 한번만 표시됩니다.| 모든 사용자는 Azure AD에 개별 개체로 만들어집니다.<br> 개체는 메타 버스에 연결되지 않습니다.
Mail 특성 | 메일 특성에 다른 포리스트의 동일한 값이 있는 경우 이 옵션은 사용자 및 연락처를 연결합니다. 연락처가 GALSync를 사용하여 만들어진 경우 이 옵션을 사용하는 것이 좋습니다.
ObjectSID 및 msExchangeMasterAccountSID|이 옵션은 계정 포리스트에서 활성화된 사용자와 Exchange 리소스 포리스트의 비활성화된 사용자를 연결합니다. Exchange에서 연결된 사서함이라고도 합니다.
sAMAccountName 및 MailNickName|이 옵션은 사용자에 대한 로그인 ID를 찾을 수 있을 것으로 예상되는 특성에 연결합니다.
나만의 특성|이 옵션을 사용하면 고유한 특성을 선택할 수 있습니다. **제한:** 메타버스에 이미 존재하는 특성을 선택할 수 있는지 확인합니다. 사용자 지정 특성을 선택하면 마법사를 완료할 수 없습니다.

- **소스 앵커** -특성 sourceAnchor는 사용자 개체의 수명 동안 변경할 수 없는 특성입니다. Azure AD에서 사용자와 온-프레미스 사용자를 연결하는 기본 키입니다. 특성을 변경할 수 없으므로, 좋은 특성을 사용해야 합니다. 좋은 후보는 objectGUID입니다. 사용자 계정이 포리스트/도메인 간에 이동하지 않는 한 이 특성은 변경되지 않습니다. 포리스트 간에 계정을 이동하는 다중 포리스트 환경에서 employeeID가 있는 특성과 같은 다른 특성이 사용되어야 합니다. 피해야할 특성은 결혼하거나 약속을 변경한 경우 변경되는 특성입니다. @ 기호와 함께 특성을 사용할 수 없으므로 전자 메일 및 userPrincipalName을 사용할 수 없습니다. 특성은 대/소문자를 구분하므로 포리스트 간에 개체를 이동하는 경우 대/소문자를 유지해야 합니다. 이진 특성의 경우 값은 base64로 인코딩되지만 다른 특성 유형의 경우 인코딩되지 않은 상태로 유지됩니다. 페더레이션 시나리오 및 일부 Azure AD 인터페이스에서 이 특성은 immutableID라고도 합니다.

- **UserPrincipalName** -특성 userPrincipalName은 Azure AD 및 Office 365에 로그인하는 경우 사용자가 사용하는 특성입니다. UPN-접미사로 알려진 사용된 도메인은 사용자가 동기화되기 전에 Azure AD에서 확인해야 합니다. 기본 특성 userPrincipalName을 유지하는 것이 좋습니다. 이 특성은 라우팅할 수 없고 확인할 수 없으며 전자 메일의 경우 로그인 ID를 보유하는 특성으로 다른 특성을 선택할 수 없습니다. **대체 ID**로도 알려져 있습니다. 대체 ID 특성 값은 RFC822 표준을 따라야 합니다. 대체 ID는 로그인 솔루션으로 암호 SSO 및 페더레이션 SSO 모두를 함께 사용할 수 있습니다.

>[AZURE.WARNING]대체 ID를 사용하면 모든 Office 365 작업 부하와 호환되지 않습니다. 자세한 내용은 [대체 로그인 ID 구성](https://technet.microsoft.com/library/dn659436.aspx.)을 참조하십시오.







### 그룹에 따라 동기화 필터링
그룹 기능에 대한 필터링을 사용하면 Azure AD 및 Office 365에 개체의 작은 하위 집합만 만들어야 하는 작은 파일럿을 실행할 수 있습니다. 이 기능을 사용하려면, Active Directory에서 그룹을 만들고 직접 구성원으로 Azure AD와 동기화되어야 하는 사용자와 그룹을 추가합니다. 나중에 사용자를 이 그룹에 추가하고 제거하여 Azure AD에 있어야 하는 개체의 목록을 유지할 수 있습니다. 이 기능을 사용하려면 사용자 지정된 경로에 페이지가 표시됩니다.

![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/filter2.png)


### 선택적 기능

이 화면을 사용하면 특정 시나리오에 대 한 선택적 기능을 선택할 수 있습니다. 다음은 각각의 개별 기능에 대해 간략히 설명합니다.

<center>![빠른 설치] (. /media/active-directory-aadconnect-get-started-custom/optional.png)</center>


선택적 기능 | 설명
-------------------    | ------------- | 
Exchange 하이브리드 배포 |Exchange 하이브리드 배포 기능을 사용하면Azure AD에서 특정 특성 집합을 온-프레미스 디렉터리에 다시 동기화하여 Exchange 사서함이 온-프레미스와 Azure에서 공존할 수 있습니다.
Azure AD 앱 및 특성 필터링|Azure AD 앱 및 특성 필터링을 사용하도록 설정하면 동기화된 특성 집합을 마법사의 후속 페이지에 나오는 특정 집합에 맞게 지정할 수 있습니다. 마법사에서 두 개의 추가 구성 페이지가 열립니다.  
암호 쓰기 저장|암호 쓰기 저장을 사용하도록 설정하면 Azure AD에서 이루어지는 암호 변경 사항이 온-프레미스 디렉터리에 다시 기록됩니다.
디렉터리 확장 특성 동기화|디렉터리 확장 특성 동기화를 사용하도록 설정하면 지정된 특성이 Azure AD에 동기화됩니다. 이 마법사에서 추가 구성 페이지가 열립니다.  

기본 구성을 변경하거나 동기화 규칙 편집기 및 선언적 프로비저닝을 사용하는 등의 추가 구성 옵션에 대한 자세한 내용은 [Azure AD Connect 관리](active-directory-aadconnect-whats-next.md)를 참조하세요.




사용자 및 그룹을 포함하는 그룹의 이름을 추가합니다. 이 그룹의 구성원만 Azure AD로 동기화됩니다.

## 디렉터리 확장 특성 동기화
디렉터리 확장을 사용하여, Active Directory의 다른 특성 또는 조직에서 추가하는 사용자 지정 특성으로 Azure AD에서 스키마를 확장할 수 있습니다. 이 기능을 사용하려면, "선택적 기능" 페이지에서 "디렉터리 확장 특성 동기화"를 선택합니다. 이렇게 하면 이 페이지에서 추가 특성을 선택할 수 있습니다.

![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/extension2.png)


단일 값 특성만이 지원되고 값이 250자를 초과할 수 없습니다. 메타 버스 및 Azure AD 스키마는 선택된 특성으로 확장됩니다. Azure AD에서 새 응용 프로그램은 특성으로 추가됩니다.

![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/extension3.png)


이제 이 특성은 그래프를 통해 사용할 수 있습니다.

![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/extension4.png)




## 그룹 쓰기 저장(미리 보기)

> [AZURE.WARNING]현재 디렉터리 동기화 또는 Azure AD Sync가 활성화되어 있는 경우 Azure AD Connect에서 쓰기 저장 기능을 활성화하지 마세요.

선택적 기능의 그룹 쓰기 저장에 대한 옵션을 사용하면 Exchange가 설치된 포리스트로 "Office 365의 그룹"을 쓰기 저장할 수 있습니다. 항상 클라우드에서 마스터되는 새 그룹 종류입니다. 다음과 같이 outlook.office365.com 또는 myapps.microsoft.com에서 찾을 수 있습니다.


![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/office365.png)



![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/myapps.png)


이 그룹은 온-프레미스 AD DS에서 배포 그룹으로 표현됩니다. 이 새 그룹 종류를 인식하려면 온-프레미스 Exchange는 Exchange 2013 누적 업데이트 8(2015년 3월에 릴리스됨)이어야 합니다.

**참고**

- 현재 주소록 특성이 채워지지 않습니다. 조직의 다른 그룹에서 주소록 속성을 찾고 동기화 엔진 외부를 채우는 것이 가장 쉽습니다. **이 작업을 수행하는 가장 쉬운 방법은 PowerShell cmdlet update-recipient를 사용하는 것입니다.**
- Exchange 스키마가 있는 포리스트만 그룹에 대한 유효한 대상이 됩니다. 검색된 Exchange가 없는 경우, 그룹 쓰기 저장을 사용하도록 설정할 수 없습니다.
- 그룹 쓰기 저장 기능은 현재 보안 그룹 또는 배포 그룹을 처리하지 않습니다.

자세한 내용은 [여기](http://blogs.office.com/2014/09/25/delivering-first-chapter-groups-office-365/)를 참조하세요.

## 장치 쓰기 저장(미리 보기)

> [AZURE.WARNING]현재 디렉터리 동기화 또는 Azure AD Sync가 활성화되어 있는 경우 Azure AD Connect에서 쓰기 저장 기능을 활성화하지 마세요.

장치 쓰기 저장 기능을 사용하면 Intune에서와 같이 클라우드에서 등록된 장치를 사용하여 조건부 액세스를 위해 AD DS에 게 허용할 수 있습니다. 기능을 사용하려면 AD DS가 준비되어야 합니다. AD FS 및 장치 등록 서비스(DRS)를 설치하는 경우, DRS는 PowerShell cmdlet을 제공하여 장치 쓰기 저장을 위한 AD를 준비합니다. DRS를 설치하지 않는 경우 C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1을 엔터프라이즈 관리자로 실행할 수 있습니다.

PowerShell cmdlet을 실행하려면 먼저 가져와야 합니다.

	Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'

이 작업을 수행하려면 Active Directory 및 MSOnline PowerShell이 로컬에 설치되어 있어야 합니다.



## 스테이징 모드
스테이징 모드를 사용하여 기존 서버와 함께 병렬로 새 동기화 서버를 설정하는 프로세스가 가능합니다. 클라우드에서 한 디렉터리에 연결하는 한 동기화 서버에서만 지원됩니다. 하지만 DirSync를 실행하는 서버와 같은 다른 서버에서 이동하려는 경우, 스테이징 모드에서 Azure AD Connect를 사용할 수 있습니다. 활성화된 동기화 엔진이 데이터를 정상적으로 가져오고 동기화하지만, Azure AD로 아무것도 내보내지 않고 암호 동기화 및 암호 쓰기 저장을 해제합니다.

![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)


스테이징 모드에 있는 동안, 동기화 엔진에 필요한 변경 내용을 작성하고 내보낼 내용을 검토합니다. 구성마음에 드는 경우, 설치 마법사를 다시 실행하고 스테이징 모드를 사용하지 않도록 설정합니다. 이렇게 하면 데이터를 Azure AD로 내보낼 수 있습니다. 한 서버만이 내보낼 수 있으므로 동시에 다른 서버를 사용하지 않도록 설정했는지 확인합니다.

### 실수로 인한 삭제 방지
Azure AD Connect를 설치하는 경우 실수로 인한 삭제를 방지하는 기능을 기본적으로 사용하고 500개가 넘는 삭제된 내보내기를 허용하지 않도록 구성합니다. 500개가 기본 값이며 변경할 수 있습니다. 이 기능을 사용하면 너무 많이 삭제한 경우 내보내기가 계속되지 않으며 다음과 같이 전자 메일을 받게 됩니다.

![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/email.png)


예상된 경우가 아니라면 조사하여 수정 작업을 수행합니다.

일시적으로 이 보호를 해제하고 삭제를 진행할 수 있도록 하려면, 이를 해제 하려면 Disable-ADSyncExportDeletionThreshold를 실행합니다.

보호를 다시 설정하거나 기본 임계값 설정을 변경하려면 Enable-ADSyncExportDeletionThreshold를 실행합니다.


## AD FS로 페더레이션 구성
Azure AD Connect를 사용하여 AD FS를 구성하는 것은 단 몇 번의 클릭으로 간단합니다. 다음은 설치하기 전에 필요합니다.

- 원격 관리가 사용 가능한 페더레이션 서버용 Windows Server 2012 R2 서버
- 원격 관리가 사용 가능한 웹 응용 프로그램 프록시 서버용 Windows Server 2012 R2 서버
- 사용할 페더레이션 서비스 이름에 대한 SSL 인증서(예: adfs.contoso.com)

### 새 AD FS 팜을 만들거나 기존 AD FS 팜 사용
기존 AD FS 팜을 사용하거나 새 AD FS 팜을 만들도록 선택할 수 있습니다. 새로 만들기를 선택하면 SSL 인증서를 제공해야 합니다. SSL 인증서가 암호로 보호되는 경우, 암호를 제공하라는 메시지가 표시됩니다.

![AD FS 팜](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

**참고:** 기존 AD FS 팜에 사용하려는 경우, 몇 페이지를 건너뛰고 직접 AD FS와 Azure AD 간의 트러스트 관계 구성으로 이동합니다.

### AD FS 서버 지정

여기에서 AD FS를 설치하려는 특정 서버를 입력합니다. 용량 계획 요구 사항에 따라 하나 이상의 서버를 추가할 수 있습니다. 이 서버는 모두 이 구성 작업을 수행하기 전에 Active Directory 도메인에 가입되어야 합니다. 테스트 및 파일럿 배포를 위한 단일 AD FS 서버를 설치하고 Azure AD Connect를 열어 새 서버를 배포하고 크기 조정 요구 사항에 맞게 추가 서버에 AD FS를 배포하여 추가 서버를 배포 하는 것이 좋습니다.


> [AZURE.NOTE]이 구성 작업을 수행하기 전에 모든 서버가 AD 도메인에 가입되었는지 확인합니다.

![AD FS 서버](./media/active-directory-aadconnect-get-started-custom/adfs2.png)


 
### 웹 응용 프로그램 프록시 서버 지정
여기에서 웹 응용 프로그램 프록시 서버로 원하는 특정 서버를 입력합니다. 웹 응용 프로그램 프록시 서버는 DMZ(엑스트라넷 연결)에 배포되며 엑스트라넷에서 인증 요청을 지원합니다. 용량 계획 요구 사항에 따라 하나 이상의 서버를 추가할 수 있습니다. 테스트 및 파일럿 배포를 위한 단일 AD FS 서버를 설치하고 Azure AD Connect를 열어 추가 서버를 배포하고 웹 응용 프로그램 프록시를 추가 서버에 배포하는 것이 좋습니다. 일반적으로 인트라넷에서 인증을 충족하기 위해 동일한 수의 프록시 서버가 있는 것이 좋습니다.

> [AZURE.NOTE]<li>Azure AD Connect를 설치하는 데 사용 하는 계정이 AD FS 서버에서 로컬 관리자가 아닌 경우, 충분한 권한이 있는 계정의 자격 증명에 대한 메시지가 표시됩니다.</li><li>이 단계를 구성하기 전에 Azure AD Connect 서버와 웹 응용 프로그램 프록시 서버 간의 HTTP/HTTPS 연결이 있는지 확인합니다.</li><li> 또한 웹 응용 프로그램 서버와 AD FS 서버 간의 HTTP/HTTPS 연결을 통해 인증 요청이 허용되는지 확인합니다.</li>


![웹 앱](./media/active-directory-aadconnect-get-started-custom/adfs3.png)


웹 응용 프로그램 서버가 AD FS 서버에 보안 연결을 설정할 수 있도록 자격 증명을 입력하라는 메시지가 표시됩니다. 이러한 자격 증명에서 로컬 관리자는 AD FS 서버에 있어야 합니다.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

 
### AD FS 서비스에 대한 서비스 계정 지정
AD FS 서비스가 Active Directory에서 사용자를 인증하고 사용자 정보를 검색하는데 도메인 서비스 계정이 필요합니다. 두 종류의 서비스 계정을 지원할 수 있습니다.

- **그룹 관리 서비스 계정** -Windows Server 2012가 포함된 Active Directory 도메인 서비스에 도입된 서비스 계정의 형식입니다. 이러한 유형의 계정은 정기적으로 계정 암호를 업데이트할 필요 없이 단일 계정을 사용하려면 AD FS와 같은 서비스를 제공합니다. AD FS 서버에 속하는 도메인에서 Windows Server 2012 도메인 컨트롤러가 이미 있는 경우 이 옵션을 사용합니다.
- **도메인 사용자 계정** -이 유형의 계정은 암호를 입력하고 암호를 변경하는 경우 암호를 정기적으로 업데이트를 해야 합니다. AD FS 서버에 속하는 도메인에 Windows Server 2012 도메인 컨트롤러가 없는 경우에만 사용합니다.

도메인 관리자로 로그인한 경우 Azure AD Connect는 그룹 관리 서비스 계정을 자동으로 만듭니다.
 
![AD FS 서비스 계정](./media/active-directory-aadconnect-get-started-custom/adfs5.png)


### 페더레이션하려는 Azure AD 도메인을 선택합니다.
이 구성은 AD FS와 Azure AD 간의 페더레이션 관계를 설정하는데 사용됩니다. Azure AD에 보안 토큰을 발급하도록 AD FS를 구성하고 이 특정 AD FS 인스턴스에서 토큰을 신뢰하도록 Azure AD를 구성합니다. 이 페이지만을 사용하여 첫 시간 환경에 단일 도메인을 구성할 수 있습니다. Azure AD Connect를 다시 열고 이 작업을 수행하여 언제든지 추가 도메인을 구성할 수 있습니다.

 
![Azure AD 도메인](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

 
### 추가 작업을 수행하여 페더레이션 구성을 완료합니다.
다음 추가 작업은 페더레이션 구성을 끝내기 위해 완료해야 합니다.

- 인트라넷(내부 DNS 서버) 및 엑스트라넷(도메인 등록 기관을 통한 공용 DNS) 모두에 대한 AD FS 페더레이션 서비스 이름(예: adfs.contoso.com)에 대한 DNS 레코드를 설정합니다. 인트라넷 DNS 레코드의 경우, CNAME 레코드가 아닌 A 레코드를 사용해야 합니다. 도메인 연결된 컴퓨터에서 제대로 작동 하려면 windows 인증이 필요합니다.
- 둘 이상의 AD FS 서버 또는 웹 응용 프로그램 프록시 서버를 배포하는 경우, 사용자 부하 분산 장치를 구성하고 AD FS 페더레이션 서비스 이름(예: adfs.contoso.com)에 대한 DNS 레코드가 부하 분산 장치를 가리키는지 확인합니다.
- windows 통합된 인증이 인트라넷에서 Internet Explorer를 사용하여 브라우저 응용 프로그램을 작동하려면, AD FS 페더레이션 서비스 이름(예: adfs.contoso.com)이 IE에서 인트라넷 영역에 추가되었는지를 확인합니다. 그룹 정책을 통해 제어되고 모든 사용자 도메인에 가입된 컴퓨터에 배포될 수 있습니다. 

### 페더레이션 구성 확인

확인 단추를 클릭할 때 Azure AD Connect에서 DNS 설정을 확인합니다.

![완료](./media/active-directory-aadconnect-get-started-custom/adfs7.png)
 
 
또한 다음 확인 단계를 수행합니다.

- 인트라넷에서 Internet Explorer에서 도메인 가입된 컴퓨터에서 브라우저 로그인의 유효성을 검사합니다. https://myapps.microsoft.com에 연결하고 로그인한 계정으로 로그인을 확인합니다.
- 엑스트라넷의 모든 장치에서 브라우저 로그인의 유효성을 검사합니다. 홈 컴퓨터 또는 모바일 장치에서 https://myapps.microsoft.com에 연결하고 로그인 ID 및 암호 자격 증명을 제공합니다.
- 리치 클라이언트 로그인의 유효성을 검사합니다. https://testconnectivity.microsoft.com에 연결하고, ' Office 365' 탭을 선택하고 ' Office 365 Single Sign-on 테스트 '를 선택합니다.

### AD FS 서비스에서 옵션 구성
AD FS에 로그인하고 PSH를 사용하여 이렇게 구성하여 AD FS 로그인 페이지에 대한 그림 및 로고 이미지를 사용자 지정할 수 있습니다.
	
	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}

<!---HONumber=August15_HO9-->