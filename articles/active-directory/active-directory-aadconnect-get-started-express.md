<properties
	pageTitle="Azure AD Connect: 기본 설정을 사용하여 시작 | Microsoft Azure"
	description="Azure AD Connect용 설치 마법사를 다운로드, 설치 및 실행하는 방법을 알아봅니다."
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
	ms.topic="get-started-article"
	ms.date="02/18/2016"
	ms.author="billmath;andkjell"/>

# 기본 설정을 사용하여 Azure AD Connect 시작
다음 설명서는 Azure Active Directory Connect를 시작하는 데 유용합니다. 이 문서에서는 Azure AD 연결을 위해 빠른 설치를 사용하여 처리합니다.

## 관련 설명서
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대한 설명서를 읽지 않은 경우 다음 표에서 관련 항목에 대한 링크를 제공합니다. 설치를 시작하려면 굵게 표시된 처음 두 항목이 필요합니다.

| 항목 | |
| --------- | --------- |
| **Azure AD Connect 다운로드** | [Azure AD Connect 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **하드웨어 및 필수 조건** | [Azure AD Connect: 하드웨어 및 필수 조건](active-directory-aadconnect-prerequisites.md) |
| 사용자 지정된 설정을 사용하여 설치 | [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md) |
| DirSync에서 업그레이드 | [Azure AD Sync 도구(DirSync)에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| 설치 후 | [설치 확인 및 라이선스 할당](active-directory-aadconnect-whats-next.md) |
| 설치에 사용되는 계정 | [Azure AD Connect 계정 및 사용 권한에 대한 추가 정보](active-directory-aadconnect-accounts-permissions.md) |


## Azure AD Connect의 빠른 설치
빠른 설정을 선택하는 것이 기본 옵션이며 가장 일반적인 시나리오 중 하나입니다. 이 설정을 선택하면 Azure AD Connect는 암호 동기화 옵션을 사용하여 동기화를 배포합니다. 단일 포리스트 전용으로, 이 옵션을 사용하면 사용자들이 온-프레미스 암호를 사용하여 클라우드에 로그인할 수 있습니다. 빠른 설치를 사용하면 설치 완료 시 동기화가 자동으로 시작됩니다(선택하지 않은 경우에도). 이 옵션에서는 몇 번의 간략한 클릭만으로 온-프레미스 디렉토리를 클라우드로 확장할 수 있습니다.

![Azure AD Connect 시작](./media/active-directory-aadconnect-get-started-express/welcome.png)

### 빠른 설치를 사용하여 Azure AD Connect를 설치하려면

1. Azure AD Connect를 설치하려는 서버에 로컬 관리자로 로그인합니다. 동기화 서버로 설정할 서버여야 합니다.
2. AzureADConnect.msi를 찾아 두 번 클릭합니다.
3. 시작 화면에서 사용권 계약에 동의하는 상자를 선택하고 **계속**을 클릭합니다.
4. 기본 설정 화면에서 **Use express settings**(기본 설정 사용)를 클릭합니다. ![Azure AD Connect 시작](./media/active-directory-aadconnect-get-started-express/express.png)
5. Azure AD 화면에 연결에서 Azure AD에 대한 Azure 전역 관리자의 사용자 이름 및 암호를 입력합니다. **다음**을 클릭합니다. ![AAD에 연결](./media/active-directory-aadconnect-get-started-express/connectaad.png) 오류가 발생하고 연결에 문제가 있는 경우 [연결 문제 해결](active-directory-aadconnect-troubleshoot-connectivity.md)을 참조하세요.
6. AD DS 화면에 연결에서 엔터프라이즈 관리자 계정에 대한 사용자 이름 및 암호를 입력합니다. **다음**을 클릭합니다. ![Azure AD Connect 시작](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. 구성 준비 화면에서 **설치**를 클릭합니다.
	- 선택적으로 구성 준비 페이지에서 **구성이 완료되자마자 동기화 프로세스를 시작합니다.** 확인란을 선택 취소할 수 있습니다. 이 작업을 수행하는 경우 마법사는 동기화를 구성하지만 작업을 비활성화 상태로 두어 작업 스케줄러에서 수동으로 사용할 때까지 실행되지 않습니다. 작업을 사용하도록 설정하면 30분마다 동기화가 실행됩니다.
	- 또한 필요에 따라 해당 확인란을 선택하여 **Exchange 하이브리드 배포**에 대한 동기화 서비스를 구성하도록 선택할 수 있습니다. 클라우드 및 온-프레미스 모두에 Exchange 사서함이 없게 하려는 경우, 필요하지 않습니다. ![Azure AD Connect 시작](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
8. 설치가 완료되면 **끝내기**를 클릭합니다.
9. 설치가 완료된 후 로그아웃하고 동기화 서비스 관리자 또는 동기화 규칙 편집기를 사용하기 전에 다시 로그인합니다.

빠른 설치 사용에 관한 비디오의 경우 다음을 확인합니다.

[AZURE.VIDEO azure-active-directory-connect-express-settings]

## 다음 단계
Azure AD Connect를 설치했으므로 [설치를 확인하고 라이선스를 할당](active-directory-aadconnect-whats-next.md)할 수 있습니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0224_2016-->