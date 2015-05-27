<properties 
	pageTitle="RemoteApp의 클라우드 컬렉션을 만드는 방법" 
	description="Azure 클라우드에 데이터를 저장하는 RemoteApp 배포를 만드는 방법에 대해 알아봅니다." 
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

# RemoteApp의 클라우드 컬렉션을 만드는 방법

다음과 같은 두 가지 종류의 RemoteApp 컬렉션이 있습니다.

- 클라우드: 완전히 Azure에서 상주하며 Azure 관리 포털의 **빠른 생성** 옵션을 사용하여 만들어집니다.  
- 하이브리드: 온-프레미스 액세스를 위한 가상 네트워크를 포함하며 관리 포털의 **VPN으로 만들기** 옵션을 사용하여 생성됩니다.

이 자습서에서는 클라우드 컬렉션을 만드는 프로세스를 단계별로 안내합니다. 이 프로세스는 다음 4개의 단계로 구성됩니다.

1.	RemoteApp 컬렉션을 만듭니다.
2.	선택적으로 디렉터리 동기화를 구성합니다. 이 작업은 RemoteApp에서 온-프레미스 Active Directory에서 Azure Active Directory 테넌트로 사용자, 연락처 및 암호를 동기화하는 데 필요합니다.
5.	RemoteApp 앱을 게시합니다.
6.	사용자 액세스를 구성합니다.

**시작하기 전에**

컬렉션을 만들기 전에 다음을 수행해야 합니다.

- RemoteApp에 [등록](http://azure.microsoft.com/services/remoteapp/)합니다. 
- 액세스 권한을 부여할 사용자에 대한 정보를 수집합니다. 이 정보는 사용자의 Microsoft 계정 정보나 Active Directory 작업 계정 정보가 될 수 있습니다.
- 이 프로세스에서는 구독의 일부로 제공된 템플릿 이미지 중 하나를 사용할 예정이거나 사용할 템플릿 이미지를 이미 업로드했다고 가정합니다. 다른 템플릿 이미지를 업로드해야 하는 경우 템플릿 이미지 페이지에서 그렇게 할 수 있습니다. 단지 **템플릿 이미지 업로드**를 클릭하고 마법사의 단계를 따르면 됩니다. 
- 사용자 지정 앱이나 LOB 프로그램을 제공하려는 경우 새로운 [이미지](remoteapp-imageoptions.md)를 만든 다음 클라우드 컬렉션에서 사용합니다.

## 1단계: RemoteApp 컬렉션을 만듭니다. ##



1. 관리 포털에서 RemoteApp 페이지로 이동합니다.
2. **새로 만들기 > 빠른 생성**을 클릭합니다.
3. 컬렉션의 이름을 입력하고 지역을 선택합니다.
4. 사용할 계획(표준 또는 기본)을 선택합니다.
5. 이 컬렉션에 사용할 템플릿을 선택합니다. 

	**팁:** RemoteApp의 구독은 Office 365 또는 Office 2013(평가판 사용) 프로그램, 게시된 항목(예: Word) 및 기타 게시할 항목을 포함하는 [템플릿 이미지](remoteapp-images.md)와 함께 제공됩니다. 또한 새로운 [이미지](remoteapp-imageoptions.md)를 만든 다음 클라우드 컬렉션에서 사용합니다.


1. **RemoteApp 컬렉션 만들기**를 클릭합니다.
	
	**중요:** 컬렉션을 프로비전하는 데 최대 30분 정도 걸릴 수 있습니다.

RemoteApp 컬렉션을 만든 후 RemoteApp **퀵 스타트** 페이지로 이동하여 설정 단계를 계속 진행합니다.


## 2단계: Active Directory 디렉터리 동기화 구성(선택 사항) ##

Active Directory를 사용하려는 경우 RemoteApp에서 Azure Active Directory 테넌트와 사용자, 연락처 및 암호를 동기화하려면 Azure Active Directory와 온-프레미스 Active Directory 간의 디렉터리 동기화가 필요합니다. 계획에 대한 내용은 [Azure RemoteApp에 대해 Active Directory 구성](remoteapp-ad.md)을 참조하세요.

## 3단계: RemoteApp 앱 게시 ##

RemoteApp 앱은 사용자에게 제공하는 앱 또는 프로그램입니다. 이 프로그램은 컬렉션에 대해 업로드한 템플릿 이미지에 있습니다. 사용자가 RemoteApp 앱에 액세스할 때 앱이 로컬 환경에서 실행하는 것처럼 보이지만 실제로는 Azure에서 실행됩니다.

사용자가 앱에 액세스할 수 있으려면 사용자가 원격 데스크톱 클라이언트를 통해 액세스하는 사용 가능한 앱 목록인 최종 사용자 피드에 게시해야 합니다.
 
RemoteApp 컬렉션에 여러 앱을 게시할 수 있습니다. RemoteApp 게시 페이지에서 **게시**를 클릭하여 프로그램을 추가합니다. 템플릿 이미지의 시작 메뉴에서 또는 앱의 템플릿 이미지에 경로를 지정하여 게시할 수 있습니다. 시작 메뉴에서 추가하도록 선택하는 경우 게시할 앱을 선택합니다. 앱의 경로를 제공하도록 선택한 경우 앱의 이름 및 템플릿 이미지에서 앱이 설치된 경로를 입력합니다.

## 4단계: 사용자 액세스 구성 ##

RemoteApp 컬렉션을 만들었으므로 원격 리소스를 사용할 수 있는 사용자를 추가해야 합니다. Active Directory를 사용하는 경우 액세스 권한을 제공하는 사용자는 이 RemoteApp 컬렉션을 만드는 데 사용한 구독과 연결된 Active Directory 테넌트에 있어야 합니다.

1.	빠른 시작 페이지에서 **사용자 액세스 구성**을 클릭합니다. 
2.	액세스 권한을 부여할 Microsoft 계정이나 Active Directory의 작업 계정을 입력합니다.

	**참고:**

	"user@domain.com" 형식을 사용해야 합니다.

	컬렉션에서 Office 365 ProPlus를 사용하는 경우 사용자에 대해 Active Directory ID를 사용해야 합니다. 그러면 라이선스 유효성 검사에 도움이 됩니다.

3.	사용자가 확인되면 **저장**을 클릭합니다.


## 다음 단계 ##

RemoteApp 클라우드 컬렉션을 만들고 배포했습니다. 다음 단계는 사용자가 원격 데스크톱 클라이언트를 다운로드하여 설치하도록 설정하는 것입니다. RemoteApp 빠른 시작 페이지에서 클라이언트의 URL을 찾을 수 있습니다. 그런 다음 사용자가 클라이언트에 로그인하여 게시된 앱에 액세스하도록 합니다.


<!--HONumber=54-->