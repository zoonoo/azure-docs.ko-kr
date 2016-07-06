<properties 
	pageTitle="Azure RemoteApp란? | Microsoft Azure" 
	description="Azure RemoteApp를 통해 장치에 앱 및 리소스를 공유하는 방법을 알아봅니다." 
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
	ms.date="06/18/2016" 
	ms.author="elizapo"/>

# Azure RemoteApp이란?

Azure RemoteApp은 원격 데스크톱 서비스가 지원하는 온-프레미스 Microsoft RemoteApp 프로그램의 기능을 Azure에 제공합니다. Azure RemoteApp은 다양한 여러 사용자 장치에서 안전하게 원격으로 응용 프로그램에 액세스하는 데 도움이 됩니다. 기본적으로 Azure RemoteApp은 클라우드에서 비영구 터미널 서버 세션을 호스팅하고 이를 사용하고 사용자와 공유하기 위해 가져옵니다.

Azure RemoteApp를 사용하면 거의 모든 장치의 사용자와도 앱 및 리소스를 공유할 수 있습니다. 우리는 사용자의 앱을 클라우드에서 호스팅하므로 하드웨어 및 크기 조정을 사용자 요구에 충족하도록 관리합니다. 우리가 해야 하는 일은 게시가 공유하려고 하는 앱을 업로드한 다음 사용자가 해당 앱을 사용하도록 하는 것이 전부입니다. [사용자는 자신의 장치를 유지](remoteapp-clients.md)하는 반면에, 게시자는 Azure 포털을 통해 모든 항목을 관리합니다. 회사 자격 증명을 사용하는 옵션도 있으므로 앱과 데이터의 보안을 확보할 수 있습니다.

Azure RemoteApp에 대한 자세한 내용을 읽거나 이미 확신을 가진 경우 [지금 사용해 보세요](https://azure.microsoft.com/services/remoteapp/).

Azure RemoteApp과 관련된 질문이 있는 경우 [FAQ](remoteapp-faq.md)를 확인하세요.

Azure RemoteApp은 [Microsoft 가상 데스크톱 인프라](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)의 일부입니다.

**신규!** Azure RemoteApp에 대해 더 알아보고 싶으세요? 아니면 Azure RemoteApp을 확인할 준비가 되셨나요? 매주 열리는 [전문가에게 물어보세요. 웹 세미나](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website)에 참여해 보세요.

## Azure RemoteApp 컬렉션
다음과 같은 두 가지 종류의 [Azure RemoteApp 컬렉션](remoteapp-collections.md)이 있습니다.


- **클라우드 컬렉션**은 클라우드에 호스트되며 프로그램에 대한 데이터를 저장합니다. 사용자는 Microsoft 계정이나 Azure Active Directory와 동기화 또는 페더레이션된 회사 자격 증명으로 로그인하여 앱에 액세스할 수 있습니다.

	공유하려는 응용 프로그램이 회사의 사설 네트워크에 있는 리소스에 연결(예: VPN 장치를 통해)할 필요가 없는 경우 클라우드 컬렉션을 선택합니다. 응용 프로그램이 인터넷, OneDrive 또는 Azure의 리소스를 사용하는 경우 클라우드 컬렉션이 적합합니다. 가장 빨리 만들 수 있는 컬렉션이기도 합니다.

- **하이브리드 컬렉션**은 Azure 클라우드에 호스트되고 데이터를 저장하지만 사용자가 로컬 네트워크에 저장된 데이터 및 리소스에 액세스할 수 있게 합니다. 사용자는 Azure Active Directory와 동기화 또는 페더레이션된 회사 자격 증명으로 로그인하여 앱에 액세스할 수 있습니다.

	회사의 사설 네트워크에 있는 리소스에 연결해야 할 경우 하이브리드 컬렉션을 선택합니다. 예를 들어 응용 프로그램에서 다음에 액세스해야 하는 경우가 있습니다.

	- 인트라넷에 위치한 파일 서버
	- Quicken
	- 방화벽 뒤에 있는 데이터베이스

	일반적으로 클라우드로 옮길 수 없는 많은 양의 리소스가 사설 네트워크에 있는 대규모 회사에 유용합니다.

다양한 컬렉션에 네트워크를 포함한 다양한 옵션이 있으므로 가장 적합한 [컬렉션](remoteapp-collections.md)을 알아낼 수 있습니다.


### 컬렉션 업데이트
하이브리드 컬렉션과 클라우드 컬렉션 간의 주요 차이점 중 하나는 소프트웨어 업데이트가 처리되는 방식입니다. 사전 설치된 Office 365 ProPlus 또는 Office 2013 이미지를 사용하는 클라우드 컬렉션의 경우 업데이트에 대해 염려하지 않아도 됩니다. 서비스가 자체적으로 유지 관리되며 지속적으로 앱과 운영 체제의 업데이트를 출시합니다.

사용자 지정 템플릿 이미지를 사용하는 클라우드 컬렉션은 물론 하이브리드 컬렉션의 경우 이미지와 앱을 유지 관리해야 합니다. 도메인에 가입된 이미지의 경우 Windows 업데이트, 그룹 정책 또는 System Center와 같은 도구를 사용하여 업데이트를 제어할 수 있습니다.

사용자 지정 템플릿 이미지를 업데이트한 후 새 이미지를 Azure 클라우드로 업로드하고 새 이미지를 사용하도록 컬렉션을 업데이트합니다. (Azure RemoteApp **빠른 시작** 페이지나 대시보드에서 이 작업을 할 수 있습니다.)

자세한 내용은 [컬렉션 업데이트](remoteapp-update.md)를 참조하세요.

## 지원되는 RemoteApp 클라이언트
Azure RemoteApp은 Windows 및 Windows RT용 RemoteApp 클라이언트 앱과 Mac, iOS 및 Android용 Microsoft 원격 데스크톱 앱에서 지원됩니다. 사용자는 휴대폰이나 계산 장치에서 이러한 앱을 사용하여 새 Azure RemoteApp 프로그램에 액세스할 수 있습니다.

클라이언트에 대한 자세한 내용은 [Azure RemoteApp의 앱에 액세스](remoteapp-clients.md)를 참조하세요.

## 다음 단계
지금 사용해 보세요! 다음은 Azure RemoteApp를 시작하는 데 도움이 되는 문서입니다.

- [Azure RemoteApp에 필요한 컬렉션의 종류는 무엇입니까?](remoteapp-collections.md)
- [Azure RemoteApp 이미지 만들기](remoteapp-imageoptions.md)
- [Azure RemoteApp의 클라우드 컬렉션을 만드는 방법](remoteapp-create-cloud-deployment.md)
- [Azure RemoteApp의 하이브리드 컬렉션을 만드는 방법](remoteapp-create-hybrid-deployment.md)
- [Azure RemoteApp의 라이선스 작동 방식](remoteapp-licensing.md)
- [Azure RemoteApp 사용 모범 사례](remoteapp-bestpractices.md)
- [Azure RemoteApp FAQ](remoteapp-faq.md)
 

### 의견 보내기 
이 기사에 대한 등급을 매기고 아래에 의견을 다는 것은 물론 문서를 직접 변경할 수 있다는 사실을 알고 계셨나요? 누락된 부분이 있나요? 잘못된 부분이 있나요? 혼동을 줄 수 있는 부분이 있나요? 위로 스크롤하여 **GitHub에서 편집** 또는 **편집**을 클릭하면 변경할 수 있습니다. 당사에서 변경 사항을 검토하고 승인하면 변경 및 개선 사항을 바로 여기서 확인할 수 있습니다.

<!---HONumber=AcomDC_0629_2016-->