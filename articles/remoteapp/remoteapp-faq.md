<properties 
	pageTitle="Azure RemoteApp FAQ | Microsoft Azure" 
	description="Azure RemoteApp에 대해 자주 묻는 질문에 대한 대답을 알아봅니다." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/27/2016" 
	ms.author="elizapo"/>

# Azure RemoteApp FAQ
Azure RemoteApp과 관련된 질문은 다음과 같습니다. 다른 질문이 있을 경우 [RemoteApp 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp)을 방문하여 알아야 할 사항을 알려 주거나 아래에 의견을 남겨주세요.

## Azure RemoteApp이란? ##


- **Azure RemoteApp이란?** RemoteApp은 다양한 사용자 장치에서 응용 프로그램에 안전하게 원격으로 액세스할 수 있는 Azure 서비스입니다. [Azure RemoteApp](remoteapp-whatis.md)에 대한 더 알아봅니다.
- **배포 옵션은 무엇인가요?** RemoteApp 컬렉션의 두 가지 종류는 클라우드 및 하이브리드입니다. 도메인 가입을 해야 하는지 여부와 같은 요인에 따라 필요한 것이 달라집니다. [여기](remoteapp-collections.md)에서 이러한 모든 결정에 대해 이야기합니다.

## Azure RemoteApp 사용에 대한 빠른 팁 ##
- **연결이 끊길 때까지 시간이 얼마나 남았나요? 부팅되기 전에 유휴 상태인 시간은 얼마나 되나요?** 4시간입니다. 사용자 또는 사용자 일부가 4시간 동안 유휴 상태인 경우 Azure RemoteApp에서 자동으로 로그아웃됩니다. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에서 기타 기본 설정을 확인합니다.
- **이 서비스를 무료로 사용해 볼 수 있나요?** 예. 30일 동안 사용할 수 있는 무료 평가판이 있습니다. 평가 기간이 끝난 후 유료 계정으로 전환하거나(프로덕션 환경에서 사용할 수 있음) 서비스 사용을 중지할 수 있습니다. [portal.azure.com](http://portal.azure.com)으로 이동하여 무료 평가판을 시작하고 RemoteApp의 새 인스턴스를 만듭니다. 무료 평가판으로 인스턴스당 10명의 사용자와 RemoteApp의 인스턴스를 2개 만들 수 있습니다. 이 평가판은 30일 동안만 사용할 수 있습니다.
## Azure RemoteApp 구독 세부 정보 ##

- **서비스의 제한 사항은 무엇입니까?** [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에서 Azure RemoteApp의 기본 설정 및 서비스 제한 사항에 대해 자세히 알아볼 수 있습니다. 추가 질문이 있으면 알려주세요.
- **얼마나 많은 사용자가 필요합니까?** 최소 20명의 사용자입니다. 분명히 알아두어야 합니다. 최소 20명입니다. 20명에 대한 비용이 청구됩니다.
- **RemoteApp 비용은 얼마인가요?** [Azure RemoteApp 가격 정보](https://azure.microsoft.com/pricing/details/remoteapp/)를 확인하세요.
- **한 가지 형식의 컬렉션이 다른 것 보다 비용이 듭니까?** 예, 컬렉션 요구 사항에 따라 다를 수 있습니다. 하이브리드 컬렉션은 Azure RemoteApp에서 온-프레미스 네트워크까지 연결을 필요로 합니다. 기존 VNET/Express 경로를 사용하면 추가 비용이 없습니다. 하지만 새 Azure VNET 및 게이트웨이 또는 Express 경로를 사용하는 경우 [VPN 게이트웨이](https://azure.microsoft.com/pricing/details/vpn-gateway) 또는 [Express 경로](https://azure.microsoft.com/pricing/details/expressroute/)의 비용이 청구됩니다. 해당 비용(링크에 자세히 설명됨)은 월별 Azure RemoteApp 비용에 우선합니다.

## 컬렉션 - 사용해야 하는 지원되는 기능 및 기타 기능
- **사용자 지정 LOB(기간 업무) 응용 프로그램이 지원되나요?** 예. Azure RemoteApp에서 사용자 지정 응용 프로그램을 사용하려면 [사용자 지정 템플릿 이미지](remoteapp-create-custom-image.md)를 만든 후 RemoteApp 컬렉션에 업로드하세요.
- **Azure RemoteApp에서 사용자 지정 LOB 응용 프로그램이 동작하나요?** 이를 알아보는 가장 좋은 방법은 테스트하는 것입니다. [RD 호환성 센터](http://www.rdcompatibility.com/compatibility/default.aspx)를 확인해 보세요.
- **어느 배포 메서드(클라우드 또는 하이브리드)가 내 조직에 가장 적합한가요?** 하이브리드 컬렉션은 SSO(Single Sign-On) 및 안전한 온-프레미스 네트워크 연결을 완벽하게 통합하려는 경우에 가장 완벽한 경험을 제공합니다. 클라우드 컬렉션은 여러 인증 방법을 사용하여 배포를 분리하는 민첩하고 쉬운 방법을 제공합니다. [배포 옵션](remoteapp-whatis.md)에 대해 자세히 알아봅니다.
- **SQL 또는 다른 데이터베이스가 온-프레미스나 Azure에 있습니다. 어떤 배포 유형을 사용해야 하나요?** SQL 또는 백 엔드 데이터베이스의 위치에 따라 달라집니다. 데이터베이스가 개인 네트워크에 있는 경우 하이브리드 컬렉션을 사용하세요. 데이터베이스가 인터넷에 노출되며 클라이언트 연결을 통해 데이터베이스에 연결할 수 있는 경우 클라우드 컬렉션을 사용할 수 있습니다.
- **드라이브 매핑, USB 및 직렬 포트, 클립보드 공유 및 프린터 리디렉션이란 무엇인가요?** 모든 해당 기능은 Azure RemoteApp에서 지원됩니다. 클립보드 공유 및 프린터 리디렉션은 기본적으로 설정되어 있습니다. 리디렉션에 대해 [여기](remoteapp-redirection.md)에서 더 알아볼 수 있습니다.


## 템플릿 이미지
- **클라우드 또는 기존 가상 컴퓨터를 내 RemoteApp 컬렉션의 템플릿으로 사용할 수 있나요?** 예! Azure VM을 기반으로 이미지를 만들거나, 구독에 포함된 이미지 중 하나를 사용하거나, 사용자 지정 이미지를 만들 수 있습니다. [RemoteApp 이미지 옵션](remoteapp-imageoptions.md)을 확인하세요.


## 네트워크 옵션
- **하이브리드 컬렉션에는 VNET이 필요합니다. 기존 VNET을 사용할 수 있습니까?** 기존 VNET이 Azure VNET일 경우 사용할 수 있습니다. 자세한 내용은 [하이브리드 컬렉션 지침](remoteapp-create-hybrid-deployment.md)의 "1단계: 가상 네트워크 설정"을 참조하세요.
- **VNET 클라우드 컬렉션과 함께 사용할 수 있습니까?** 할 수 있습니다. [클라우드 컬렉션 만들기](remoteapp-create-cloud-deployment.md), 특히 자세한 내용은 1단계를 확인합니다.

## 인증 옵션



- **인증은 어떻게 처리되나요? 어떤 방법이 지원되나요?** 클라우드 컬렉션은 Microsoft 계정과 Azure Active Directory 계정(Office 365 계정이기도 함)을 지원합니다. 하이브리드 컬렉션은 Windows Server Active Directory 배포에서 [Azure Active Directory 동기화](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)와 같은 도구를 사용하여 동기화된 Azure Active Directory 계정만 지원합니다. 특히 암호 동기화 옵션을 사용하여 동기화되거나 AD FS(Active Directory Federation Services) 페더레이션을 구성하여 동기화됩니다. [MFA(Multi-Factor Authentication)](https://azure.microsoft.com/services/multi-factor-authentication/)를 구성할 수도 있습니다.

>[AZURE.NOTE]Azure Active Directory 사용자는 구독과 연결된 테넌트로부터 시작되어야 합니다. (포털의 **설정** 페이지에서 구독을 보고 수정할 수 있습니다. 자세한 내용은 [RemoteApp에서 사용되는 Azure Active Directory 테넌트 변경](remoteapp-changetenant.md)을 참조하세요.)

- **내 Azure Active Directory 계정 액세스를 왜 지정할 수 없나요?** Azure Active Directory 사용자는 구독과 연결된 디렉터리로부터 시작되어야 합니다. 포털의 설정 탭에서 해당 디렉터리를 보거나 수정할 수 있습니다. 자세한 내용은 [RemoteApp에서 사용되는 Azure Active Directory 테넌트 변경](remoteapp-changetenant.md)을 참조하세요.

## 클라이언트 - Azure RemoteApp에 액세스하는 데 어떤 장치를 사용할 수 있습니까?
[Azure RemoteApp의 앱에 액세스](remoteapp-clients.md)에 다른 클라이언트를 설치하기 위한 단계를 포함하여 좋은 클라이언트 정보를 찾을 수 있습니다.

- **클라이언트 응용 프로그램에서 지원하는 장치 및 운영 체제는 무엇인가요?** 우선, 컴퓨터와 태블릿입니다.
	- Windows 10 (클라이언트 미리 보기)
	- Windows 8.1 및 Windows 8
	- Windows 7 서비스 팩 1
	- Mac OS X
	- Windows RT
	- Android 태블릿
	- iPad 및 전화:
	- iPhone
	- Android 휴대폰
	- Windows Phone
 
	지금 RemoteApp 클라이언트를 [다운로드](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx)하세요.
- **Azure RemoteApp은 씬 클라이언트를 지원하나요?** 예. 다음과 같은 Windows Embedded 씬 클라이언트가 지원됩니다.
	- Windows Embedded Standard 7
	- Windows Embedded 8 Standard
	- Windows Embedded 8.1 Industry Pro
	- Windows 10 IoT Enterprise

- **RDSH(원격 데스크톱 세션 호스트)에 지원되는 Windows Server 버전은 무엇인가요?** Windows Server 2012 R2입니다.

##지원 및 피드백


- **RemoteApp에 대한 지원 계획은 무엇인가요?** 청구 및 구독 관리 지원은 무료로 제공됩니다. 기술 지원은 [Azure 서비스 플랜](https://azure.microsoft.com/support/plans/)을 통해 이용할 수 있습니다. [Azure 토론 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)을 통해 커뮤니티 지원을 무료로 이용할 수도 있습니다.
- **사용자 의견을 제출하려면 어떻게 해야 하나요?** [피드백 포럼](https://feedback.azure.com/forums/247748-azure-remoteapp/)을 방문하세요.
- **Azure RemoteApp에 대해 자세히 알아보려면 어디로 문의해야 하나요?** 질문을 게시하는 데 유용한 [토론 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp) 외에도 RemoteApp과 관련된 모든 사항을 토론하는 주간 [전문가에게 물어보세요 웹 세미나](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)에 참가할 수 있습니다.
- **RemoteApp 설명서는 어떤가요?** 질문해 주셔서 감사합니다. 포털 도움말 서랍에 있는 도움말 콘텐츠(포털의 아무 페이지에서나 **?** 클릭) 외에도 RemoteApp과 관련된 모든 사항을 알려주는 다음 문서를 사용할 수 있습니다.
	- **시작:**
		- [RemoteApp이란?](remoteapp-whatis.md)
		- [RemoteApp 템플릿 이미지에 있는 항목](remoteapp-images.md)
		- [라이선스 작동 방식](remoteapp-licensing.md)
		- [RemoteApp과 Office가 함께 작동하는 방식](remoteapp-o365.md)
		- [RemoteApp에서 리디렉션이 작동하는 방식](remoteapp-redirection.md)
	- **배포:**
		- [사용자 지정 템플릿 이미지 만들기](remoteapp-create-custom-image.md)
		- [하이브리드 컬렉션 만들기](remoteapp-create-hybrid-deployment.md)
		- [클라우드 컬렉션 만들기](remoteapp-create-cloud-deployment.md)
		- [RemoteApp에 대해 Azure Active Directory 구성](remoteapp-ad.md)
		- [RemoteApp에 앱 게시](remoteapp-publish.md)
	- **관리:**
		- [사용자 추가](remoteapp-user.md)
		- [RemoteApp 구성 및 사용 모범 사례](remoteapp-bestpractices.md)

	동영상! RemoteApp에 대한 많은 동영상도 있습니다. 소개 동영상([Azure RemoteApp 소개](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/))도 있고, 배포([클라우드 배포](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) 및 [하이브리드 배포](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be))를 안내하는 동영상도 있습니다. 지금 확인해 보세요.

 
### 의견 보내기 
이 기사에 대한 등급을 매기고 아래에 의견을 다는 것은 물론 문서를 직접 변경할 수 있다는 사실을 알고 계셨나요? 누락된 부분이 있나요? 잘못된 부분이 있나요? 혼동을 줄 수 있는 부분이 있나요? 위로 스크롤하여 **GitHub에서 편집**을 클릭하면 변경할 수 있습니다. 당사에서 변경 사항을 검토하고 승인하면 변경 및 개선 사항을 바로 여기서 확인할 수 있습니다.

<!---HONumber=AcomDC_0629_2016-->