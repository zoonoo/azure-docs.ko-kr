<properties 
	pageTitle="Azure Active Directory와 온-프레미스 ID 통합" 
	description="Azure AD Connect의 정의 및 사용해야 하는 이유를 설명합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="billmath"/>

# Azure Active Directory와 온-프레미스 ID 통합



오늘날 사용자는 랩톱, 스마트폰, 태블릿 등 어떤 장치에서든 온-프레미스와 클라우드 모두에서 응용 프로그램에 액세스할 수 있기를 원합니다. 이를 위해서는 관리자와 해당 조직이 사용자에게 이러한 앱에 액세스할 수 있는 방법을 제공할 수 있어야 하지만, 그렇다고 항상 클라우드로 완전히 전환해야 하는 것은 아닙니다.

<center>![Azure AD Connect 정의](./media/active-directory-aadconnect/arch.png)</center>

Azure Active Directory Connect의 도입으로 이러한 앱에 대한 액세스를 제공하고 클라우드로 전환하는 것이 어느 때보다 쉬워졌습니다. Azure AD Connect는 다음과 같은 이점을 제공합니다.

- 사용자가 클라우드와 온-프레미스 모두에서 공통 ID로 로그온할 수 있습니다. 여러 암호 또는 계정을 기억할 필요가 없으며, 관리자는 여러 계정으로 인해 발생할 수 있는 추가 오버 헤드를 걱정할 필요가 없습니다.
- 온-프레미스 디렉터리를 Azure Active Directory와 연결하는 데 사용되는 단일 도구 및 단계별 환경. 설치하면 마법사가 동기화 서비스, 암호 동기화 또는 AD FS 등의 디렉터리 통합을 실행하고 Azure AD PowerShell 모듈과 같은 필수 구성 요소를 얻는 데 필요한 모든 구성 요소를 배포 및 구성합니다.



<center>![Azure AD Connect 정의](./media/active-directory-aadconnect/azuread.png)</center>




## Azure AD Connect를 사용하는 이유 

Azure AD와 온-프레미스 디렉터리를 통합하면 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하므로 사용자가 더 생산성을 높일 수 있습니다. 이러한 통합을 통해 사용자와 조직은 다음과 같은 이점을 얻을 수 있습니다.
	
* 조직에서는 사용자에게 Windows Server Active Directory를 활용하여 Azure Active Directory에 연결하는 클라우드 기반 서비스 또는 온-프레미스 간에 공통 하이브리드 ID를 제공할 수 있습니다. 
* 관리자는 응용 프로그램 리소스, 장치 및 사용자 ID, 네트워크 위치, 다단계 인증 등을 기반으로 조건부 액세스를 제공할 수 있습니다.
* 사용자는 Azure AD의 계정을 통해 공통 ID를 활용하여 Office 365, Intune, SaaS 앱 및 타사 응용 프로그램에 액세스할 수 있습니다.  
* 개발자는 온-프레미스 Active Directory 또는 클라우드 기반 응용 프로그램용 Azure에 응용 프로그램을 통합하여 공통 ID 모델을 활용하는 응용 프로그램을 빌드할 수 있습니다.

Azure AD Connect는 이러한 통합을 간편하게 해주며, 온-프레미스 및 클라우드 ID 인프라 관리를 간소화합니다.



----------------------------------------------------------------------------------------------------------
## Azure AD Connect 다운로드

Azure AD Connect를 사용하여 시작하려면 [Azure AD Connect 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771)를 사용하여 최신 버전을 다운로드하면 됩니다.

----------------------------------------------------------------------------------------------------------

## Azure AD Connect 작동 방법


