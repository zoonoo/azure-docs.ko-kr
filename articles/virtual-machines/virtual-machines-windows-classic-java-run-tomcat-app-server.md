<properties
	pageTitle="가상 컴퓨터의 Tomcat | Microsoft Azure"
	description="이 자습서에서는 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, Windows 가상 컴퓨터를 만들고 Apache Tomcat 응용 프로그램 서버를 실행하도록 구성하는 방법을 보여 줍니다."
	services="virtual-machines-windows"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
    tags="azure-service-management" />

<tags
	ms.service="virtual-machines-windows"
	ms.workload="web"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>

# 클래식 배포 모델을 사용하여 만든 가상 컴퓨터에서 Java 응용 프로그램 서버를 실행하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Azure에서 가상 컴퓨터를 사용하여 서버 기능을 제공할 수 있습니다. 한 예로, Java 응용 프로그램 서버(예: Apache Tomcat)를 호스트하도록 Azure에서 실행되는 가상 컴퓨터를 구성할 수 있습니다. 이 가이드를 완료하고 나면 Azure에서 실행되는 가상 컴퓨터를 만들고 Java 응용 프로그램 서버에서 실행하도록 구성하는 방법을 이해할 수 있게 됩니다.

다음 내용을 배웁니다.

* Java 개발 키트(JDK)가 이미 설치된 가상 컴퓨터를 만드는 방법
* 가상 컴퓨터에 원격으로 로그인하는 방법
* 가상 컴퓨터에 Java 응용 프로그램 서버를 설치하는 방법
* 가상 컴퓨터의 끝점을 만드는 방법
* 방화벽에서 응용 프로그램 서버용 포트를 여는 방법

이 자습서에서는 가상 컴퓨터에 Apache Tomcat 응용 프로그램 서버를 설치합니다. 설치가 완료되면 다음과 같은 Tomcat 설치 화면이 나타납니다.

