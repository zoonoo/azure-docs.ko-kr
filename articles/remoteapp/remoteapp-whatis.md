<properties 
	pageTitle="RemoteApp이란?" 
	description="Azure RemoteApp에 대해 알아봅니다." 
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
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="elizapo"/>

# Azure RemoteApp이란?

Azure RemoteApp은 원격 데스크톱 서비스가 지원하는 온-프레미스 Microsoft RemoteApp 프로그램의 기능을 Azure에 제공합니다. Azure RemoteApp은 다양한 여러 사용자 장치에서 안전하게 원격으로 응용 프로그램에 액세스하는 데 도움이 됩니다.

RemoteApp을 Azure로 이동하면 복잡한 온-프레미스 구성에 대해 염려하지 않고 Azure의 저장소, 확장성 및 글로벌 환경을 활용할 수 있습니다. 비즈니스 용도에 가장 적합한 앱을 만드는 것과 같은 더 중요한 문제에 전념할 수 있도록 Microsoft에서 Azure를 유지 관리하여 안정성을 제공합니다. Azure RemoteApp의 또 다른 이점은 액세스 가능성입니다. 사용자가 Windows, iOS, Mac OS X 및 Android 장치에서 RemoteApp 프로그램에 액세스할 수 있습니다. 사용자는 선호하는 환경에서 앱을 사용할 수 있고 관리자는 Azure 관리 포털을 사용하여 해당 앱을 관리합니다.