Azure Active Directory Connect는 기본 세 부분으로 구성됩니다. 이러한 주요 부분은 동기화 서비스, 선택적 Active Directory Federation Services 부분, [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)를 사용하여 수행하는 모니터링 부분입니다.


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- 동기화 - 이 부분은 이전에 DirSync 및 AAD 동기화로 릴리스된 구성 요소와 기능으로 이루어집니다. 사용자 및 그룹 생성을 담당하는 부분입니다. 또한 온-프레미스 환경의 사용자 및 그룹에 대한 정보가 클라우드 내의 정보와 일치하도록 만들기도 합니다.
- AD FS - Azure AD Connect의 선택적 부분이며 온-프레미스 AD FS 인프라를 사용하여 하이브리드 환경을 설정하는 데 사용할 수 있습니다. 이 부분은 조직에서 도메인 가입 SSO, AD 로그인 정책 강화, 스마트 카드나 타사 MFA 등이 포함된 복잡한 배포를 처리하는 데 사용할 수 있습니다. SSO 구성에 대한 자세한 내용은 [Single Sign On을 사용한 DirSync](https://msdn.microsoft.com/library/azure/dn441213.aspx)를 참조하세요.
- 상태 모니터링 - AD FS를 사용하는 복잡한 배포의 경우 Azure AD Connect Health는 페더레이션 서버에 대한 강력한 모니터링을 제공하고Azure 포털에서 이 활동을 볼 수 있는 중앙 위치를 제공할 수 있습니다. 자세한 내용은 [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)를 참조하세요.


### Azure AD Connect 지원 구성 요소

다음은 Azure AD Connect를 설정한 서버에 Azure AD Connect가 설치되는 데 필요한 필수 조건 및 지원 구성 요소의 목록입니다. 이 목록은 기본 Express 설치에 해당합니다. 동기화 서비스 설치 페이지에서 다른 SQL Server를 사용하도록 선택하는 경우, 다음 목록에 나온 SQL Server 2012 구성 요소는 설치되지 않습니다.

- Azure AD Connect Azure AD Connector
- Microsoft SQL Server 2012 명령줄 유틸리티
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Windows PowerShell용 Azure Active Directory 모듈
- IT 전문가를 위한 Microsoft Online Services 로그인 도우미
- Microsoft Visual C++ 2013 재배포 패키지






## Azure AD Connect 시작



다음 설명서는 Azure Active Directory Connect를 시작하는 데 유용합니다. 이 문서에서는 Azure AD 연결을 위해 빠른 설치를 사용하여 처리합니다. 사용자 지정 설치에 대한 내용은 [Azure AD 연결을 위한 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요. 디렉터리 동기화에서 Azure AD Connect로 업그레이드에 대한 내용은 [디렉터리 동기화를 Azure Active Directory 연결로 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md)를 참조하세요.


### Azure AD Connect를 설치하기 전에
빠른 설정을 사용하여 Azure AD Connect를 설치하기 전에 필요한 몇 가지 사항이 있습니다.


 
- Azure 구독 또는 [Azure 평가판 구독](http://azure.microsoft.com/pricing/free-trial/) -Azure AD Connect를 사용하지 않고 Azure 포털에 액세스하기 위해서만 필요합니다. PowerShell 또는 Office 365를 사용하는 경우, Azure AD Connect를 사용하여 Azure 구독을 설치할 필요가 없습니다.
- 통합하려는 Azure AD 테넌트에 대한 Azure AD 전역 관리자 계정
- Windows Server 2008 이상에서 사용되는 AD 도메인 컨트롤러 또는 구성원 서버
- 로컬 Active Directory에 대한 엔터프라이즈 관리자 계정
- 선택 사항: 동기화를 확인할 테스트 사용자 계정 

#### Azure AD Connect의 하드웨어 요구 사항
아래 표는 Azure AD Connect 컴퓨터의 최소 요구 사항을 보여 줍니다.

| Active Directory의 개체 수 | CPU | 메모리 | 하드 드라이브 크기 |
| ------------------------------------- | --- | ------ | --------------- |
| 10,000개 미만 | 1.6GHz | 4GB | 70GB |
| 10,000–50,000개 | 1.6GHz | 4GB | 70GB |
| 50,000–100,000개 | 1.6GHz | 16GB | 100GB |
| 처음 사용자용 SQL Server가 필요한 100,000개 이상의 개체| | | |
| 100,000–300,000개 | 1.6GHz | 32GB | 300GB |
| 300,000–600,000개 | 1.6GHz | 32GB | 450GB |
| 600,000개 초과 | 1.6GHz | 32GB | 500GB |




여러 포리스트 또는 페더레이션된 로그인과 같은 사용자 지정 옵션은 [여기](active-directory-aadconnect-get-started-custom.md)에서 추가 요구 사항에 관해 알아보세요.


### Azure AD Connect의 빠른 설치
빠른 설정을 선택하는 것이 기본 옵션이며 가장 일반적인 시나리오 중 하나입니다. 이 설정을 선택하면 Azure AD Connect는 암호 해시 동기화 옵션을 사용하여 동기화를 배포합니다. 단일 포리스트 전용으로, 이 옵션을 사용하면 사용자들이 온-프레미스 암호를 사용하여 클라우드에 로그인할 수 있습니다. 빠른 설치를 사용하면 설치 완료 시 동기화가 자동으로 시작됩니다(선택하지 않은 경우에도). 이 옵션에서는 몇 번의 간략한 클릭만으로 온-프레미스 디렉토리를 클라우드로 확장할 수 있습니다.

<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)</center>

#### 빠른 설치를 사용하여 Azure AD Connect를 설치하려면
--------------------------------------------------------------------------------------------

1. Azure AD Connect를 설치하려는 서버에 엔터프라이즈 관리자로 로그인합니다. 동기화 서버로 설정할 서버여야 합니다.
2. AzureADConnect.msi를 찾아 두 번 클릭합니다.
3. 시작 화면에서 사용권 계약에 동의하는 상자를 선택하고 **계속**을 클릭합니다.
4. 빠른 설치 화면에서 **빠른 설치 사용**을 클릭합니다.
<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)</center>
6. Azure AD 화면에 연결에서 Azure AD에 대한 Azure 전역 관리자의 사용자 이름 및 암호를 입력합니다. **다음**을 클릭합니다.
8. AD DS 화면에 연결에서 엔터프라이즈 관리자 계정에 대한 사용자 이름 및 암호를 입력합니다. **다음**을 클릭합니다.
<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/install4.png)</center>
9. 구성 준비 화면에서 **설치**를 클릭합니다.
	- 선택적으로 구성 준비 페이지에서 "**구성이 완료되자마자 동기화 프로세스를 시작합니다.**"의 선택을 취소할 수 있습니다. 이 작업을 수행하는 경우 마법사는 동기화를 구성하지만 작업을 비활성화 상태로 두어 작업 스케줄러에서 수동으로 사용할 때까지 실행되지 않습니다. 작업을 사용하도록 설정하면 3시간마다 동기화가 실행됩니다.
	- 또한 필요에 따라 해당 확인란을 선택하여 **Exchange 하이브리드 배포**에 대한 동기화 서비스를 구성하도록 선택할 수 있습니다. 클라우드 및 온-프레미스 모두에 Exchange 사서함이 없게 하려는 경우, 필요하지 않습니다.

