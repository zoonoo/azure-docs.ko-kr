<properties 
	pageTitle="Azure RemoteApp용 하이브리드 컬렉션을 만드는 방법"
	description="내부 네트워크에 연결되는 RemoteApp 배포를 만드는 방법에 대해 알아봅니다."
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
	ms.date="08/12/2015"
	ms.author="elizapo"/>

# Azure RemoteApp용 하이브리드 컬렉션을 만드는 방법

다음과 같은 두 가지 종류의 RemoteApp 컬렉션이 있습니다.

- 클라우드: 완전히 Azure에서 상주하며 Azure 관리 포털의 **빠른 생성** 옵션을 사용하여 만들어집니다.  
- 하이브리드: 온-프레미스 액세스를 위한 가상 네트워크를 포함하며 관리 포털의 **VNET으로 만들기** 옵션을 사용하여 생성됩니다.

이 자습서에서는 하이브리드 컬렉션을 만드는 프로세스를 단계별로 안내합니다. 8가지 단계가 있습니다.

1.	컬렉션에 사용할 [이미지](remoteapp-imageoptions.md)를 결정합니다. 사용자 지정 이미지를 만들거나 구독에 포함된 Microsoft 이미지 중 하나를 선택할 수 있습니다.
2. 가상 네트워크를 설정합니다.
2.	RemoteApp 컬렉션을 만듭니다.
2.	컬렉션을 가상 네트워크에 연결합니다.
3.	템플릿 이미지를 컬렉션에 추가합니다.
4.	디렉터리 동기화를 구성합니다. RemoteApp은 1) 암호 동기화 옵션을 포함하여 Azure Active Directory 동기화를 구성하거나 2) 암호 동기화 옵션을 포함하지 않지만 AD FS로 페더레이션되는 도메인을 사용하여 Azure Active Directory 동기화를 구성하는 방식으로 Azure Active Directory와 통합해야 합니다. [RemoteApp과 함께 Active Directory에 대한 구성 정보](remoteapp-ad.md)를 확인합니다.
5.	RemoteApp 앱을 게시합니다.
6.	사용자 액세스를 구성합니다.

**시작하기 전에**

컬렉션을 만들기 전에 다음을 수행해야 합니다.

- RemoteApp에 [등록](http://azure.microsoft.com/services/remoteapp/)합니다. 
- RemoteApp 서비스 계정으로 사용할 Active Directory의 사용자 계정을 만듭니다. 이 계정의 권한은 도메인에 컴퓨터를 가입시킬 수 있는 권한만으로 제한합니다.
- 온-프레미스 네트워크에 대한 정보 수집: IP 주소 정보 및 VPN 장치 세부 정보입니다.
- [Azure PowerShell](../install-configure-powershell.md) 모듈을 설치합니다.
- 액세스 권한을 부여할 사용자에 대한 정보를 수집합니다. 각 사용자당 하나의 Azure Active Directory 사용자 계정 이름(예: name@contoso.com)이 필요합니다.
- 템플릿 이미지를 선택합니다. RemoteApp 템플릿 이미지는 사용자를 위해 게시하려는 앱 및 프로그램을 포함합니다. 자세한 내용은 [RemoteApp 이미지 옵션](remoteapp-imageoptions.md)을 참조하세요. 
- [RemoteApp에 대해 Azure Active Directory를 구성합니다](remoteapp-ad.md).



## 1단계: 가상 네트워크를 설정합니다.
기존 Azure 가상 네트워크를 사용하는 하이브리드 RemoteApp 컬렉션을 배포하거나 새 가상 네트워크를 만들 수 있습니다. 가상 네트워크를 사용하여 사용자는 RemoteApp 원격 리소스를 통해 로컬 네트워크의 데이터에 액세스할 수 있습니다. Azure 가상 네트워크를 사용하여 다른 Azure 서비스 및 해당 가상 네트워크에 배포된 가상 컴퓨터에 대한 직접 네트워크 액세스를 컬렉션에 제공합니다.

### Azure VNET을 만들고 Active Directory 배포에 조인

[가상 네트워크](../virtual-network/virtual-networks-create-vnet.md)를 만들기 시작합니다. 이 작업은 Azure 관리 포털의 **네트워크** 탭에서 수행됩니다. Azure Active Directory 테넌트로 동기화되는 Active Directory 배포에 가상 네트워크를 연결해야 합니다.

자세한 내용은 [관리 포털에서 가상 네트워크 설정 정보](../virtual-network/virtual-networks-settings.md)를 참조하세요.

### 가상 네트워크가 RemoteApp에 대해 사용할 수 있는지 확인합니다.
RemoteApp 컬렉션을 만들기 전에 새 가상 네트워크를 사용할 수 있는지 확인해 보겠습니다. 다음을 수행하여 이를 확인할 수 있습니다.

1. RemoteApp에 대해 방금 만든 가상 네트워크의 서브넷 내에 Azure 가상 컴퓨터를 만듭니다.
2. 원격 데스크톱을 사용하여 가상 컴퓨터에 연결합니다. (**연결**을 클릭합니다.)
3. RemoteApp에 사용하려는 것과 동일한 Active Directory 배포에 조인합니다.

작동했나요? 가상 네트워크 및 서브넷을 RemoteApp에 사용할 수 있습니다!

Azure 가상 컴퓨터 만들기 및 원격 데스크톱을 사용하여 연결하기에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/library/azure/jj156003.aspx)에서 찾을 수 있습니다.

## 2단계: RemoteApp 컬렉션 만들기 ##



