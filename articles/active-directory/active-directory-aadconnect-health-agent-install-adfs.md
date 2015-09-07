<properties 
	pageTitle="Azure AD Connect Health AD FS 에이전트 설치| Microsoft Azure"
	description="AD FS(Active Directory Federation Services) 에이전트 설치를 설명하는 Azure AD Connect Health 페이지입니다."
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
	ms.date="08/14/2015"
	ms.author="billmath"/>






# AD FS에 대한 Azure AD Connect Health 에이전트 설치

이 문서는 서버에 AD FS에 대한 Azure AD Connect Health 에이전트를 설치하고 구성하는 방법을 설명합니다.

>[AZURE.NOTE]Azure AD Connect Health 인스턴스에 데이터를 표시하려면 대상 서버에 Azure AD Connect Health Agent를 설치해야 합니다. 에이전트를 설치하기 전에 [여기](active-directory-aadconnect-health.md#requirements)에서 요구 사항을 완료해야 합니다. [여기](http://go.microsoft.com/fwlink/?LinkID=518973)에서 에이전트를 다운로드할 수 있습니다.


## 에이전트 설치
에이전트 설치를 시작하려면 다운로드한 .exe 파일을 두 번 클릭합니다. 첫 번째 화면에서 설치를 클릭합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install1.png)

설치가 완료되면 지금 구성을 클릭합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install2.png)

명령 프롬프트가 시작된 다음 PowerShell에서 Register-AzureADConnectHealthADFSAgent를 실행합니다. Azure에 로그인하라는 메시지가 표시됩니다. 로그인을 진행합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install3.png)


로그인한 후 PowerShell이 계속됩니다. PowerShell이 완료되면 닫을 수 있으며 구성이 완료됩니다.

이제 서비스가 자동으로 시작되고 에이전트는 데이터를 모니터링하고 수집합니다. 이전 섹션에 설명된 필수 조건이 모두 충족되지 않으면 PowerShell 창에 경고가 표시됩니다. 에이전트를 설치하기 전에 [여기](active-directory-aadconnect-health.md#requirements)에서 요구 사항을 완료해야 합니다. 다음 스크린 샷은 이러한 오류의 예입니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install4.png)

에이전트가 설치되었는지 확인하려면 서비스를 열고 다음 사항을 확인합니다. 구성을 완료한 경우 다음 서비스가 실행되고 있어야 합니다. 그렇지 않은 경우 구성이 완료될 때까지 서비스가 시작되지 않습니다.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service
 
![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install5.png)


## Windows Server 2008 R2 서버에서 에이전트 설치

Windows Server 2008 R2 서버에 대해 다음을 수행합니다.

1. 서버가 서비스 팩 1 이상에서 실행되고 있는지 확인합니다.
1. 에이전트 설치에 대해 IE ESC를 끕니다.
1. AD Health Agent를 설치하기 전에 각 서버에서 Windows PowerShell 4.0을 설치합니다. Windows PowerShell 4.0을 설치하려면
 - 오프라인 설치 관리자를 다운로드하려면 다음 링크를 사용하여 [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779)를 설치합니다.
 - Windows 기능에서 PowerShell ISE를 설치합니다.
 - [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)을 설치합니다.
 - 서버에서 Internet Explorer 버전 10 이상을 설치합니다. Health Service에서 Azure 관리자 자격 증명을 사용하여 인증하는 데 필요합니다.
1. Windows Server 2008 R2에서 Windows PowerShell 4.0을 설치하는 방법에 대한 자세한 내용은 [여기](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)에서 wiki 문서를 참조하세요.

## 관련 링크

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 작업](active-directory-aadconnect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health FAQ](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->