<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)</center>
8. 설치가 완료되면 **끝내기**를 클릭합니다.


<br> <br>

빠른 설치 사용에 관한 비디오의 경우 다음을 확인합니다.

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>



### 설치 확인

Azure AD Connect를 성공적으로 설치한 후 Azure 포털에 로그인하여 마지막 동기화 시간을 확인하여 동기화가 수행되는 것을 확인할 수 있습니다.

1.  Azure 포털에 로그인합니다.
2.  왼쪽 창에서 Active Directory를 선택합니다.
3.  Azure AD Connect를 설정하는데 사용되는 디렉토리를 두 번 클릭합니다.
4.  위쪽에서 디렉토리 통합을 선택합니다. 마지막 동기화 시간을 참고합니다.

<center>![Express Installation](./media/active-directory-aadconnect-get-started/verify.png)</center>

## Azure AD Connect 관리 



다음은 조직 요구 사항 및 요건에 부합하도록 Azure Active Directory Connect를 사용자 지정할 수 있는 고급 운영에 대해 설명합니다.

### Azure AD Premium 및 엔터프라이즈 이동성 사용자에게 라이선스 할당

이제 사용자가 클라우드로 동기화되었으므로, Office 365와 같은 클라우드 앱으로 작업할 수 있도록 라이선스를 할당해야 합니다.

#### Azure AD Premium 또는 엔터프라이즈 이동성 제품군 라이선스를 할당하려면
--------------------------------------------------------------------------------
1. 관리자 권한으로 Azure 포털에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. Active Directory 페이지에서 사용하도록 설정하려는 사용자가 있는 디렉토리를 두 번 클릭합니다.
4. 디렉터리 페이지의 맨 위에서 **라이선스**를 선택합니다.
5. 라이선스 페이지에서 Active Directory Premium 또는 Enterprise Mobility Suite를 선택한 후 **할당**을 클릭합니다.
6. 대화 상자에서 라이선스를 할당하려는 사용자를 선택 하고 확인 표시 아이콘을 클릭하여 변경 내용을 저장합니다.


