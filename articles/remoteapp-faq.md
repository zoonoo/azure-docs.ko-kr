<properties title="Azure RemoteApp FAQ" pageTitle="Azure RemoteApp FAQ" description="Azure RemoteApp에 대해 알아봅니다." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Azure RemoteApp FAQ
Azure RemoteApp과 관련된 질문은 다음과 같습니다. 다른 질문이 있을 경우 [RemoteApp 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-kr/home?forum=AzureRemoteApp)을 방문하여 알아야 할 사항을 알려 주세요.

##Azure RemoteApp 정의##


- **Azure RemoteApp이란 무엇인가요?** RemoteApp은 Azure에서 원격 데스크톱 서비스가 지원하는 온-프레미스 Microsoft RemoteApp의 기능을 제공하는 Azure 서비스입니다. RemoteApp은 다양한 여러 사용자 장치에서 안전하게 원격으로 응용 프로그램에 액세스할 수 있게 해줍니다. 자세한 내용은 [Azure RemoteApp](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-whatis/)을 참조하세요.
- **두 가지 종류의 배포 옵션은 ?** 클라우드와 하이브리드라는 두 가지 종류의 RemoteApp 배포(또는 컬렉션)가 있습니다. 조직에 가장 적합한 [배포 옵션](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-whatis/)을 확인하세요.

##지원되는 구성##