![Apache Tomcat을 실행하는 가상 컴퓨터][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## 가상 컴퓨터를 만드는 방법

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **새로 만들기**를 클릭하고, **계산**, **가상 컴퓨터**, **갤러리에서**를 차례로 클릭합니다.
3. **가상 컴퓨터 이미지 선택** 대화 상자에서 **JDK 7 Windows Server 2012**를 선택합니다. JDK 7에서 실행할 준비가 되지 않은 레거시 응용 프로그램이 있는 경우 **JDK 6 Windows Server 2012**를 사용할 수 있습니다.
4. **다음**을 클릭합니다.
5. **가상 컴퓨터 구성** 대화 상자에서 다음을 수행합니다.
    1. 가상 컴퓨터의 이름을 지정합니다.
    2. 가상 컴퓨터에 사용할 크기를 지정합니다.
    3. **사용자 이름** 필드에 관리자의 이름을 입력합니다. 다음에 입력하는 이름 및 암호는 가상 컴퓨터에 원격으로 로그인할 때 사용해야 하니 기억해 두시기 바랍니다.
    4. **새 암호** 필드에 암호를 입력하고 **확인** 필드에 다시 입력합니다. 이 암호는 관리자 계정 암호입니다.
    5. **다음**을 클릭합니다.
6. 다음 **가상 컴퓨터 구성** 대화 상자에서 다음을 수행합니다.
    1. **클라우드 서비스**의 경우 기본값인 **새 클라우드 서비스 만들기**를 사용합니다.
    2. **클라우드 서비스 DNS 이름** 값은 cloudapp.net에서 고유해야 합니다. 필요한 경우 Azure에서 고유한 이름이 되도록 수정합니다.
    2. 지역, 선호도 그룹 또는 가상 네트워크를 지정합니다. 이 자습서에서는 지역(예: **미국 서부**)을 지정합니다.
    2. **저장소 계정** 상자에서 **자동으로 생성된 저장소 계정 사용**을 선택합니다.
    3. **가용성 집합**에서 **(없음)**을 선택합니다.
    4. **다음**을 클릭합니다.
7. 마지막 **가상 컴퓨터 구성** 대화 상자에서 다음을 수행합니다.
    1. 기본 끝점 항목을 그대로 사용합니다.
    2. **완료**를 클릭합니다.

## 가상 컴퓨터에 원격으로 로그인하려면 다음을 수행합니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그온합니다.
2. **가상 컴퓨터**를 클릭합니다.
3. 로그인할 가상 컴퓨터의 이름을 클릭합니다.
4. 가상 컴퓨터가 시작되면 페이지 아래쪽의 팝업 메뉴를 통해 연결이 가능합니다.
5. **Connect**를 클릭합니다.
6. 가상 컴퓨터에 연결해야 한다는 메시지에 응답합니다. 이:때 연결 세부 정보가 포함된 .rdp 파일을 열거나 저장해야 합니다. .rdp 파일의 첫 줄 끝 부분으로 url:port를 복사한 다음 원격 로그인 응용 프로그램에 붙여 넣어야 할 수 있습니다.

## 가상 컴퓨터에 Java 응용 프로그램 서버를 설치하는 방법

Java 응용 프로그램 서버를 가상 컴퓨터로 복사하거나 설치 관리자를 통해 Java 응용 프로그램 서버를 설치할 수 있습니다.

이 자습서에서는 Tomcat을 설치합니다.

1. 가상 컴퓨터에 로그인하는 경우 [Apache Tomcat](http://tomcat.apache.org/download-70.cgi)에 대한 브라우저 세션을 엽니다.
2. **32-bit/64-bit Windows Service Installer** 링크를 두 번 클릭합니다. 이 기술을 사용하여 Tomcat이 Windows 서비스로 설치됩니다.
3. 메시지가 표시되면 설치 관리자를 실행하도록 선택합니다.
4. **Apache Tomcat Setup** 마법사에서 프롬프트에 따라 Tomcat을 설치합니다. 이 자습서에서는 기본값을 그대로 사용해도 됩니다. **Completing the Apache Tomcat Setup Wizard** 대화 상자가 표시되면 옵션으로 제공되는 **Run Apache Tomcat**을 선택하여 Tomcat을 바로 시작할 수 있습니다. **Finish**를 클릭하여 Tomcat 설치 프로세스를 완료합니다.

## Tomcat 시작 방법
**Completing the Apache Tomcat Setup Wizard** 대화 상자에서 Tomcat을 실행하도록 선택하지 않은 경우 가상 컴퓨터에서 명령 프롬프트를 열고 **net start Tomcat7**을 실행하여 시작합니다.

이제 가상 컴퓨터의 브라우저를 실행하여 열고 <http://localhost:8080>을 열면 Tomcat이 실행되는 것을 볼 수 있습니다.

Tomcat이 외부 컴퓨터에서 실행되는 것을 보려면 끝점을 만들고 포트를 열어야 합니다.

## 가상 컴퓨터의 끝점을 만드는 방법
1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. **가상 컴퓨터**를 클릭합니다.
3. Java 응용 프로그램 서버를 실행하는 가상 컴퓨터의 이름을 클릭합니다.
4. **끝점**을 클릭합니다.
5. **추가**를 클릭합니다.
6. **끝점 추가** 대화 상자에서 **독립형 끝점 추가**가 선택되었는지 확인하고 **다음**을 클릭합니다.
7. **새 끝점 세부 정보** 대화 상자에서 다음을 수행합니다.
    1. 끝점의 이름(예: **HttpIn**)을 지정합니다.
    2. 프로토콜의 **TCP**를 지정합니다.
    3. 공용 포트에서 **80**을 지정합니다.
    4. 개인 포트에서 **8080**을 지정합니다.
    5. **완료** 단추를 클릭하여 대화 상자를 닫습니다. 이제 끝점이 만들어집니다.

## 가상 컴퓨터의 방화벽에서 포트를 여는 방법
1. 가상 컴퓨터에 로그인합니다.
2. **Windows 시작**을 클릭합니다.
3. **제어판**을 클릭합니다.
4. **시스템 및 보안**, **Windows 방화벽**, **고급 설정**을 차례로 클릭합니다.
5. **인바운드 규칙**을 선택하고 **새 규칙**을 클릭합니다. ![새 인바운드 규칙][NewIBRule]
6. **규칙 유형**에 대해 **포트**를 선택하고 **다음**을 클릭합니다. ![새 인바운드 규칙 포트][NewRulePort]
7. **프로토콜 및 포트** 화면에서 **TCP**를 선택하고 **특정 로컬 포트**로 **8080**을 지정한 후 **다음**을 클릭합니다. ![새 인바운드 규칙][NewRuleProtocol]
8. **작업** 화면에서 **연결 허용**을 선택하고 **다음**을 클릭합니다. ![새 인바운드 규칙 동작][NewRuleAction]
9. **프로필** 화면에서 **도메인**, **개인** 및 **공용**이 선택되었는지 확인하고 **다음**을 클릭합니다. ![새 인바운드 규칙 프로필][NewRuleProfile]
10. **이름** 화면에서 규칙 이름(예: **HttpIn**)(규칙 이름은 끝점 이름과 일치하지 않아도 됨)을 지정하고 **마침**을 클릭합니다. ![새 인바운드 규칙 이름][NewRuleName]

이제 외부 브라우저에서 **http://*your\_DNS\_name*.cloudapp.net** 형식의 URL을 사용하면 Tomcat 웹 사이트가 표시됩니다. 여기서 ***your\_DNS\_name***은 가상 컴퓨터를 만들 때 지정한 DNS 이름입니다.

## 응용 프로그램 수명 주기 고려 사항
* 사용자 고유의 WAR(웹 응용 프로그램 보관)을 만들어 **webapps** 폴더에 추가할 수 있습니다. 예를 들어, 기본 JSP(Java 서비스 페이지) 동적 웹 프로젝트를 만들어 WAR 파일로 내보내고, 가상 컴퓨터의 Apache Tomcat **webapps** 폴더로 WAR 파일을 복사한 후 브라우저에서 실행합니다.
* 기본적으로 Tomcat 서비스가 설치될 때 수동으로 시작되도록 설정됩니다. 서비스 스냅인을 사용하여 자동으로 시작되도록 전환할 수 있습니다. **Windows 시작**, **관리 도구**, **서비스**를 차례로 클릭하여 서비스 스냅인을 시작합니다. **Apache Tomcat** 서비스를 두 번 클릭하고 **시작 유형**을 **자동**으로 설정합니다.

    ![서비스가 자동으로 시작되도록 설정][service_automatic_startup]

    Tomcat이 자동으로 시작되도록 설정할 경우의 이점은 가상 컴퓨터가 다시 부팅되면(예를 들어 재부팅이 필요한 소프트웨어 업데이트가 설치된 후) Tomcat이 시작된다는 점입니다.

## 다음 단계
Java 응용 프로그램에 포함할 수 있는 다른 서비스(예: Azure 저장소, 서비스 버스, SQL 데이터베이스)에 대한 자세한 내용은 [Java 개발자 센터](https://azure.microsoft.com/develop/java/)에 제공되는 정보를 참조하세요.

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png

<!---HONumber=AcomDC_0817_2016-->