### 예약된 동기화 작업 확인
동기화의 상태를 확인하려는 경우 Azure 포털에서 선택하여 수행할 수 있습니다.

#### 예약된 동기화 작업을 확인하려면
--------------------------------------------------------------------------------

1. 관리자 권한으로 Azure 포털에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. Active Directory 페이지에서 사용하도록 설정하려는 사용자가 있는 디렉토리를 두 번 클릭합니다.
4. 디렉토리 페이지의 맨 위에서 **디렉토리 통합**을 선택합니다.
5. 로컬 활성 디렉토리로 통합하여 마지막 동기화 시간을 참고합니다.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

### 예약된 동기화 작업 시작
동기화 작업을 실행해야하는 경우, Azure AD Connect 마법사를 다시 실행하여 이를 수행할 수 있습니다. Azure AD 자격 증명을 제공해야 합니다. 마법사에서 **동기화 옵션 사용자 지정** 작업을 선택하고 마법사를 통해 다음을 클릭합니다. 끝에서 **구성이 완료되자마자 동기화 프로세스를 시작합니다.**가 선택한 상태인지 확인합니다.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>




### Azure AD Connect에서 사용할 수 있는 추가 작업
Azure AD Connect의 초기 설치 후, Azure AD Connect 시작 페이지 또는 바탕 화면 바로 가기에서 항상 마법사를 다시 시작할 수 있습니다. 마법사를 다시 실행하면 몇가지 새로운 옵션이 추가 작업의 형태로 제공됩니다.

다음 테이블에서는 각각에 대해 간략한 설명과 이 작업에 대한 요약을 제공합니다.

<center>![Join Rule](./media/active-directory-aadconnect-whats-next/addtasks.png) </center>

추가 작업 | 설명 
------------- | ------------- |
선택한 시나리오 보기 |현재 Azure AD Connect 솔루션을 볼 수 있습니다. 일반 설정, 동기화된 디렉토리, 동기화 설정 등을 포함합니다.
동기화 옵션 사용자 지정 | 추가 Active Directory 포리스트를 구성에 추가 또는 사용자, 그룹, 장치나 암호 쓰기 저장과 같은 동기화 옵션 사용을 포함한 현재 구성을 변경할 수 있습니다.
스테이징 모드 사용 | 이 모드를 사용하면 나중에 동기화되는 스정보를 스테이징할 수 있지만 Azure AD 또는 Active Directory로 내보내지지 않습니다. 이렇게 하면 발생하기 전에 동기화를 미리볼 수 있습니다.


 
### 추가 설명서
Azure AD Connect 작업에 대한 추가 설명서는 다음을 참조하세요.

- [Azure AD Connect 기본 구성 변경](active-directory-aadconnect-whats-next-change-default-config.md)
- [Azure AD Connect 동기화 규칙 편집기 사용](active-directory-aadconnect-whats-next-synch-rules-editor.md)
- [선언적 프로비저닝 사용](active-directory-aadconnect-whats-next-declarative-prov.md)

Azure AD Sync용으로 만들어진 설명서 중 일부는 Azure AD Connect에도 관련되며 적용됩니다. 이 설명서를 Azure.com에서도 볼 수 있도록 최선의 노력을 하고 있지만 이 설명서 일부는 여전히 MSDN의 범위 지정된 라이브러리에 있습니다. 추가 설명서를 보려면 [MSDN의 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 및 [MSDN의 Azure AD Sync](https://msdn.microsoft.com/library/azure/dn790204.aspx)를 참조하세요.


**추가 리소스**



클라우드로 온-프레미스 디렉토리 확장에 2015 프레젠테이션을 ignite합니다.

[AZURE.VIDEO microsoft-ignite-2015-extending-on-premises-directories-to-the-cloud-made-easy-with-azure-active-directory-connect]

[Single Sign On을 사용한 다중 포리스트 디렉터리 동기화 시나리오](https://msdn.microsoft.com/library/azure/dn510976.aspx) - Azure AD와 여러 디렉터리를 통합합니다.

[Azure AD Connect Health](active-directory-aadconnect-health.md) - 온-프레미스 AD FS 인프라의 상태를 모니터링합니다.






 

<!---HONumber=July15_HO4-->