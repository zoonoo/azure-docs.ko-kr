<properties 
	pageTitle="Azure AD Connect - Windows 원격 관리 힌트" 
	description="AD FS와 함께 사용하기 위한 Azure AD Connect Windows Remote Managed 힌트" 
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
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect - Windows 원격 관리 힌트


Azure AD Connect를 사용하여 웹 응용 프로그램 프록시 또는 Active Directory Federation Services를 배포하고 아래 힌트를 확인하여 연결 및 구성이 성공하도록 합니다.

- 대상 서버가 도메인에 가입된 경우 Windows 원격 관리가 사용되는지 확인합니다. 
	* 관리자 권한 PSH 명령 창에서 "Enable-psremoting-force" 명령을 사용합니다. 

- 대상 서버가 도메인에 연결된 WAP 컴퓨터가 아닌 경우, 몇가지 추가 요구 사항이 있습니다.
	- 대상 컴퓨터(WAP 컴퓨터):” 

- Winrm(Windows Remote Management / WS-Management) 서비스가 서비스 스냅인을 통해 실행되는지 확인합니다.

- 관리자 권한 PSH 명령 창에서 "Enable-psremoting-force" 명령을 사용합니다.
	- 마법사를 실행 중인 컴퓨터(대상 컴퓨터가 도메인에 가입되지 않거나 신뢰할 수 없는 도메인인 경우): 

- 관리자 권한 PSH 명령 창에서 “Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate” 명령을 사용합니다.
	- 서버 관리자:
		- DMZ WAP 호스트를 컴퓨터 풀에 추가(서버 관리자 -> 관리 -> 서버 추가...DNS 탭 사용) 
		- 서버 관리자 모든 서버 탭: WAP 서버를 마우스 오른쪽 단추로 클릭하고 다음으로 관리...를 선택하고, WAP 컴퓨터에 대한 로컬(도메인이 아닌) 자격 증명을 입력합니다. 
		- 서버 관리자 모든 서버 탭에서 원격 PSH 연결 유효성을 검사하려면, WAP 서버를 마우스 오른쪽 단추로 클릭하고 Windows PowerShell을 선택합니다. 원격 PowerShell 세션을 설정할 수 있도록 원격 PSH 세션을 열어야 합니다. 

**추가 리소스**


* [Azure AD Connect 계정 및 사용 권한에 대한 추가 정보](active-directory-aadconnect-account-summary.md)
* [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)
* [MSDN의 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=62-->