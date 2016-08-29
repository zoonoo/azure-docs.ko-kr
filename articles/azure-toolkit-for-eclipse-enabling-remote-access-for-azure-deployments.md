<properties
    pageTitle="Eclipse에서 Azure 배포에 대한 원격 액세스를 사용하도록 설정"
    description="Eclipse용 Azure 도구 키트를 사용하여 Azure 배포에 대한 원격 액세스를 사용하도록 설정하는 방법에 알아봅니다."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# Eclipse에서 Azure 배포에 대한 원격 액세스를 사용하도록 설정

배포를 해결하기 위해 원격 액세스를 활성화하고 사용하여 배포를 호스팅하는 가상 컴퓨터에 연결합니다. 원격 액세스 기능은 원격 데스크톱 프로토콜(RDP)에 의존합니다. Azure에 게시한 후 배포에 대한 원격 액세스를 구성할 수 있거나 Windows 운영 체제와 Eclipse를 사용하는 경우 Azure에 게시하기 전에 원격 액세스를 구성할 수 있습니다. Azure에서 배포의 가상 컴퓨터에 연결하기 위해 운영 체제와 호환되는 원격 데스크톱 클라이언트가 필요합니다.

## Azure에 배포하기 전에 원격 액세스를 사용하도록 설정하는 방법

> [AZURE.NOTE] Azure에 응용 프로그램을 배포하기 전에 원격 액세스를 사용하도록 설정하려면 Windows에서 Eclipse를 실행해야 합니다.

다음 이미지는 원격 액세스를 활성화하는 데 사용되는 **원격 액세스** 속성 대화 상자를 보여 줍니다.

![][ic719494]

**원격 액세스** 속성 대화 상자를 표시하는 두 가지 방법이 있습니다.

* **Azure에 게시** 대화 상자의 **원격 액세스** 섹션에서 **고급** 링크를 클릭합니다.
* Azure 프로젝트의 **속성** 대화 상자를 엽니다.

새 Azure 배포 프로젝트를 만드는 경우 프로젝트는 기본적으로 활성화된 원격 액세스가 없습니다. 그러나 **Azure에 게시** 대화 상자에서 사용자 이름 및 암호를 지정하여 쉽게 원격 액세스를 활성화할 수 있습니다. 원격 액세스 암호는 X.509 인증서를 사용하여 암호화됩니다. 사용자 인증서를 제공하지 않는 경우 암호화는 Eclipse용 Azure 플러그 인과 함께 제공되는 자체 서명된 인증서를 사용합니다. 이 자체 서명된 인증서는 Azure 프로젝트의 **cert** 폴더에 있으며 공용 인증서 파일(SampleRemoteAccessPublic.cer) 및 개인 정보 교환(PFX) 인증서 파일(SampleRemoteAccessPrivate.pfx) 모두로 저장됩니다. 후자는 인증서에 대한 개인 키를 포함하고 기본 암호, **Password1**을 갖습니다. 그러나 이 암호는 공공 정보이므로 기본 인증서는 프로덕션 배포용이 아닌 학습 용도로만 사용되어야 합니다. 따라서 학습 목적 이외로 배포를 위한 원격 세션을 사용하도록 설정하려는 경우 **Azure에 게시** 대화 상자에서 **고급** 링크를 클릭하여 고유 인증서를 지정해야 합니다. Azure에서 사용자 암호를 해독할 수 있도록 Azure 관리 포털 내의 호스티드 서비스에 인증서의 PFX 버전을 업로드해야 합니다.

이 자습서의 나머지 부분에서는 사용하지 않도록 설정된 원격 액세스를 사용하여 처음에 만든 Azure 배포 프로젝트에 대한 원격 액세스를 사용하도록 설정하는 방법을 보여 줍니다. 이 자습서의 목적을 위해 새 자체 서명된 인증서를 만들 예정이며 해당 .pfx 파일은 사용자가 선택한 암호를 갖게 됩니다. 인증 기관에서 발급한 인증서를 사용할 수도 있습니다.

## Azure에 배포한 후에 원격 액세스를 사용하도록 설정하는 방법

Azure에 배포한 후 원격 액세스를 사용하도록 설정하려면 다음 단계를 사용합니다.

1. Azure 계정을 사용하여 Azure 관리 포털에 로그인합니다.
1. **클라우드 서비스** 목록에서 배포된 클라우드 서비스를 선택합니다.
1. 클라우드 서비스 웹 페이지에서 **구성** 링크를 클릭합니다.
1. 구성 페이지의 맨 아래에서 **원격** 링크를 클릭합니다.
1. 팝업 대화 상자가 나타나는 경우:
    * 원격 액세스를 사용하도록 설정하려는 역할을 지정합니다.
    * 클릭하여 **원격 데스크톱 사용** 확인란을 선택합니다.
    * 원격 액세스에 대해 사용하려는 사용자 이름 및 암호를 지정합니다.
    * 사용할 인증서를 선택합니다.
1. **확인**을 클릭합니다.

구성 변경이 진행 중임을 나타내는 메시지가 표시되며 완료하려면 몇 분 정도 소요될 수 있습니다. 구성 변경이 완료된 후 이 문서의 뒷부분에 나오는 **원격으로 로그인하려면** 섹션의 단계를 따릅니다.
	