RemoteApp에 대한 자세한 내용을 읽거나 이미 확신을 가진 경우 [지금 사용해 보세요](http://azure.microsoft.com/services/remoteapp/).

Azure RemoteApp과 관련된 질문이 있는 경우 [FAQ](remoteapp-faq.md)를 확인하세요.

Azure RemoteApp은 [Microsoft 가상 데스크톱 인프라](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)의 일부입니다.

**신규!** Azure RemoteApp에 대해 더 알아보고 싶으세요? 아니면 RemoteApp을 확인할 준비가 되셨나요? 매주 열리는 [전문가에게 물어보세요. 웹 세미나](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website)에 참여해 보세요.

## RemoteApp 배포 옵션
다음과 같은 두 가지 종류의 RemoteApp 컬렉션이 있습니다.


- **클라우드 컬렉션**은 Azure 클라우드에 호스트되며 프로그램에 대한 모든 데이터를 저장합니다. 사용자는 Microsoft 계정이나 Azure Active Directory와 동기화 또는 페더레이션된 회사 자격 증명으로 로그인하여 앱에 액세스할 수 있습니다.
- **하이브리드 컬렉션**은 Azure 클라우드에 호스트되고 데이터를 저장하지만 사용자가 로컬 네트워크에 저장된 데이터 및 리소스에 액세스할 수 있게 합니다. 사용자는 Azure Active Directory와 동기화 또는 페더레이션된 회사 자격 증명으로 로그인하여 앱에 액세스할 수 있습니다.

### 클라우드 컬렉션

[클라우드 RemoteApp 컬렉션](remoteapp-create-cloud-deployment.md)은 클라우드에서 응용 프로그램을 호스트하는 독립 실행형 방법을 제공합니다. 클라우드 컬렉션은 로컬 네트워크에 연결하지 않고 Azure 클라우드에만 있습니다.

RemoteApp 평가판의 일부로, 사전 설치되어 사용자와 공유할 수 있는 Office 365 ProPlus 또는 Office 2013 앱을 제공합니다. 사용 가능한 소프트웨어를 이용하도록 선택한 경우 서비스를 빠르게 프로비전할 수 있습니다.

Office와 함께 클라우드 컬렉션을 사용할 경우 서비스가 빌드된 운영 체제 및 앱이 정기적인 업데이트를 통해 항상 최신 상태로 유지되며 Microsoft 맬웨어 방지 프로그램 끝점 보호를 통해 지속적으로 보호된다는 추가 장점이 있습니다. 최종 사용자는 Microsoft 계정 또는 회사 자격 증명을 사용하여 응용 프로그램에 액세스합니다. 관리자는 누가 어떤 앱에 액세스할 수 있어야 하는지만 파악하면 됩니다.

클라우드 컬렉션을 만들어 사용자를 위해 사용자 지정 응용 프로그램 또는 응용 프로그램 집합을 공유할 수도 있습니다. 이렇게 하려면 [사용자 지정 이미지를 만들고](remoteapp-imageoptions.md)(앱을 RemoteApp에 게시하는 데 사용) 컬렉션을 만들 때 Office 2013 이미지 대신 해당 이미지를 선택하면 됩니다.

#### 클라우드를 선택하는 경우

클라우드 컬렉션은 응용 프로그램이 VPN 장치를 통해 회사의 개인 네트워크에서 리소스에 연결하도록 요청하지 않을 때 적합합니다. 응용 프로그램이 인터넷, OneDrive 또는 Azure의 리소스를 사용하는 경우에 작동하며 가장 간단한 설정 방법이 됩니다.

### 하이브리드 컬렉션
[하이브리드 RemoteApp 컬렉션](remoteapp-create-hybrid-deployment.md)을 사용하면 사용자 지정 응용 프로그램 집합을 사용자에게 제공하고 로컬 네트워크의 데이터와 리소스에 액세스하도록 할 수 있습니다. 클라우드 컬렉션과 함께 사용되는 사용자 지정 이미지와 달리 하이브리드 컬렉션용으로 만든 이미지는 도메인에 가입된 환경에서 앱을 실행하고 로컬 네트워크와 데이터에 대한 모든 권한을 부여합니다.

Active Directory와 Azure Active Directory가 통합되므로(DirSync 사용) 사용자가 회사 자격 증명을 사용하여 앱과 데이터에 액세스할 수 있습니다. Active Directory에서 회사 계정을 사용할 경우 회사 정책을 클라우드로 가져와 RemoteApp을 통해 제공하는 앱을 제어할 수 있습니다.

RD 세션 호스트 역할 서비스가 포함된 Windows Server 2012 R2에서 템플릿 이미지를 빌드하는 한 사용자에 대해 게시할 수 있는 앱에 대한 제한이 거의 없습니다. 앱이 해당 템플릿 이미지 환경에서 제대로 작동하는 경우 최종 사용자가 RemoteApp을 통해 앱에 액세스할 수 있습니다.

#### 하이브리드를 선택하는 경우

하이브리드는 개인 네트워크에 다시 연결 해야하는 경우에 더 적합합니다. 적절한 예는 다음과 같습니다.

- 개인 파일 서버
- Quicken
- 개인 데이터베이스
- 등

일반적으로 클라우드로 이동할 수 없는 개인 네트워크에 많은 리소스가 있는 대규모 회사에 보다 유용합니다.

### 컬렉션 업데이트
하이브리드 컬렉션과 클라우드 컬렉션 간의 주요 차이점 중 하나는 소프트웨어 업데이트가 처리되는 방식입니다. 사전 설치된 Office 365 ProPlus 또는 Office 2013 이미지를 사용하는 클라우드 컬렉션의 경우 업데이트에 대해 염려하지 않아도 됩니다. 서비스가 자체적으로 유지 관리되며 지속적으로 앱과 운영 체제의 업데이트를 출시합니다.

사용자 지정 템플릿 이미지를 사용하는 클라우드 컬렉션은 물론 하이브리드 컬렉션의 경우 이미지와 앱을 유지 관리해야 합니다. 도메인에 가입된 이미지의 경우 Windows 업데이트, 그룹 정책 또는 System Center와 같은 도구를 사용하여 업데이트를 제어할 수 있습니다.

사용자 지정 템플릿 이미지를 업데이트한 후 새 이미지를 Azure 클라우드로 업로드하고 새 이미지를 사용하도록 컬렉션을 업데이트합니다. (RemoteApp **퀵 스타트** 페이지나 대시보드에서 이 작업을 할 수 있습니다.)

## 지원되는 RemoteApp 클라이언트
Azure RemoteApp은 Windows 및 Windows RT용 RemoteApp 클라이언트 앱과 Mac, iOS 및 Android용 Microsoft 원격 데스크톱 앱에서 지원됩니다. 사용자는 휴대폰이나 컴퓨팅 장치에서 이러한 앱을 사용하여 새 RemoteApp 프로그램에 액세스할 수 있습니다.

## 다음 단계
지금 사용해 보세요! 다음은 RemoteApp를 시작하는 데 도움이 되는 문서입니다.

- [RemoteApp 이미지 만들기](remoteapp-imageoptions.md)
- [RemoteApp의 클라우드 컬렉션을 만드는 방법](remoteapp-create-cloud-deployment.md)
- [RemoteApp의 하이브리드 컬렉션을 만드는 방법](remoteapp-create-hybrid-deployment.md)
- [RemoteApp의 라이선스 작동 방식](remoteapp-licensing.md)
- [Azure RemoteApp 사용 모범 사례](remoteapp-bestpractices.md)
- [Azure RemoteApp FAQ](remoteapp-faq.md)

<!--HONumber=54--> 