<properties
	pageTitle="Azure AD Connect 자격 증명 및 권한에 대한 자세한 정보 | Microsoft Azure"
	description="Azure AD Connect 자격 증명 및 사용 권한의 사용자 지정 설정을 설명합니다."
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
	ms.date="10/13/2015"
	ms.author="billmath"/>



# Azure AD Connect 자격 증명 및 권한에 대한 자세한 정보


Azure AD Connect 마법사는 여러가지 고유한 사용 권한 요구 사항과 함께 서로 다른 두 경로를 제공합니다.

* Express 설정에서 사용자를 만들거나 사용 권한을 별도로 구성하지 않고도 구성을 쉽게 설정할 수 있도록 더 많은 권한이 필요합니다.

* 사용자 지정 설정에서 더 많은 선택과 옵션을 제공하지만, 사용자가 올바른 사용 권한이 있는지 확인해야 하는 경우도 있습니다.


## 수집되는 자격 증명 및 빠른 설치에서의 용도

마법사 페이지 | 수집되는 자격 증명 | 필요한 사용 권한| 용도
------------- | ------------- |------------- |------------- |
Azure에 연결| Azure AD 디렉터리 자격 증명 | Azure AD에서 글로벌 관리자 역할 | <li>Azure AD 디렉터리에서 동기화를 사용하도록 설정합니다.</li> <li>Azure AD에서 진행 중인 동기화 작업에 사용할 수 있는 Azure AD 계정을 만드는데 사용됩니다.</li>
AD DS에 연결 | 온-프레미스 Active Directory 자격 증명 | Active Directory의 Enterprise Admins(EA) 그룹의 구성원| 로컬 AD 커넥터 계정으로 사용됩니다. 즉, 동기화에 대한 디렉터리 정보를 읽고 쓰는 계정입니다.
해당 없음|마법사를 실행하는 사용자의 로그온 자격 증명| 로컬 서버의 관리자|마법사는 로컬 컴퓨터에서 동기화 서비스 로그온 계정으로 사용할 수 있는 AD 계정을 만듭니다.

<br> <br>


## 수집되는 자격 증명 및 사용자 지정 설치에서의 용도


마법사 페이지 | 수집되는 자격 증명 | 필요한 사용 권한| 용도
------------- | ------------- |------------- |-------------
해당 없음|마법사를 실행하는 사용자의 로그온 자격 증명|로컬 서버의 관리자| <li>기본적으로 마법사는 로컬 컴퓨터에서 동기화 서비스 로그온 계정으로 사용할 수 있는 AD 계정을 만듭니다</li><li>관리자에서 특정 계정을 지정하지 않으면 동기화 서비스 로그온 계정만 만듭니다</li> <li>DC에서 계정이 도메인 사용자가 아닌 한 계정은 로컬 사용자입니다</li>
동기화 서비스 페이지, 서비스 계정 옵션을 설치합니다. | AD 또는 로컬 사용자 계정 자격 증명 | 로컬 사용자|관리자가 계정을 지정하는 경우, 이 계정은 동기화 서비스에 대한 로그온 계정으로 사용됩니다.
Azure에 연결|Azure AD 디렉터리 자격 증명| Azure AD에서 글로벌 관리자 역할|마법사는 로컬 컴퓨터에서 동기화 서비스 로그온 계정으로 사용할 수 있는 AD 계정을 만듭니다.
디렉터리에 연결|Azure AD에 연결될 각 포리스트의 온-프레미스 Active Directory 자격 증명 |<li>마법사에 필요한 최소 수준의 사용 권한은 도메인 사용자입니다.</li> <li>그러나 지정된 계정은 의도한 시나리오에 필요한 권한이 있어야 합니다.</li><li>Azure AD에 암호 동기화를 구성하려는 경우 이 계정에 할당된 다음 사용 권한을 확인하세요. -디렉터리 복제 변경-모든 디렉터리 변경 복제</li> <li>Azure AD에서 로컬 AD까지 '쓰기 저장' 정보와 동기화를 구성하려는 경우, 쓰기 저장할 디렉터리 개체 및 속성에 대한 쓰기 권한이 계정에 있는지 확인해야 합니다.</li> <li>로그인에 대한 AD FS를 구성하려는 경우, D FS 서버가 있는 포리스트에 대해 제공하는 AD 자격 증명에 도메인 관리자 권한이 있는지 확인합니다.</li><li>사용자 시나리오는 아래 추가 요구사항 목록의 테이블을 참조하세요.</li>|<li>이는 로컬 AD 관리 에이전트(MA) 계정에 사용되는 계정입니다. 진행 중인 동기화 작업에 대한 로컬 AD에서 개체와 특성을 읽고 쓰는 데 사용됩니다.</li>
AD FS 서버|목록의 각 서버에 대해, 마법사를 실행하는 사용자의 로그온 자격 증명이 연결하기에 충분하지 않으면 마법사는 자격 증명을 수집합니다.|도메인 관리자|AD FS 서버 역할 설치 및 구성|
웹 응용 프로그램 프록시 서버 |목록의 각 서버에 대해, 마법사를 실행하는 사용자의 로그온 자격 증명이 연결하기에 충분하지 않으면 마법사는 자격 증명을 수집합니다.|대상 컴퓨터의 로컬 관리자입니다.|WAP 서버 역할 설치 및 구성
프록시 트러스트 자격 증명 |페더레이션 서비스 자격 증명(FS에서 프록시가 신뢰 인증서를 등록하는 데 사용할 자격 증명) |AD FS 서버의 로컬 관리자인 도메인 계정|FS-WAP 신뢰 인증서의 초기 등록
AD FS 서비스 계정 페이지에서 "도메인 사용자 계정 옵션 사용"|AD 사용자 계정 자격 증명|도메인 사용자|해당 자격 증명을 제공하는 AD 사용자 계정이 AD FS 서비스의 로그온 계정으로 사용됩니다.