- **사용자 지정 LOB(기간 업무) 응용 프로그램이 지원되나요?** 예. Azure RemoteApp에서 사용자 지정 응용 프로그램을 사용하려면 [사용자 지정 템플릿 이미지](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-custom-image/)를 만든 후 RemoteApp 컬렉션에 업로드하세요.
- **내 사용자 지정 LOB 응용 프로그램이 Azure RemoteApp에서 작동하나요?** 테스트를 통해 확인하는 것이 가장 좋습니다. [응용 프로그램 호환성 요구 사항](http://www.microsoft.com/ko-kr/download/details.aspx?id=18704)을 검토하고 [RD 호환성 센터](http://www.rdcompatibility.com/compatibility/default.aspx)를 확인하세요.
- **내 조직에 가장 적합한 배포 방법(클라우드 또는 하이브리드)은 무엇인가요?** 전체 SSO(Single Sign-on) 통합과 보안 온-프레미스 네트워크 연결을 원하는 경우 하이브리드 컬렉션이 가장 완전한 환경을 제공합니다. 클라우드 컬렉션은 여러 인증 방법을 사용하여 배포를 민첩하고 쉽게 격리시키는 방법을 제공합니다. 자세한 내용은 [배포 옵션](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-whatis/)을 참조하세요.
- **하이브리드 컬렉션에는 VNET이 필요합니다. 기존 NET를 사용할 수 있나요?** 지금은 사용할 수 없지만 현재 작업 중입니다. 작업이 완료될 때까지 [이 지침](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx)에 따라 기존 VNET을 Azure RemoteApp VNET에 연결할 수 있습니다.
- **클라우드 또는 기존 가상 컴퓨터를 내 RemoteApp 컬렉션의 템플릿으로 사용할 수 있나요?** 지금은 사용할 수 없지만 [피드백 사이트](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w)에서 자주 요청되는 기능입니다.
- **SQL 또는 다른 데이터베이스가 온-프레미스나 Azure에 있습니다. 어떤 배포 유형을 사용해야 하나요?** SQL 또는 백 엔드 데이터베이스의 위치에 따라 달라집니다. 데이터베이스가 개인 네트워크에 있는 경우 하이브리드 컬렉션을 사용하세요. 데이터베이스가 인터넷에 노출되며 클라이언트 연결을 통해 데이터베이스에 연결할 수 있는 경우 클라우드 컬렉션을 사용할 수 있습니다.
- **드라이브 매핑, USB 및 직렬 포트, 클립보드 공유 및 프린터 리디렉션은 어떻게 처리되나요?** 이러한 기능은 Azure RemoteApp에서 모두 지원됩니다. 클립보드 공유 및 프린터 리디렉션은 기본적으로 사용됩니다. [드라이브 매핑이나 USB 및 직렬 포트 리디렉션을 사용하도록 설정하려면 여기서 문의하세요](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20enable%20Redirection%20request). 이 지원을 포털에 추가하기 위해 작업 중이지만 아직 구현되지는 않았습니다.
- **인증은 어떻게 처리되나요? 어떤 방법이 지원되나요?** 클라우드 컬렉션은 Microsoft 계정과 Azure Active Directory 계정(Office 365 계정이기도 함)을 지원합니다. 하이브리드 컬렉션은 Windows Server Active Directory 배포에서 DirSync와 같은 도구를 사용하여 동기화된 Azure Active Directory 계정만 지원합니다. 특히 암호 동기화 옵션을 사용하여 동기화되거나 AD FS(Active Directory Federation Services) 페더레이션을 구성하여 동기화됩니다. [MFA(Multi-Factor Authentication)](http://azure.microsoft.com/ko-kr/documentation/services/multi-factor-authentication/)를 구성할 수도 있습니다.

	**참고:** Azure Active Directory 사용자는 구독과 연결된 테넌트의 사용자여야 합니다. 포털의 **설정** 탭에서 구독을 보고 수정할 수 있습니다. 자세한 내용은 [RemoteApp에서 사용되는 Azure Active Directory 테넌트 변경](http://msdn.microsoft.com/ko-kr/3d6c4fd1-c981-4c57-9402-59fe31b11883)을 참조하세요.

- **내 Azure Active Directory 계정에 액세스 권한을 부여할 수 없는 것은 무엇 때문인가요?** Azure Active Directory 사용자는 구독과 연결된 디렉터리에 있어야 합니다. 포털의 설정 탭에서 해당 디렉터리를 보거나 수정할 수 있습니다. 자세한 내용은 [RemoteApp에서 사용되는 Azure Active Directory 테넌트 변경](http://msdn.microsoft.com/ko-kr/3d6c4fd1-c981-4c57-9402-59fe31b11883)을 참조하세요.
- **클라이언트 응용 프로그램에서 지원하는 장치 및 운영 체제는 무엇인가요?** 클라이언트 응용 프로그램은 Windows 8.1, Windows 8, Windows 7 서비스 팩 1, iOS, Mac OS X, Windows RT, Android 장치 및 Windows Phone에서 사용할 수 있습니다. Windows 10 미리 보기도 지원합니다.
 
	지금 RemoteApp 클라이언트를 [다운로드](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx)하세요.
- **Azure RemoteApp은 씬 클라이언트 지원하나요?** 예, 다음과 같은 Windows Embedded 씬 클라이언트가 지원됩니다.
	- Windows Embedded Standard 7 서비스 팩 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **RDSH(원격 데스크톱 세션 호스트)에 지원되는 Windows Server 버전은 무엇인가요?** Windows Server 2012 R2입니다.

##지원 및 피드백

- **이 서비스를 무료로 체험할 수 있나요?** 예. 30일 동안 무료 평가판을 사용할 수 있습니다. 평가 기간이 끝나면 프로덕션에서 사용 가능한 유료 계정으로 전환하거나 서비스 사용을 중지할 수 있습니다. [manage.windowsazure.com](http://manage.windowsazure.com) - 새 RemoteApp 인스턴스 만들기로 이동하여 무료 평가판을 시작하세요. 무료 평가판을 사용할 경우 인스턴스당 10명의 사용자를 포함하여 RemoteApp 인스턴스 2개를 빌드할 수 있습니다. 이 평가판은 30일 동안만 유지된다는 것을 명심하세요.
- **RemoteApp에 대한 지원 계획은 무엇인가요?** 청구 및 구독 관리 지원은 무료로 제공됩니다. 기술 지원은 [Azure 서비스 계획](http://azure.microsoft.com/support/plans/)을 통해 제공됩니다. [Azure 토론 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/ko-kr/home?forum=AzureRemoteApp)을 통해 무료 커뮤니티 지원을 받을 수도 있습니다. 
- **RemoteApp은 얼마인가요?** [Azure RemoteApp 가격 정보](http://azure.microsoft.com/ko-kr/pricing/details/remoteapp/)를 참조하세요.
- **피드백을 제출하려면 어떻게 해야 하나요?** [피드백 포럼](http://feedback.azure.com/forums/247748-azure-remoteapp)을 방문하세요.
- **Azure RemoteApp에 대해 자세히 알아보려면 어디로 문의해야 하나요?** 질문을 게시하는 데 유용한 [토론 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/ko-kr/home?forum=AzureRemoteApp) 외에도 RemoteApp과 관련된 모든 사항을 토론하는 주간 [전문가에게 물어보세요 웹 세미나](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)에 참가할 수 있습니다.
- **RemoteApp 설명서는 어떤가요?** 포털 도움말 서랍에 있는 도움말 콘텐츠(포털의 아무 페이지에서나 **?** 클릭) 외에도 RemoteApp과 관련된 모든 사항을 알려주는 다음 문서를 사용할 수 있습니다.
	- **시작:**
		- [RemoteApp 정의](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-whatis/)
		- [RemoteApp 템플릿 이미지에 있는 항목](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-images/)
		- [라이선스 작동 방식](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-licensing/)
		- [RemoteApp과 Office가 함께 작동하는 방식](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-o365/)
	- **배포:**
		- [사용자 지정 템플릿 이미지 만들기](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-custom-image/)
		- [하이브리드 컬렉션 만들기](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [클라우드 컬렉션 만들기](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-cloud-deployment/)
		- [RemoteApp에 대해 Azure Active Directory 구성](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-ad/)
		- [RemoteApp에 앱 게시](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-publish/)
	- **관리:**
		- [사용자 추가](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-user/)
		- [RemoteApp 구성 및 사용 모범 사례](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-bestpractices/)	

	동영상! RemoteApp에 대한 많은 동영상도 있습니다. 소개 동영상([Azure RemoteApp 소개](http://azure.microsoft.com/ko-kr/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/))도 있고, 배포([클라우드 배포](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) 및 [하이브리드 배포](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be))를 안내하는 동영상도 있습니다. 지금 확인해 보세요.


<!--HONumber=35.2-->