## 패키지에서 원격 액세스를 사용하도록 설정하는 방법

1. Eclipse의 Project Explorer 창에서 Azure 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Properties**를 클릭합니다.

1. **Properties** 대화 상자에서 왼쪽 창의 **Azure**를 확장하고 **Remote Access**를 클릭합니다.

1. **Remote Access** 대화 상자에서 **Enable all roles to accept Remote Desktop Connections with these login credentials**이 선택되었는지 확인합니다.

1. 원격 데스크톱 연결에 대한 사용자 이름을 지정합니다.

1. 사용자에 대한 암호를 지정하고 확인합니다. 이 대화 상자에 설정된 사용자 이름 및 암호 값은 원격 데스크톱 연결을 설정할 때 사용됩니다. (이것은 PFX 암호와 다른 별도 암호입니다.)

1. 사용자 계정에 대한 만료 날짜를 지정합니다.

1. **New**를 클릭하여 자체 서명된 새로운 인증서를 만듭니다. (또는 **Workspace** 또는 **FileSystem** 단추를 통해 각각 작업 영역이나 파일 시스템에서 인증서를 선택할 수 있지만 이 자습서의 목적을 위해 새 인증서를 만듭니다.)

    * **New Certificate** 대화 상자에서 PFX 파일에서 사용할 암호를 지정하고 확인합니다.

    * **Name (CN)**에서 제공된 값을 사용하거나 사용자 지정 이름을 사용합니다.

    * .cer 형식으로 새 인증서가 저장될 경로 및 파일 이름을 지정합니다. 이 단계와 다음 단계의 경우 Azure 프로젝트의 **cert** 폴더를 사용할 수 있지만 다른 위치를 선택할 수도 있습니다. 이 자습서의 목적을 위해 **c:\\mycert\\mycert.cer**을 사용합니다. (계속하기 전에 **c:\\mycert** 폴더를 만들거나 원하는 경우 기존 폴더를 사용합니다.)

    * .cer 형식으로 새 인증서 및 해당 개인 키가 저장될 경로 및 파일 이름을 지정합니다. 이 자습서의 목적을 위해 **c:\\mycert\\mycert.pfx**를 사용합니다. **새 인증서** 대화 상자는 다음과 유사합니다(**c:\\mycert**를 사용하지 않은 경우 폴더 경로 업데이트).

        ![][ic712275]

    * **OK**를 클릭하여 **New Certificate** 대화 상자를 닫습니다.

1. **원격 액세스** 대화 상자는 다음과 유사합니다. </p>

    ![][ic719495]

1. **OK**를 클릭하여 **Remote Access** 대화 상자를 닫습니다.
	
클라우드 배포용으로 설정된 빌드로 응용 프로그램을 다시 빌드합니다.

## 원격으로 로그인하려면

역할 인스턴스가 준비되면 응용 프로그램을 호스팅하는 가상 컴퓨터에 원격으로 로그인할 수 있습니다.

* Azure에 배포하는 동안 Windows에서 Eclipse를 사용하고 **Start remote desktop on deploy** 옵션을 선택한 경우 배포를 시작할 때 원격 데스크톱 연결 로그온 화면이 나타납니다. 사용자 이름 및 암호를 묻는 메시지가 나타나면 원격 사용자에 대해 지정한 값을 입력하고 로그인할 수 있게 됩니다.

* 원격으로 로그인하는 또 다른 방법은 <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Azure 관리 포털</a>을 통해서 입니다.

    * Azure 관리 포털의 **클라우드 서비스** 보기 내에서 **인스턴스**를 클릭하고 특정 인스턴스를 클릭한 다음 **연결** 단추를 클릭합니다. **연결** 단추가 명령 모음에서 다음과 같이 나타납니다.

        ![][ic659273]

    * **연결** 단추를 클릭한 후 RDP 파일을 열라는 메시지가 나타납니다 파일을 열고 프롬프트를 따릅니다. (이 파일을 로컬 컴퓨터에 저장한 다음 두 번 클릭하여 파일을 실행하여 관리 포털로 먼저 이동하지 않고도 가상 컴퓨터에 원격으로 로그인할 수도 있습니다.)

    * 사용자 이름 및 암호를 묻는 메시지가 나타나면 원격 사용자에 대해 지정한 값을 입력하고 로그인할 수 있게 됩니다.

> [AZURE.NOTE] 비 Windows 운영 체제에 있는 경우 운영 체제와 호환되는 원격 데스크톱 클라이언트를 사용하고 다운로드한 RDP 파일의 설정을 사용하여 해당 클라이언트를 구성하는 단계를 수행해야 합니다.

## 참고 항목

[Eclipse용 Azure 도구 키트][]

[Eclipse에서 Azure용 Hello World 응용 프로그램 만들기][]

[Eclipse용 Azure 도구 키트 설치][]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터][]를 참조하세요.

<!-- URL List -->

[Azure Java 개발자 센터]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Eclipse용 Azure 도구 키트]: http://go.microsoft.com/fwlink/?LinkID=699529
[Eclipse에서 Azure용 Hello World 응용 프로그램 만들기]: http://go.microsoft.com/fwlink/?LinkID=699533
[Eclipse용 Azure 도구 키트 설치]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

<!---HONumber=AcomDC_0817_2016-->