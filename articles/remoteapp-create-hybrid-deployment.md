<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="RemoteApp의 하이브리드 배포를 만드는 방법" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/12/2014" ms.author="elizapo" ms.manager="kathyw" />

#RemoteApp의 하이브리드 배포를 만드는 방법

다음과 같은 두 가지 종류의 RemoteApp 배포가 있습니다. 

- 클라우드: 완벽히 Azure에서 상주하며 Azure 관리 포털의 **빠른 생성** 옵션을 사용하여 생성됩니다.  
- 하이브리드: 온-프레미스 액세스를 위한 가상 네트워크를 포함하며 관리 포털의 **VPN으로 만들기** 옵션을 사용하여 생성됩니다.

이 자습서에서는 하이브리드 배포를 만드는 프로세스를 단계별로 안내합니다. 이 프로세스는 다음 7개의 단계로 구성됩니다. 

1.	[RemoteApp용 사용자 지정 템플릿 이미지](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-custom-image/)를 만듭니다.
2.	RemoteApp 서비스를 만듭니다.
2.	가상 네트워크에 연결합니다.
3.	템플릿 이미지를 연결합니다.
4.	디렉터리 동기화를 구성합니다. RemoteApp은 1) 암호 동기화 옵션을 포함하여 DirSync를 구성하거나 2) 암호 동기화 옵션을 포함하지 않지만 AD FS로 페더레이션되는 도메인을 사용하여 DirSync를 구성하는 방식으로 Azure Active Directory와 통합해야 합니다.
5.	RemoteApp 프로그램을 게시합니다.
6.	사용자 액세스를 구성합니다.

**시작하기 전에**

서비스를 만들기 전에 다음을 수행해야 합니다.

