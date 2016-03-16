<properties 
	pageTitle="Azure RemoteApp의 클라우드 컬렉션을 만드는 방법 | Microsoft Azure" 
	description="Azure 클라우드에 데이터를 저장하는 Azure RemoteApp 배포를 만드는 방법에 대해 알아봅니다." 
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
	ms.date="02/05/2016" 
	ms.author="elizapo"/>

# Azure RemoteApp의 클라우드 컬렉션을 만드는 방법

다음과 같은 두 가지 종류의 [Azure RemoteApp 컬렉션](remoteapp-collections.md)이 있습니다.

- 클라우드: 완전히 Azure에 상주합니다. 클라우드에 모든 데이터를 저장(클라우드 전용 컬렉션)하거나 컬렉션을 VNET에 연결하고 해당 위치에 데이터를 저장하도록 선택할 수 있습니다.   
- 하이브리드: 온-프레미스 액세스를 위해 가상 네트워크를 포함합니다. 이 경우 Azure AD 및 온-프레미스 Active Directory 환경을 사용해야 합니다.

이 자습서에서는 클라우드 컬렉션을 만드는 프로세스를 단계별로 안내합니다. 이 프로세스는 다음 4개의 단계로 구성됩니다.

1.	Azure RemoteApp 컬렉션을 만듭니다.
2.	선택적으로 디렉터리 동기화를 구성합니다. Azure AD와 Active Directory를 사용하는 경우 온-프레미스 Active Directory에서 Azure AD 테넌트로 사용자, 연락처 및 암호를 동기화해야 합니다.
5.	앱을 게시합니다.
6.	사용자 액세스를 구성합니다.


**시작하기 전에**

컬렉션을 만들기 전에 다음을 수행해야 합니다.