<br> <br>
## 특정 시나리오에 필요한 사용 권한

시나리오 |사용 권한
------------- | ------------- |
암호 동기화| <li>디렉터리 변경 내용을 복제합니다.</li> <li>디렉터리 변경 내용을 모두 복제합니다.</li>
Exchange 하이브리드 배포|[Office 365 Exchange 하이브리드 AAD 동기화 다시 쓰기 특성 및 사용 권한](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)을 참조하세요.
암호 쓰기 저장 | <li>암호 변경</li><li>암호 재설정</li>
사용자, 그룹 및 장치 쓰기 저장|'다시 쓰기'를 수행하려는 디렉터리 개체 및 특성에 대한 쓰기 권한
SSO(Single Sign-on) 및 AD FS| 페더레이션된 서버에 있는 도메인에 도메인 관리자 권한이 있습니다.
SQL Server 사용 권한|Azure AD Connect 동기화 서비스 계정에는 SQL의 dbo 권한이 필요합니다. 이러한 권한은 Azure AD Connect를 설치하는 동안 설정되며 변경할 수 없습니다.

<br> <br>
## Azure AD Connect에서 만든 계정 요약



만든 계정 |할당된 사용 권한 | 용도
------------- | ------------- |------------- |
동기화를 위한 Azure AD 계정|전용 디렉터리 동기화 역할|진행 중인 동기화 작업(Azure AD MA 계정)
Express 설정: 동기화에 사용되는 AD 계정|동기화 + 암호 동기화에 필요한 디렉터리에 대한 읽기/쓰기 권한|진행 중인 동기화 작업(Azure AD MA 계정)
Express 설정: 서비스 로그온 계정 동기화 | 마법사를 실행하는 사용자의 로그온 자격 증명|서비스 로그온 계정 동기화
사용자 지정 설정: 서비스 로그온 계정 동기화 |해당 없음|서비스 로그온 계정 동기화
AD FS:GMSA 계정(aadcsvc$)|도메인 사용자|FS 서비스 로그온 계정


**추가 리소스**



* [암호 동기화에 대한 사용 권한](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Exchange 하이브리드에 대한 사용 권한](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [암호 쓰기 저장에 대한 사용 권한](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)
* [MSDN의 Azure AD Connect](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->