1. [Azure 관리 포털](http://manage.windowsazure.com)에서, RemoteApp 페이지로 이동합니다.
2. **새로 만들기 > VNET으로 만들기**를 클릭합니다.
3. 컬렉션의 이름을 입력합니다.
4. 사용할 계획(표준 또는 기본)을 선택합니다.
5. **RemoteApp 컬렉션 만들기**를 클릭합니다.

RemoteApp 컬렉션을 만든 후에는 컬렉션의 이름을 두 번 클릭합니다. 그러면 **빠른 시작** 페이지가 나타나며 여기에서 컬렉션 구성을 완료합니다.

## 3단계: 컬렉션을 가상 네트워크에 연결합니다. ##

 
1. **퀵 스타트** 페이지에서 **가상 네트워크 연결**을 클릭합니다.
2. 드롭다운 목록에서 사용하려는 가상 네트워크를 선택합니다.
3. 사용하려는 지역을 선택하고 올바른 구독이 필드에 나타나는지 확인합니다. 
5. **퀵 스타트** 페이지로 돌아가 **로컬 도메인 가입**을 클릭합니다. 로컬 Active Directory 도메인에 RemoteApp 서비스 계정을 추가합니다. 도메인 이름, 조직 구성 단위, 서비스 계정 사용자 이름 및 암호가 필요합니다. 

	[Azure RemoteApp에 대해 Active Directory 구성](remoteapp-ad.md)에서 단계를 수행하면 수집되는 정보입니다.


## 4단계: RemoteApp 이미지에 연결 ##

RemoteApp 템플릿 이미지에는 사용자와 공유할 프로그램이 포함됩니다. 새 [템플릿 이미지](remoteapp-imageoptions.md) 또는 기존 이미지(Azure RemoteApp에서 이미 가져오거나 업로드된 것)에 연결할 수 있습니다. Office 365 또는 Office 2013(평가판 사용) 프로그램을 포함하는 RemoteApp [템플릿 이미지](remoteapp-images.md) 중 하나에 연결할 수도 있습니다.

새 이미지를 업로드하는 경우 이름을 입력하고 이미지의 위치를 선택해야 합니다. 마법사의 다음 페이지에는 PowerShell cmdlet 집합이 표시됩니다. 관리자 권한 Windows PowerShell 프롬프트에서 이러한 cmdlet을 복사하고 실행하여 지정된 이미지를 업로드합니다.

기존 템플릿 이미지에 연결하는 경우 단지 이미지 이름, 위치 및 연결된 Azure 구독을 지정하기만 하면 됩니다.



## 5단계 : Active Directory 디렉터리 동기화 구성 ##

RemoteApp은 1) 암호 동기화 옵션을 포함하여 Azure Active Directory 동기화를 구성하거나 2) 암호 동기화 옵션을 포함하지 않지만 AD FS로 페더레이션되는 도메인을 사용하여 Azure Active Directory 동기화를 구성하는 방식으로 Azure Active Directory와 통합해야 합니다. 계획 정보 및 자세한 단계에 대해서는 [디렉터리 동기화 로드맵](http://msdn.microsoft.com//library/azure/hh967642.aspx)을 참조하세요.

## 6단계: RemoteApp 앱 게시 ##

RemoteApp 앱은 사용자에게 제공하는 앱 또는 프로그램입니다. 이 프로그램은 컬렉션에 대해 업로드한 템플릿 이미지에 있습니다. 사용자가 앱에 액세스할 때 앱이 로컬 환경에서 실행하는 것처럼 보이지만 실제로는 Azure에서 실행됩니다.

사용자가 RemoteApp 앱에 액세스할 수 있으려면 사용자가 원격 데스크톱 클라이언트를 통해 액세스하는 사용 가능한 앱 목록인 최종 사용자 피드에 게시해야 합니다.
 
RemoteApp 컬렉션에 여러 앱을 게시할 수 있습니다. RemoteApp 게시 페이지에서 **게시**를 클릭하여 앱을 추가합니다. 템플릿 이미지의 시작 메뉴에서 또는 앱의 템플릿 이미지에 경로를 지정하여 게시할 수 있습니다. 시작 메뉴에서 추가하도록 선택하는 경우 앱에 게시할 프로그램을 선택합니다. 앱의 경로를 제공하도록 선택한 경우 앱의 이름 및 템플릿 이미지에서 앱이 설치된 경로를 입력합니다.

## 7단계: 사용자 액세스 구성 ##

RemoteApp 컬렉션을 만들었으므로 원격 리소스를 사용할 수 있는 사용자를 추가해야 합니다. 액세스 권한을 제공하는 사용자는 이 RemoteApp 컬렉션을 만드는 데 사용한 구독과 연결된 Active Directory 테넌트에 존재해야 합니다.

1.	빠른 시작 페이지에서 **사용자 액세스 구성**을 클릭합니다. 
2.	액세스 권한을 부여할 Microsoft 계정이나 Active Directory의 작업 계정을 입력합니다.

	**참고:**

	"user@domain.com" 형식을 사용해야 합니다.

	컬렉션에서 Office 365 ProPlus를 사용하는 경우 사용자에 대해 Active Directory ID를 사용해야 합니다. 그러면 라이선스 유효성 검사에 도움이 됩니다.


3.	사용자가 확인되면 **저장**을 클릭합니다.


## 다음 단계 ##
RemoteApp 하이브리드 컬렉션을 성공적으로 만들고 배포했습니다. 다음 단계는 사용자가 원격 데스크톱 클라이언트를 다운로드하여 설치하도록 설정하는 것입니다. RemoteApp 빠른 시작 페이지에서 클라이언트의 URL을 찾을 수 있습니다. 그런 다음 사용자가 클라이언트에 로그인하여 게시된 앱에 액세스하도록 합니다.


 

<!---HONumber=August15_HO9-->