- RemoteApp의 미리 보기에 등록합니다. [http://azure.microsoft.com/ko-kr/services/remoteapp/](http://azure.microsoft.com/ko-kr/services/remoteapp/)에서 등록할 수 있습니다.
- RemoteApp 서비스 계정으로 사용할 Active Directory의 사용자 계정을 만듭니다. 이 계정의 권한은 도메인에 컴퓨터를 가입시킬 수 있는 권한만으로 제한합니다.
- 온-프레미스 네트워크에 대한 IP 주소 정보 및 VPN 장치 정보를 수집합니다.
- [Azure PowerShell](http://azure.microsoft.com/ko-kr/documentation/articles/install-configure-powershell/) 모듈을 설치합니다.
- 액세스 권한을 부여할 사용자 및 그룹에 대한 정보를 수집합니다. 이 정보는 사용자 또는 그룹의 Microsoft 계정 정보나 Active Directory 작업 계정 정보가 될 수 있습니다.
- 템플릿 이미지를 만듭니다. RemoteApp 템플릿 이미지는 사용자를 위해 게시하려는 앱 및 프로그램을 포함합니다. 단계에 대한 자세한 내용은 [RemoteApp용 사용자 지정 템플릿 이미지를 만드는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-custom-image/)을 참조하세요. 






## **1단계: RemoteApp 서비스 만들기** ##



1. [Azure 관리 포털](http://manage.windowsazure.com)에서, RemoteApp 페이지로 이동합니다.
2. **새로 만들기 > VPN으로 만들기**를 클릭합니다.
3. 서비스의 이름을 입력하고 **RemoteApp 서비스 만들기**를 클릭합니다.

RemoteApp 서비스를 만든 후 RemoteApp **빠른 시작** 페이지로 이동하여 설정 단계를 진행합니다.

## **2단계: 가상 네트워크에 연결** ##

가상 네트워크를 사용하여 사용자는 RemoteApp 원격 리소스를 통해 로컬 네트워크의 데이터에 액세스할 수 있습니다. 가상 네트워크 링크를 구성할 수 있는 단계는 다음과 같이 4가지입니다.

1. 빠른 시작 페이지에서 **RemoteApp 가상 네트워크 연결**을 클릭합니다. 
2. 새로운 가상 네트워크를 만들지 아니면 기존 네트워크를 사용할지 여부를 선택합니다. 이 자습서에서는 새 네트워크를 만듭니다.
3. 새로운 네트워크에 대한 다음 정보를 제공합니다.  
      - 이름
      - 가상 네트워크 주소 공간
      - 로컬 주소 공간
      - DNS 서버 IP 주소
      - VPN IP 주소

자세한 내용은 [관리 포털에서 사이트 간 VPN 구성](http://msdn.microsoft.com/library/azure/dn133795.aspx)을 참조하세요.

4. 그런 다음 빠른 시작 페이지로 돌아가서 **스크립트 가져오기**를 클릭하여 스크립트를 다운로드해서 방금 만든 가상 네트워크에 연결할 VPN 장치를 구성합니다. VPN 장치에 대한 다음 정보가 필요합니다. 
	- 공급업체
	- 플랫폼
	- 운영 체제

스크립트를 저장하고 VPN 장치에서 실행합니다. 

	**참고:** 이 스크립트를 실행한 후 가상 네트워크는 준비 상태로 전환되며 대략 5-7분이 소요될 수 있습니다. 네트워크가 준비될 때까지 네트워크를 사용할 수 없습니다.

5. 마지막으로 빠른 시작 페이지에서 다시 **로컬 도메인 가입**을 클릭합니다. 로컬 Active Directory 도메인에 RemoteApp 서비스 계정을 추가합니다. 도메인 이름, 조직 구성 단위, 서비스 계정 사용자 이름 및 암호가 필요합니다.


## **3단계: RemoteApp 템플릿 이미지에 연결** ##

RemoteApp 템플릿 이미지에는 사용자와 공유할 프로그램이 포함됩니다. [RemoteApp용 사용자 지정 템플릿 이미지를 만드는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-create-custom-image/)의 지침에 따라 직접 만든 새 템플릿 이미지를 업로드하거나, Azure에 이미 업로드된 기존 이미지에 연결할 수 있습니다.

새 이미지를 업로드하는 경우 이름을 입력하고 이미지의 위치를 선택해야 합니다. 마법사의 다음 페이지에는 PowerShell cmdlet 집합이 표시됩니다. 관리자 권한 Windows PowerShell 프롬프트에서 이러한 cmdlet을 복사하고 실행하여 지정된 이미지를 업로드합니다.

기존 템플릿 이미지에 연결하는 경우 단지 이미지 이름, 위치 및 연결된 Azure 구독을 지정하기만 하면 됩니다.



## **4단계: Active Directory 디렉터리 동기화 구성** ##

RemoteApp은 1) 암호 동기화 옵션을 포함하여 DirSync를 구성하거나 2) 암호 동기화 옵션을 포함하지 않지만 AD FS로 페더레이션되는 도메인을 사용하여 DirSync를 구성하는 방식으로 Azure Active Directory와 통합해야 합니다. 계획 정보 및 자세한 단계에 대해서는 [디렉터리 동기화 로드맵](http://msdn.microsoft.com//library/azure/hh967642.aspx)을 참조하세요.

## **5단계: RemoteApp 프로그램 게시** ##

RemoteApp 프로그램은 사용자에게 제공하는 앱 또는 프로그램입니다. 이 프로그램은 서비스를 위해 업로드하는 템플릿 이미지에 있습니다. 사용자가 RemoteApp 프로그램에 액세스할 때 프로그램이 로컬 환경에서 실행하는 것처럼 보이지만 실제로는 Azure에서 실행됩니다. 

사용자가 RemoteApp 프로그램에 액세스할 수 있으려면 최종 사용자 피드에 사용자가 Azure 포털을 통해 액세스할 수 있는 프로그램 목록을 게시해야 합니다.
 
RemoteApp 서비스에 여러 프로그램을 게시할 수 있습니다. RemoteApp 프로그램 페이지에서 **게시**를 클릭하여 프로그램을 추가합니다. 템플릿 이미지의 시작 메뉴에서 또는 프로그램의 템플릿 이미지에 경로를 지정하여 게시할 수 있습니다. 시작 메뉴에서 추가하도록 선택하는 경우 게시할 프로그램을 선택합니다. 프로그램의 경로를 제공하도록 선택한 경우 프로그램의 이름 및 템플릿 이미지에서 프로그램이 설치되는 경로를 입력합니다.

## **6단계: 사용자 액세스 구성** ##

RemoteApp 서비스를 만들었으므로 원격 리소스를 사용할 수 있는 사용자 및 그룹을 추가해야 합니다. 액세스 권한을 제공하는 사용자 또는 그룹은 이 RemoteApp 서비스를 만드는 데 사용한 구독과 연결된 Active Directory 테넌트에 존재해야 합니다.

1.	빠른 시작 페이지에서 **사용자 액세스 구성**을 클릭합니다. 
2.	액세스 권한을 부여할 Microsoft 계정이나 Active Directory의 작업 계정 또는 그룹 이름을 입력합니다.

사용자의 경우 "user@domain.com" 형식을 사용해야 합니다. 그룹의 경우 그룹 이름을 입력합니다.

3.	사용자 또는 그룹이 확인되면 **저장**을 클릭합니다.


## 다음 단계 ##
RemoteApp 하이브리드 배포를 만들고 배포했습니다. 다음 단계는 사용자가 원격 데스크톱 클라이언트를 다운로드하여 설치하도록 설정하는 것입니다. RemoteApp 빠른 시작 페이지에서 클라이언트의 URL을 찾을 수 있습니다. 그런 다음 사용자가 클라이언트에 로그인하여 게시된 RemoteApp 프로그램에 액세스하도록 합니다.


