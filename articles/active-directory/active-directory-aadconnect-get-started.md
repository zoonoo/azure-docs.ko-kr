<properties 
	pageTitle="Azure AD Connect 시작" 
	description="Azure AD Connect용 설치 마법사를 다운로드, 설치 및 실행하는 방법을 알아봅니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="azure-active-directory-connect" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Azure AD Connect 시작


<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/documentation/articles/active-directory-aadconnect/" title="정의" class="current">정의</a> <a href="/ko-kr/documentation/articles/active-directory-aadconnect-how-it-works/" title="작동 방식">작동 방식</a> <a href="/ko-kr/documentation/articles/active-directory-aadconnect-get-started/" title="시작">시작</a> <a href="/ko-kr/documentation/articles/active-directory-aadconnect-whats-next/" title="다음 단계">다음 단계</a> <a href="/ko-kr/documentation/articles/active-directory-aadconnect-learn-more/" title="자세한 정보">자세한 정보</a>
</div>

다음 설명서는 Azure Active Directory Connect를 시작하는 데 유용합니다.

## Azure AD Connect 다운로드



Azure AD Connect를 사용하여 시작하려면 다음을 사용하여 최신 버전을 다운로드하면 됩니다. [Azure AD Connect 공개 미리 보기 다운로드](http://connect.microsoft.com/site1164/program8612)

## Azure AD Connect를 설치하기 전에
Azure AD Connect를 설치하기 전에 필요한 몇 가지 사항이 있습니다.

- Azure 구독 또는 [Azure 평가판 구독](http://azure.microsoft.com/pricing/free-trial/)
- Azure AD Premium 또는 [Azure AD Premium 평가판](http://aka.ms/aadptrial)
- 통합하려는 Azure AD 테넌트에 대한 Azure AD 전역 관리자 계정
- Windows Server 2008 이상에서 사용되는 AD 도메인 컨트롤러 또는 구성원 서버
- 로컬 Active Directory에 대한 엔터프라이즈 관리자 계정
- 선택 사항: 동기화를 확인할 테스트 사용자 계정 

AD FS 옵션과 함께 SSO를 사용하려는 경우에는 다음도 필요합니다.

-  원하는 페더레이션 서버에 대한 로컬 관리자 자격 증명
- 웹 응용 프로그램 프록시 역할을 배포하려는 임의의 작업 그룹(도메인에 가입되지 않음) 서버에 대한 로컬 관리자 자격 증명
- 페더레이션 서버용 Windows Server 2012 R2 서버
- 웹 응용 프로그램 프록시용 Windows Server 2012 R2 서버
-  원하는 페더레이션 서비스 이름(예: fs.contoso.com)에 대한 하나의 SSL 인증서가 있는 .pfx 파일
- 마법사를 실행하는 컴퓨터는 Windows 원격 관리를 통해 AD FS나 웹 응용 프로그램 프록시를 설치하려는 다른 컴퓨터에 연결할 수 있어야 합니다.


## Azure AD Connect 설치

Azure AD Connect를 다운로드한 후에는 다음 단계를 사용하여 Azure AD Connect를 설치합니다.

1. Azure AD Connect를 설치하려는 서버에 엔터프라이즈 관리자로 로그인합니다.
2. AzureADConnect.msi를 찾아 두 번 클릭합니다.
3. 빠른 설정 또는 사용자 지정 설정을 사용하여 마법사의 단계를 따릅니다.
4. 선택 사항: 테스트 사용자 계정을 사용하여 Office 365와 같은 클라우드 서비스에 로그인합니다.

<center>![Azure AD Connect 시작](./media/active-directory-aadconnect-get-started/aadConnect_Welcome.png)</center>

## 동기화 서비스 선택적 구성
동기화 서비스를 설치할 때는 선택적 구성 섹션을 선택 취소된 채로 두면 됩니다. 그러면 Azure AD Connect에서 모든 사항이 자동 설정됩니다. SQL Server 2012 Express 설정, 적절한 그룹 만들기, 그룹에 권한 할당 등이 자동으로 설정됩니다. 기본값을 변경하려는 경우 다음 표를 참조하여 사용할 수 있는 선택적 구성 옵션을 이해할 수 있습니다.

선택적 구성 | 설명 
------------- | ------------- |
SQL Server 이름 |SQL Server 이름 및 인스턴스 이름을 지정할 수 있습니다. 사용하려는 AD 데이터베이스 서버가 이미 있는 경우 이 옵션을 선택합니다.
서비스 계정 |기본적으로 Azure AD Connect는 사용할 동기화 서비스에 대한 서비스 계정을 만듭니다. 발생하는 문제는 암호가 자동으로 생성되어 Azure AD Connect를 설치하는 사람이 암호를 알 수 없다는 점입니다. 이는 대부분의 시나리오에서 무방합니다. 그러나 동기화되는 부서의 범위를 지정하는 등의 고급 구성을 수행하려는 경우에는 계정을 만들고 고유 암호를 선택하고자 합니다. |
권한 | 동기화 서비스가 설치되면 기본적으로 Azure AD Connect에서는 4개의 그룹을 만들어집니다. 이 그룹은 Administrators 그룹, Operators 그룹, Browse 그룹, Password Reset 그룹입니다. 고유한 그룹을 지정하려면 여기서 지정하면 됩니다.
설정 가져오기 |Azure AD Sync의 DirSync에서 구성 정보를 가져오는 경우 이 옵션을 사용합니다.|



## 빠른 설치
빠른 설정을 선택하는 것이 기본 옵션이며 가장 일반적인 시나리오 중 하나입니다. 이 설정을 선택하면 Azure AD Connect는 암호 해시 동기화 옵션을 사용하여 동기화를 배포합니다. 단일 포리스트 전용으로, 이 옵션을 사용하면 사용자들이 온-프레미스 암호를 사용하여 클라우드에 로그인할 수 있습니다. 빠른 설치를 사용하면 설치 완료 시 동기화가 자동으로 시작됩니다. 이 옵션에서는 6번의 간략한 클릭만으로 온-프레미스 디렉터리를 클라우드로 확장할 수 있습니다.

<center>![빠른 설치](./media/active-directory-aadconnect-get-started/express.png)</center>

## 사용자 지정 설치

사용자 지정 설치에서는 다양한 옵션을 선택할 수 있습니다. 다음 표에서는 사용자 지정 설치 옵션을 선택하는 경우 사용할 수 있는 마법사 페이지에 대해 설명합니다.

페이지 이름 | 설명
-------------------    | ------------- | 
사용자 로그인|이 페이지에서는 암호 동기화를 사용할지, AD FS로 페더레이션을 사용할지 아니면 둘 중 어느 것도 사용하지 않을지 선택할 수 있습니다.
디렉터리에 연결|이 페이지에서는 동기화하려는 하나 이상의 디렉터리를 추가할 수 있습니다.
동기화 필터링| 여기서는 모든 사용자 및 그룹을 동기화할지 아니면 디렉터리당 하나의 그룹을 지정해서 해당 그룹만 동기화할지 결정할 수 있습니다.
온-프레미스 ID|여기서는 사용자가 Connect에서 디렉터리 페이지에 추가된 모든 디렉터리에 한 번 존재하는지 아니면 사용자가 여러 디렉터리에 존재하는지 지정할 수 있습니다. 사용자가 여러 디렉터리에 존재하는 경우 디렉터리에서 이러한 사용자를 고유하게 식별하는 특성을 선택해야 합니다. 예를 들어 메일 특성 ObjectSID 또는 SAMAccountName은 모두 사용자를 고유하게 식별하는 데 사용되는 공통 특성입니다.
Azure ID|이 페이지에서는 ID 페더레이션에 사용할 원본 앵커를 지정합니다.
옵션 기능|다음 표에서 선택할 수 있는 옵션 기능에 대한 간략한 설명을 확인할 수 있습니다.

<center>![빠른 설치](./media/active-directory-aadconnect-get-started/of.png)</center>


선택적 기능 | 설명
-------------------    | ------------- | 
Exchange 하이브리드 배포 |Exchange 하이브리드 배포 기능을 사용하면Azure AD에서 특정 특성 집합을 온-프레미스 디렉터리에 다시 동기화하여 Exchange 사서함이 온-프레미스와 Azure에서 공존할 수 있습니다.
Azure AD 앱 및 특성 필터링|Azure AD 앱 및 특성 필터링을 사용하도록 설정하면 동기화된 특성 집합을 마법사의 후속 페이지에 나오는 특정 집합에 맞게 지정할 수 있습니다. 마법사에서 두 개의 추가 구성 페이지가 열립니다.  
암호 쓰기 저장|암호 쓰기 저장을 사용하도록 설정하면 Azure AD에서 이루어지는 암호 변경 사항이 온-프레미스 디렉터리에 다시 기록됩니다.
사용자 쓰기 저장|사용자 쓰기 저장을 사용하도록 설정하면 Azure AD에서 만들어진 사용자가 온-프레미스 디렉터리에 다시 기록됩니다. 이 마법사에서 추가 구성 페이지가 열립니다.  
장치 동기화|장치 동기화를 사용하도록 설정하면 Azure AD에서 장치 구성을 기록할 수 있습니다.
디렉터리 확장 특성 동기화|디렉터리 확장 특성 동기화를 사용하도록 설정하면 지정된 특성이 Azure AD에 동기화됩니다. 이 마법사에서 추가 구성 페이지가 열립니다.  

기본 구성을 변경하거나 동기화 규칙 편집기 및 선언적 프로비저닝을 사용하는 등의 추가 구성 옵션에 대한 자세한 내용은 [Azure AD Connect 관리](active-directory-aadconnect-whats-next.md)를 참조하세요.

## Azure AD Connect 지원 구성 요소

다음은 Azure AD Connect를 설정한 서버에 Azure AD Connect가 설치되는 데 필요한 필수 조건 및 지원 구성 요소의 목록입니다. 이 목록은 기본 Express 설치에 해당합니다. 동기화 서비스 설치 페이지에서 다른 SQL Server를 사용하도록 선택하는 경우, 다음 목록에 나온 SQL Server 2012 구성 요소는 설치되지 않습니다.

- Forefront Identity Manager Azure Active Directory Connector
- Microsoft SQL Server 2012 명령줄 유틸리티
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Windows PowerShell용 Azure Active Directory 모듈
- IT 전문가를 위한 Microsoft Online Services 로그인 도우미
- Microsoft Visual C++ 2013 재배포 패키지


**추가 리소스**

* [클라우드에서 온-프레미스 ID 인프라 사용](active-directory-aadconnect.md)
* [Azure AD Connect 작동 방법](active-directory-aadconnect-how-it-works.md)
* [Azure AD Connect의 다음 단계](active-directory-aadconnect-whats-next.md)
* [자세한 정보](active-directory-aadconnect-learn-more.md)
* [MSDN의 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58--> 