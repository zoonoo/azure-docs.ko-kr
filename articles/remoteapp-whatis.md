<properties title="What is RemoteApp?" pageTitle="RemoteApp이란?" description="Azure RemoteApp에 대해 알아봅니다." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

#Azure RemoteApp이란?

Azure RemoteApp은 원격 데스크톱 서비스로 지원되는 온-프레미스 Microsoft RemoteApp 프로그램의 기능을 Azure에 추가합니다. Azure RemoteApp을 사용하면 다양한 사용자 장치에서 응용 프로그램에 안전하게 원격으로 액세스할 수 있습니다.

RemoteApp을 Azure로 이동하면 복잡한 온-프레미스 구성에 대한 우려 없이 저장소, 확장성 및 글로벌 환경을 활용할 수 있습니다. 비즈니스 용도에 가장 적합한 앱을 만드는 것과 같은 더 중요한 문제에 전념할 수 있도록 Microsoft에서 Azure를 유지 관리하여 안정성을 제공합니다. Azure RemoteApp의 또 다른 이점은 액세스 가능성입니다. 사용자가 Windows, iOS, Mac OS X 및 Android 장치에서 RemoteApp 프로그램에 액세스할 수 있습니다. 사용자는 선호하는 환경에서 앱을 사용할 수 있고 관리자는 Azure 관리 포털을 사용하여 해당 앱을 관리합니다. 

RemoteApp에 대한 자세한 내용을 읽어 보거나, 이미 확신이 든다면 [지금 사용해 보세요](http://azure.microsoft.com/ko-kr/services/remoteapp/).

Azure RemoteApp은 [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/ko-kr/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)의 일부입니다.

**새롭습니다!** Azure RemoteApp에 대해 더 알아보고 싶으세요? 아니면 RemoteApp을 확인할 준비가 되셨나요? 매주 열리는 [전문가에게 물어보세요. 웹 세미나](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)에 참여해 보세요.

##RemoteApp 배포 옵션
다음과 같은 두 가지 종류의 RemoteApp 배포가 있습니다.


- **클라우드 배포**는 Azure 클라우드에서 호스트되고 프로그램에 대한 모든 데이터를 저장합니다. 사용자는 Azure Active Directory와 동기화되거나 페더레이션되는 Microsoft 계정 또는 회사 자격 증명으로 로그인하여 앱에 액세스할 수 있습니다.
- **하이브리드 배포**는 Azure 클라우드에서 호스트되고 데이터를 저장하지만, 이 배포를 통해 사용자가 로컬 네트워크에 저장된 데이터와 리소스에 액세스할 수도 있습니다. 사용자는 Azure Active Directory와 동기화되거나 페더레이션되는 회사 자격 증명으로 로그인하여 앱에 액세스할 수 있습니다.

###클라우드 배포

[클라우드 RemoteApp 배포](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-cloud-deployment/)는 클라우드에서 응용 프로그램을 호스트하는 독립 실행형 방법을 제공합니다. 클라우드 배포는 로컬 네트워크에 연결하는 것이 아니라 Azure 클라우드에서만 존재합니다.

RemoteApp 미리 보기와 함께 미리 설치되고 사용자와 공유할 준비가 된 Office 2013 앱이 제공됩니다. 사용 가능한 소프트웨어를 활용하면 서비스를 빠르게 프로비전할 수 있습니다.

클라우드 배포를 Office 2013 앱과 함께 사용하면 앱 및 서비스가 빌드되어 있는 운영 체제가 정기적인 업데이트를 통해 항상 최신 상태로 유지되고 Microsoft 맬웨어 방지 끝점 보호에서 지속적인 보호를 제공한다는 추가적인 이점이 있습니다. 최종 사용자는 Microsoft 계정 또는 회사 자격 증명을 사용하여 응용 프로그램에 액세스합니다. 관리자는 누가 어떤 앱에 액세스할 수 있도록 할지만 결정하면 됩니다.

클라우드 배포를 만들어 사용자에 대한 사용자 지정 응용 프로그램 또는 응용 프로그램 집합을 공유할 수도 있습니다. 이 작업을 하려면 [사용자 지정 템플릿 이미지를 만들고](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-custom-image/)(RemoteApp에 앱을 게시하는 방법) 배포를 만들 때 Office 2013 이미지가 아니라 해당 이미지를 선택해야 합니다. 

###하이브리드 배포
[하이브리드 RemoteApp 배포](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-hybrid-deployment/)를 사용하면 사용자에게 사용자 지정 응용 프로그램 집합이 제공되고 사용자가 로컬 네트워크의 데이터 및 리소스에 액세스할 수 있습니다. 클라우드 배포에서 사용되는 사용자 지정 이미지와 달리 하이브리드 배포용으로 만든 이미지는 도메인에 가입된 환경에서 앱을 실행하여 로컬 네트워크 및 데이터에 대한 전체 액세스 권한을 부여합니다.

Azure Active Directory(DirSync 사용)와 Active Directory를 통합하면 사용자가 회사 자격 증명을 사용하여 앱과 데이터에 액세스할 수 있습니다. Active Directory에서 작업 계정을 사용하면 RemoteApp을 통해 제공하는 앱을 제어하기 위한 회사 정책을 클라우드에 적용할 수 있습니다.

RD 세션 호스트 역할 서비스를 통해 Windows Server 2012 R2에서 템플릿 이미지를 빌드하면 사용자에 대해 게시할 수는 앱에 몇 가지 제한이 적용됩니다. 앱이 해당 템플릿 이미지 환경에서 제대로 작동하는 경우 최종 사용자가 RemoteApp을 통해 앱에 액세스할 수 있습니다. 

###배포 업데이트
하이브리드 및 클라우드 배포 간의 주요 차이점 중 하나는 소프트웨어 업데이트 처리 방법입니다. 미리 설치된 Office 2013 이미지를 사용하는 클라우드 배포를 사용하면 업데이트에 대해 걱정할 필요가 없습니다. 서비스가 자체적으로 유지 관리되고 지속적으로 앱과 운영 체제에 업데이트를 전달합니다.

하이브리드 배포뿐 아니라 사용자 지정 템플릿 이미지를 사용하는 클라우드 배포의 경우 관리자가 이미지와 앱을 유지 관리합니다. 도메인에 가입된 이미지의 경우 Windows Update, 그룹 정책 또는 System Center와 같은 도구를 사용하여 업데이트를 제어할 수 있습니다.

사용자 지정 템플릿 이미지를 업데이트한 후 Azure 클라우드에 새 이미지를 업로드하고 새 이미지를 사용하도록 배포를 업데이트합니다. RemoteApp 빠른 시작 페이지나 대시보드에서 이 작업을 할 수 있습니다.

##지원되는 RemoteApp 클라이언트
Azure RemoteApp 미리 보기 릴리스의 일부로 Windows 및 Windows RT용 새 Microsoft RemoteApp 클라이언트 앱과 iOS 및 Android용 Microsoft 원격 데스크톱 앱에 대한 업데이트가 릴리스되었습니다. 사용자는 모바일이나 계산 장치에서 이러한 앱을 사용하여 새 RemoteApp 프로그램에 액세스할 수 있습니다.

##다음 단계
지금 바로 사용해 보세요! 다음 문서는 RemoteApp을 시작하는 데 도움이 됩니다.

- [RemoteApp용 사용자 지정 템플릿 이미지를 만드는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-custom-image/)
- [RemoteApp의 클라우드 배포를 만드는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-cloud-deployment/)
- [RemoteApp의 하이브리드 배포를 만드는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-hybrid-deployment/)