- Azure RemoteApp에 [등록](https://azure.microsoft.com/services/remoteapp/)합니다. 
- 액세스 권한을 부여할 사용자에 대한 정보를 수집합니다. 이 정보는 사용자의 Microsoft 계정 정보나 Active Directory 작업 계정 정보가 될 수 있습니다.
- 이 프로세스에서는 구독의 일부로 제공된 템플릿 이미지 중 하나를 사용할 예정이거나 사용할 템플릿 이미지를 이미 업로드했다고 가정합니다. 다른 템플릿 이미지를 업로드해야 하는 경우 템플릿 이미지 페이지에서 그렇게 할 수 있습니다. 단지 **템플릿 이미지 업로드**를 클릭하고 마법사의 단계를 따르면 됩니다. 
- Office 365 ProPlus 이미지를 사용하려고 하나요? [여기](remoteapp-officesubscription.md)서 정보를 확인하세요.
- 사용자 지정 앱이나 LOB 프로그램을 제공하려는 경우 새로운 [이미지](remoteapp-imageoptions.md)를 만든 다음 클라우드 컬렉션에서 사용합니다.
- VNET에 연결해야 하는지 여부를 파악합니다. VNET에 연결하려는 경우 [크기 조정 지침](remoteapp-vnetsizing.md)을 충족하는지와 [RemoteApp에 연결할 수 있는지](remoteapp-vnet.md) 확인합니다. 자세한 내용을 보려면 [VNET 계획 문서](remoteapp-planvnet.md)를 확인하세요.
- VNET을 사용하는 경우 로컬 Active Directory 도메인에 연결할지 여부를 결정합니다.

## 1단계: VNET과 관계 없이 클라우드 컬렉션 만들기##


다음 단계에 따라 **클라우드 전용 컬렉션을 만듭니다**.

1. 관리 포털에서 RemoteApp 페이지로 이동합니다.
2. **새로 만들기 > 빠른 생성**을 클릭합니다.
3. 컬렉션의 이름을 입력하고 지역을 선택합니다.
4. 사용할 계획(표준 또는 기본)을 선택합니다.
5. 이 컬렉션에 사용할 템플릿을 선택합니다. 

	**팁:** RemoteApp의 구독은 Office 365 또는 Office 2013(평가판 사용) 프로그램, 게시된 항목(예: Word) 및 기타 게시할 항목을 포함하는 [템플릿 이미지](remoteapp-images.md)와 함께 제공됩니다. 또한 새로운 [이미지](remoteapp-imageoptions.md)를 만든 다음 클라우드 컬렉션에서 사용합니다.


1. **RemoteApp 컬렉션 만들기**를 클릭합니다.
	
	**중요:** 컬렉션을 프로비전하는 데 최대 30분 정도 걸릴 수 있습니다.

Azure RemoteApp 컬렉션을 만든 후에는 컬렉션의 이름을 두 번 클릭합니다. 그러면 **빠른 시작** 페이지가 나타나며, 여기서 컬렉션 구성을 완료합니다.

다음 단계에 따라 **클라우드 + VNET 컬렉션**을 만듭니다.

1. 관리 포털에서 Azure RemoteApp 페이지로 이동합니다.
2. **새로 만들기** > **VNET으로 만들기**를 클릭합니다.
3. 컬렉션의 이름을 입력합니다.
4. 사용할 계획(표준 또는 기본)을 선택합니다.
5. 이미 만든 VNET을 선택합니다. 방법을 모르시나요? 현재 해당 단계는 [하이브리드](remoteapp-create-hybrid-deployment.md) 항목에 나와 있습니다.
6. 컬렉션을 도메인에 연결할지 여부를 결정합니다. 연결하려는 경우 AD Connect를 사용하여 Azure AD 및 Active Directory 환경을 통합해야 합니다. 이 내용은 아래의 **2단계**에 나옵니다.
6. **RemoteApp 컬렉션 만들기**를 클릭합니다.


## 2단계: Active Directory 디렉터리 동기화 구성(선택 사항) ##

Active Directory를 사용하려는 경우 Azure RemoteApp에서 Azure Active Directory 테넌트와 사용자, 연락처 및 암호를 동기화하려면 Azure Active Directory와 온-프레미스 Active Directory 간의 디렉터리 동기화가 필요합니다. 계획에 대한 내용은 [Azure RemoteApp에 대해 Active Directory 구성](remoteapp-ad.md)을 참조하세요. [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx)로 직접 이동하여 자세한 내용을 확인할 수도 있습니다.

## 3단계: 앱 게시 ##

Azure RemoteApp 앱은 사용자에게 제공하는 앱 또는 프로그램입니다. 이 프로그램은 컬렉션에 대해 업로드한 템플릿 이미지에 있습니다. 사용자가 앱에 액세스할 때 앱이 로컬 환경에서 실행하는 것처럼 보이지만 실제로는 Azure의 가상 컴퓨터에서 실행됩니다.

사용자가 앱에 액세스할 수 있도록 하려면 해당 앱을 게시해야 합니다. 앱을 게시하면 사용자가 원격 데스크톱 클라이언트를 통해 해당 앱에 액세스할 수 있습니다.
 
Azure RemoteApp 컬렉션에 여러 앱을 게시할 수 있습니다. 게시 페이지에서 **게시**를 클릭하여 프로그램을 추가합니다. 템플릿 이미지의 **시작** 메뉴에서 또는 앱의 템플릿 이미지에 경로를 지정하여 게시할 수 있습니다. **시작** 메뉴에서 추가하도록 선택하는 경우 게시할 앱을 선택합니다. 앱의 경로를 제공하도록 선택한 경우 앱의 이름 및 템플릿 이미지에서 앱이 설치된 경로를 입력합니다.

## 4단계: 사용자 액세스 구성 ##

컬렉션을 만들었으므로 원격 리소스를 사용할 수 있는 사용자를 추가해야 합니다. Active Directory를 사용하는 경우 액세스 권한을 제공하는 사용자는 이 컬렉션을 만드는 데 사용한 구독과 연결된 Active Directory 테넌트에 있어야 합니다.

1.	빠른 시작 페이지에서 **사용자 액세스 구성**을 클릭합니다. 
2.	액세스 권한을 부여할 Microsoft 계정이나 Active Directory의 작업 계정을 입력합니다.

	**참고:**

	"user@domain.com" 형식을 사용해야 합니다.

	컬렉션에서 Office 365 ProPlus를 사용하는 경우 사용자에 대해 Active Directory ID를 사용해야 합니다. 그러면 라이선스 유효성 검사에 도움이 됩니다.

3.	사용자가 확인되면 **저장**을 클릭합니다.


## 다음 단계 ##

Azure RemoteApp 클라우드 컬렉션을 만들고 배포했습니다. 다음 단계는 사용자가 원격 데스크톱 클라이언트를 다운로드하여 설치하도록 설정하는 것입니다. Azure RemoteApp 빠른 시작 페이지에서 클라이언트의 URL을 찾을 수 있습니다. 그런 다음 사용자가 클라이언트에 로그인하여 게시된 앱에 액세스하도록 합니다.

### 의견 보내기 
이 기사에 대한 등급을 매기고 아래에 의견을 다는 것은 물론 문서를 직접 변경할 수 있다는 사실을 알고 계셨나요? 누락된 부분이 있나요? 잘못된 부분이 있나요? 혼동을 줄 수 있는 부분이 있나요? 위로 스크롤하여 **GitHub에서 편집**을 클릭하면 변경할 수 있습니다. 당사에서 변경 사항을 검토하고 승인하면 변경 및 개선 사항을 바로 여기서 확인할 수 있습니다.

<!---HONumber=AcomDC_0211_2016-->