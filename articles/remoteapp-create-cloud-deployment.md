<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# RemoteApp의 클라우드 배포를 만드는 방법

다음과 같은 두 가지 종류의 RemoteApp 배포가 있습니다.

-   클라우드: 완벽히 Azure에서 상주하며 Azure 관리 포털의 **빠른 만들기** 옵션을 사용하여 생성됩니다.
-   하이브리드: 온-프레미스 액세스를 위한 가상 네트워크를 포함하며 관리 포털의 **VPN으로 만들기** 옵션을 사용하여 생성됩니다.

이 자습서에서는 클라우드 배포를 만드는 프로세스를 단계별로 안내합니다. 이 프로세스는 다음 4개의 단계로 구성됩니다.

1.  RemoteApp 서비스를 만듭니다.
2.  선택적으로 디렉터리 동기화를 구성합니다. 이 작업은 RemoteApp에서 온-프레미스 Active Directory에서 Azure Active Directory 테넌트로 사용자, 그룹, 연락처 및 암호를 동기화하는 데 필요합니다.
3.  RemoteApp 프로그램을 게시합니다.
4.  사용자 액세스를 구성합니다.

**시작하기 전에**

서비스를 만들기 전에 다음을 수행해야 합니다.

-   RemoteApp의 미리 보기에 등록합니다. [][]<http://azure.microsoft.com/ko-kr/services/remoteapp/></a>에서 등록할 수 있습니다.
-   액세스 권한을 부여할 사용자 및 그룹에 대한 정보를 수집합니다. 이 정보는 사용자 또는 그룹의 Microsoft 계정 정보나 Active Directory 조직 계정 정보가 될 수 있습니다.
-   이 프로세스에서는 구독의 일부로 제공된 템플릿 이미지를 사용할 예정이거나 사용할 템플릿 이미지를 이미 업로드했다고 가정합니다. 다른 템플릿 이미지를 업로드해야 하는 경우 템플릿 이미지 페이지에서 그렇게 할 수 있습니다. 단지 **템플릿 이미지 업로드**를 클릭하고 마법사의 단계를 따르면 됩니다.

## **1단계: RemoteApp 서비스 만들기**

1.  [Windows Azure 관리 포털][Windows Azure 관리 포털]에서, RemoteApp 페이지로 이동합니다.
2.  **새로 만들기 \> 빠른 생성**을 클릭합니다.

3.  서비스의 이름을 입력하고 지역을 선택합니다.
4.  이 서비스를 만드는 데 사용할 구독을 선택합니다.
5.  이 서비스에 사용할 템플릿을 선택합니다.

    **팁:** RemoteApp의 구독은 Office 2013 프로그램, 게시된 항목(예: Word) 및 기타 게시할 항목을 포함하는 템플릿 이미지와 함께 제공됩니다.

6.  **RemoteApp 서비스 만들기**를 클릭합니다.

    **중요:** 서비스를 제공하는 데 최대 30분 정도 걸릴 수 있습니다.

RemoteApp 서비스를 만든 후 RemoteApp **빠른 시작** 페이지로 이동하여 설정 단계를 진행합니다.

## **2단계: Active Directory 디렉터리 동기화 구성(선택 사항)**

Active Directory를 사용하는 경우 RemoteApp에서는 Azure Active Directory 테넌트와 사용자, 그룹, 연락처 및 암호를 동기화하려면 Azure Active Directory와 온-프레미스 Active Directory 간의 디렉터리 동기화가 필요합니다. 계획 정보 및 자세한 단계에 대해서는 [디렉터리 동기화 로드맵][디렉터리 동기화 로드맵]을 참조하세요.

## **3단계: RemoteApp 프로그램 게시**

RemoteApp 프로그램은 사용자에게 제공하는 앱 또는 프로그램입니다. 이 프로그램은 서비스를 위해 업로드하는 템플릿 이미지에 있습니다. 사용자가 RemoteApp 프로그램에 액세스할 때 프로그램이 로컬 환경에서 실행하는 것처럼 보이지만 실제로는 Azure에서 실행됩니다.

사용자가 RemoteApp 프로그램에 액세스할 수 있으려면 최종 사용자 피드에 사용자가 Azure 포털을 통해 액세스할 수 있는 프로그램 목록을 게시해야 합니다.

RemoteApp 서비스에 여러 프로그램을 게시할 수 있습니다. RemoteApp 프로그램 페이지에서 **게시**를 클릭하여 프로그램을 추가합니다. 템플릿 이미지의 시작 메뉴에서 또는 프로그램의 템플릿 이미지에 경로를 지정하여 게시할 수 있습니다. 시작 메뉴에서 추가하도록 선택하는 경우 게시할 프로그램을 선택합니다. 프로그램의 경로를 제공하도록 선택한 경우 프로그램의 이름 및 템플릿 이미지에서 프로그램이 설치되는 경로를 입력합니다.

## **4단계: 사용자 액세스 구성**

RemoteApp 서비스를 만들었으므로 원격 리소스를 사용할 수 있는 사용자 및 그룹을 추가해야 합니다. Active Directory를 사용하는 경우 액세스 권한을 제공하는 사용자 또는 그룹은 이 RemoteApp 서비스를 만드는 데 사용한 구독과 연결된 Active Directory 테넌트에 있어야 합니다.

1.  빠른 시작 페이지에서 **사용자 액세스 구성**을 클릭합니다.
2.  액세스 권한을 부여할 Microsoft 계정이나 Active Directory의 조직 계정 또는 그룹 이름을 입력합니다.

    사용자의 경우 “<user@domain.com>” 형식을 사용해야 합니다. 그룹의 경우 그룹 이름을 입력합니다.

3.  사용자 또는 그룹이 확인되면 **저장**을 클릭합니다.

## 다음 단계

RemoteApp 클라우드 배포를 만들고 배포했습니다. 다음 단계는 사용자가 원격 데스크톱 클라이언트를 다운로드하여 설치하도록 설정하는 것입니다. RemoteApp 빠른 시작 페이지에서 클라이언트의 URL을 찾을 수 있습니다. 그런 다음, 사용자가 Azure에 로그인하여 게시된 RemoteApp 프로그램에 액세스하도록 합니다.

  []: http://azure.microsoft.com/ko-kr/services/remoteapp/
  [Windows Azure 관리 포털]: http://manage.windowsazure.com
  [디렉터리 동기화 로드맵]: http://msdn.microsoft.com/ko-kr/library/azure/hh967642.aspx
