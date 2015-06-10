<properties 
	pageTitle="Azure AD 용어" 
	description="Azure AD와 관련된 용어 및 정의입니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Azure AD 용어

Microsoft Azure Active Directory(Azure AD)에는 클라우드, 하이브리드 및 온-프레미스 시나리오와 관련된 고유한 용어 집합이 있습니다. 다음 표는 이러한 용어의 사용에 대한 기본적인 이해를 제공하기 위해 용어를 정의합니다.

 용어 | 정의
------------- | -------------
추가 보안 확인 | Azure Active Directory 인증 시스템에 해당 ID를 확인할 때 사용자 암호와 전화 응답 둘 다를 사용하도록 전역 관리자가 조직 디렉터리의 사용자 계정에 설정할 수 있는 보안 설정입니다.
Azure Active Directory | REST 기반 API를 통해 ID 관리 및 액세스 제어 기능을 제공하는 Azure의 ID 서비스입니다.
Azure Active Directory 액세스 제어 | 페더레이션 인증 및 REST 웹 서비스에 대한 규칙 기준, 클레임 기준 인증을 제공하는 Azure 서비스입니다.
Azure Active Directory 인증 시스템 | 회사 또는 학교 계정을 인증하고 권한을 부여하는 데 사용되는 Microsoft의 클라우드 ID 서비스입니다.
Azure Active Directory Graph | 사용자 정보 및 관계를 쉽게 표시하기 위해 소셜 엔터프라이즈 그래프 내의 사용자, 그룹 및 역할 개체에 액세스하는 Azure Active Directory의 기능입니다.
Windows PowerShell용 Azure Active Directory 모듈 | Azure Active Directory를 관리하는 데 사용되는 cmdlet 그룹입니다. 이러한 cmdlet을 사용하여 사용자, 그룹, 도메인, 클라우드 서비스 구독, 라이선스, 디렉터리 동기화, Single Sign-On 등을 관리할 수 있습니다.
Azure Active Directory 연결 | Azure Active Directory 연결 마법사는 온-프레미스 디렉터리를 Azure Active Directory와 연결하는 데 사용되는 단일 도구 및 단계별 환경입니다. 이 마법사는 동기화 서비스, 암호 동기화 또는 AD FS(Active Directory Federation Services) 등의 디렉터리 통합을 실행하고 Azure AD PowerShell 모듈과 같은 필수 조건을 얻는 데 필요한 모든 구성 요소를 배포 및 구성합니다.
Azure Active Directory 동기화 도구 | 회사의 온-프레미스 Active Directory 서비스에서 Azure Active Directory로 디렉터리 개체를 단방향으로 동기화하는 응용 프로그램입니다.
디렉터리 통합 | 온-프레미스 디렉터리와 클라우드 디렉터리에서의 ID 유지 관리와 관련된 관리 환경을 개선하기 위해 설정할 수 있는 Azure Active Directory의 기능입니다. 디렉터리 통합 시나리오는 디렉터리 동기화 및 Single Sign-On을 통한 디렉터리 동기화를 포함합니다.
디렉터리 동기화 | 관리 오버헤드를 줄이기 위해 온-프레미스 디렉터리 개체(사용자, 그룹, 연락처)를 클라우드로 동기화하는 데 사용됩니다. Azure AD 포털 및 Azure 관리 포털에서 이러한 개체를 동기화하는 작업이 디렉터리 동기화입니다. 관리자는 디렉터리 동기화를 설정한 후 온-프레미스에서 Active Directory의 디렉터리 개체를 Azure AD 인스턴스로 프로비전할 수 있습니다.
Microsoft Online Services 로그인 도우미 | 로그인 도우미는 클라이언트 컴퓨터에 설치되며 한 번 로그인한 다음 로그인 세션 동안 서비스에 원하는 만큼 여러 번 액세스할 수 있게 하는 응용 프로그램입니다. 로그인 도우미가 없는 경우에는 최종 사용자가 서비스에 액세스하려고 할 때마다 이름과 암호를 입력해야 합니다. 로그인 도우미를 Single Sign-On과 혼동해서는 안 됩니다. Single Sign-On은 Microsoft 클라우드 서비스에 원활하게 액세스하기 위해 사용자의 기존 온-프레미스 회사 자격 증명을 활용하도록 배포할 수 있는 Azure Active Directory의 디렉터리 통합 기능입니다.
다단계 인증(2단계 인증 또는 2FA라고도 함) | 다단계 인증은 사용자 로그인 및 트랜잭션에 중요한 제2의 보안 계층을 추가합니다. Azure AD의 사용자 계정에 다단계 인증을 사용하도록 설정하면 해당 사용자는 조직에서 구독하는 Microsoft 클라우드 서비스에 로그인하고 사용할 때마다 추가 보안 확인 방법으로서 표준 암호 자격 증명 외에 휴대폰도 사용해야 합니다.
Single Sign-On | 회사 네트워크에 로그온한 동안 Microsoft 클라우드 서비스에 액세스할 때 사용자에게 보다 원활한 인증 환경을 제공하기 위해 사용됩니다. Single Sign-On을 설정하려는 조직은 온-프레미스에서 보안 토큰 서비스를 배포해야 합니다. Single Sign-On이 설정되고 나면 사용자는 Active Directory 회사 자격 증명(사용자 이름 및 암호)을 사용하여 클라우드의 서비스 및 기존 온-프레미스 리소스에 액세스할 수 있습니다.
사용자 ID | 사용자 ID는 사용자가 조직에서 구독하는 Microsoft 클라우드 서비스에 액세스하기 위해 로그인 페이지에 제공하는 고유 식별자입니다.
회사 또는 학교 계정 | 조직(회사, 학교, 비영리 단체)이 자신의 구성 요소(직원, 학생, 고객) 중 하나에 할당하는 사용자 계정으로서, 조직의 하나 이상의 Microsoft 클라우드 서비스(Office 365 또는 Azure 등) 구독에 로그인 액세스를 제공합니다. 이러한 계정은 조직의 Azure AD 디렉터리에 저장되며, 사용자가 조직을 떠날 때 일반적으로 삭제됩니다. 회사 또는 학교 계정은 사용자가 아닌 회사의 관리자가 만들고 관리하는 Microsoft 계정과는 다릅니다. 

## 다음 단계
- [조직으로 Azure에 등록](sign-up-organization.md)
- [Azure 구독과 Azure AD의 연관 관계](active-directory-how-subscriptions-associated-directory.md)
- [Azure AD 서비스 제한 및 제한 사항](active-directory-service-limits-restrictions.md)

<!---HONumber=58-->