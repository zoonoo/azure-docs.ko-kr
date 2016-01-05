<properties
    pageTitle="Eclipse에서 Azure 응용 프로그램 디버깅"
    description="Eclipse 용 Azure 도구 키트를 사용하여 Azure 응용 프로그램 디버깅에 알아봅니다."
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
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# Eclipse에서 Azure 응용 프로그램 디버깅 #

Windows 운영 체제를 사용하는 경우 Eclipse용 Azure 도구 키트를 사용하여 응용 프로그램이 Azure 또는 계산 에뮬레이터에서 실행 중인지 여부를 디버그할 수 있습니다. 다음 그림에서는 원격 디버깅을 사용하는 데 사용되는 **Debugging** 속성 대화 상자를 보여 줍니다.

![][ic719504]

이 자습서에서는 성공적으로 만들어진 응용 프로그램이 있고 계산 에뮬레이터 및 Azure에 배포에 대해 잘 알고 있다고 가정합니다.

이 항목에 대한 출발점으로 [JSP에서 Azure 서비스 런타임 라이브러리 사용][] 자습서에서 응용 프로그램을 사용할 예정입니다. 계속하기 전에 응용 프로그램이 없는 경우 해당 응용 프로그램을 만듭니다.

## Azure에서 실행되는 동안 응용 프로그램을 디버그하려면 ##

>[AZURE.WARNING]원격 Java 디버깅에 대한 도구 키트의 현재 지원은 주로 Azure 계산 에뮬레이터에서 실행 중인 배포를 위한 것입니다. 디버깅 연결은 안전하지 않으므로 프로덕션 배포에서 원격 디버깅을 사용하지 마십시오. Azure 클라우드에서 실행되는 응용 프로그램을 디버그해야 할 경우 스테이징 배포를 사용하지만 스테이징 배포이더라도 클라우드 배포의 IP 주소를 아는 사람이 있는 경우 디버그 세션에 대한 무단 액세스가 가능할 수 있습니다.

1. 에뮬레이터에서 테스트용 프로젝트 빌드: Eclipse의 프로젝트 탐색기에서 마우스 오른쪽 단추로 **MyAzureProject**를 클릭하고 **Properties**, **Azure**를 차례로 클릭하고 **Build for**를 **Deployment to cloud**로 설정합니다.
1. 프로젝트 다시 빌드: Eclipse 메뉴에서 **Project**를 클릭한 다음 **Build All**을 클릭합니다.
1. Azure의 *준비*에 응용 프로그램을 배포합니다.

    >[AZURE.IMPORTANT] 위에서 설명했듯이 대부분의 경우에는 계산 에뮬레이터에서 디버그한 다음 추가 디버깅이 필요한 경우에만 스테이징 환경에서 디버그하는 것이 좋습니다. 프로덕션 환경에서 디버그하지 않는 것이 좋습니다.
1. Azure에서 배포가 준비되면 [Azure 관리 포털][]에서 배포에 대한 DNS 이름을 가져옵니다. 스테이징 배포는 http://*&lt;guid&gt;*.cloudapp.net의 형식으로 DNS 이름을 가지며 여기서 *&lt;guid&gt;*은(는) Azure에서 할당된 GUID 값입니다.
1. Eclipse의 Project Explorer에서 **WorkerRole1**을 마우스 오른쪽 단추로 클릭하고 **Azure**를 클릭한 후 **Debugging**을 클릭합니다.
1. **Properties for WorkerRole1 Debugging** 대화 상자에서:
    1. **Enable remote debugging for this role**을 선택합니다.
    1. **Input endpoint to use**에 대해 **Debugging (public:8090, private:8090)**을 사용합니다.
    1. **Start JVM in suspended mode, waiting for a debugger connection**이 선택 취소되었는지 확인합니다.

        >[AZURE.IMPORTANT] **Start JVM in suspended mode, waiting for a debugger connection** 옵션은 계산 에뮬레이터에서 고급 디버깅 시나리오를 위한 것입니다(클라우드 배포용이 아님). **Start JVM in suspended mode, waiting for a debugger connection** 옵션을 사용하는 경우 Eclipse 디버거가 해당 JVM에 연결될 때까지 서버의 시작 프로세스를 일시 중단합니다. 계산 에뮬레이터를 사용하여 디버깅 세션에 이 옵션을 사용할 수도 있지만 클라우드 배포에서 디버깅 세션에 사용하지 마십시오. 서버 초기화는 Azure 시작 작업에서 수행되고 Azure 클라우드는 시작 작업이 완료될 때까지 사용 가능한 공용 끝점을 만들지 않습니다. 따라서 외부 Eclipse 클라이언트에서 연결을 수신할 수 없기 때문에 클라우드 배포에서 이 옵션을 사용하는 경우 시작 프로세스가 올바르게 완료되지 않습니다.
    1. **Create Debug Configurations**를 클릭합니다.
1. **Azure Debug Configuration** 대화 상자에서:
    1. **Java project to debug**에 대해 **MyHelloWorld** 프로젝트를 선택합니다.
    1. **Configure debugging for**에 대해 **Azure cloud (staging)**를 선택합니다.
    1. **Azure compute emulator**가 선택 취소되었는지 확인합니다.
    1. **Host**에 대해 앞의 ****http://** 없이 스테이징된 배포의 DNS 이름을 입력합니다. 예를 들어(여기에 나와 있는 GUID 대신 실제 GUID 사용): **4e616d65-6f6e-6 d 65 6973 526f62657274.cloudapp.net**
1. **OK**를 클릭하여 **Azure Debug Configuration** 대화 상자를 닫습니다.
1. **OK**를 클릭하여 **Properties for WorkerRole1 Debugging** 대화 상자를 닫습니다.
1. index.jsp에서 설정된 중단점이 없는 경우 다음과 같이 설정합니다.
    1. Eclipse의 프로젝트 탐색기 내에서 **MyHelloWorld**를 확장하고 **WebContent**를 확장하고 **index.jsp**를 두 번 클릭합니다.
    1. index.jsp 내에서 Java 코드 왼쪽의 파란색 막대에서 마우스 오른쪽 단추로 클릭하고 다음에 표시된 것처럼 **Toggle Breakpoints**를 클릭합니다. ![][ic551537]
1. Eclipse 메뉴 내에서 **Run**을 클릭한 다음 **Debug Configurations**를 클릭합니다.
1. **Debug Configurations** 대화 상자에서 왼쪽 창의 **Remote Java Application**을 확장하고 **Azure Cloud (WorkerRole1)**를 선택한 다음 **Debug**를 클릭합니다.
1. 브라우저 내에서 스테이징된 응용 프로그램, ****http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, *&lt;guid&gt;*에 대한 DNS 이름에서 GUID 대체를 실행합니다. **Confirm Perspective Switch** 대화 상자로 메시지가 표시된 경우 **Yes**를 클릭합니다. 중단점을 설정한 코드 줄에 지금 디버그 세션을 실행해야 합니다.

>[AZURE.NOTE]여러 역할 인스턴스를 실행하는 배포에 원격 디버깅 연결을 시작하려는 경우 Azure 부하 분산 장치에서 인스턴스를 임의로 선택하므로 디버거가 처음에 연결되는 인스턴스를 제어할 수 없습니다. 해당 인스턴스로 연결되더라도 동일한 인스턴스 디버깅을 계속합니다. 또한 4분 이상의 비활성화가 있는 경우(예: 너무 오랫동안 중단점에서 중지되어 있는 경우) Azure에서 연결을 닫을 수 있습니다.

## 다중 인스턴스 배포에서 특정 역할 인스턴스 디버깅 ##

배포가 클라우드에서 실행 중인 경우 여러 계산 또는 역할, 인스턴스에서 실행하게 됩니다. 이렇게 하면 Azure 99.95% 가용성 보장을 활용하고 응용 프로그램을 확장할 수 있습니다.

이러한 시나리오에서는 특정 역할 인스턴스에서 Java 응용 프로그램을 원격으로 디버그해야 합니다. 그러나 디버깅에 대해 일반 입력 끝점만을 사용하도록 활성화한 경우 Azure 부하 분산 장치는 사실상 특정 역할 인스턴스에 디버거를 연결할 수 없게 합니다. 대신 임의로 선택하는 역할 인스턴스에 연결시킵니다.

인스턴스 입력 끝점을 활용하여 특정 역할 인스턴스를 쉽게 디버그할 수 있도록 하는 시나리오의 유형입니다.

최대 5개의 역할 인스턴스 배포를 실행하려는 경우를 가정해 봅니다. 역할 속성 대화 상자의 **끝점** 속성 페이지를 사용하여 인스턴스 입력 끝점을 만들고 단일 포트 번호가 아닌 공용 포트의 범위를 할당합니다. 예를 들어 **공용 포트** 입력 상자에서 **81-85**를 지정합니다.

이 인스턴스 끝점으로 응용 프로그램을 배포한 후 Azure는 이 범위에서 고유 포트 번호를 각 역할 인스턴스에 할당합니다. 그런 다음 각 인스턴스가 할당 받은 포트 번호를 확인하기 위해 배포에서 도구 키트로 자동으로 구성된 *InstanceEndpointName***\_PUBLICPORT** 환경 변수(여기서 *InstanceEndpointName*은 인스턴스 끝점을 만들 때 할당된 이름)를 사용할 수 있습니다(예를 들어 페이지로 이동할 때 읽을 수 있도록 웹 페이지의 바닥글에 해당 값을 반환하여).

각 인스턴스에 할당된 공용 포트 번호를 알게 되면 서비스의 호스트 이름에 이를 추가하여 Eclipse의 디버그 구성에서 참조할 수 있습니다. Eclipse 디버거가 다른 인스턴스가 아닌 해당 특정 인스턴스에 연결할 수 있도록 합니다.

## Windows에만 해당: 계산 에뮬레이터에서 실행하는 동안 응용 프로그램을 디버그하려면 ##

>[AZURE.NOTE]Azure 에뮬레이터는 Windows에서만 사용할 수 있습니다. Windows 이외의 운영 체제를 사용하는 경우 이 섹션을 건너뜁니다.

1. 에뮬레이터에서 테스트용 프로젝트 빌드: Eclipse의 프로젝트 탐색기에서 마우스 오른쪽 단추로 **MyAzureProject**를 클릭하고 **Properties**, **Azure**를 차례로 클릭하고 **Build for**를 **Testing in emulator**로 설정합니다.
1. 프로젝트 다시 빌드: Eclipse 메뉴에서 **Project**를 클릭한 다음 **Build All**을 클릭합니다.
1. Eclipse의 Project Explorer에서 **WorkerRole1**을 마우스 오른쪽 단추로 클릭하고 **Azure**를 클릭한 후 **Debugging**을 클릭합니다.
1. **Properties for WorkerRole1 Debugging** 대화 상자에서:
    1. **Enable remote debugging for this role**을 선택합니다.
    1. **Input endpoint to use**에 대해 **Debugging (public:8090, private:8090)**에 나열된 도구 키트에 의해 자동으로 생성된 기본 끝점을 사용합니다.
    1. **Start JVM in suspended mode, waiting for a debugger connection** 옵션이 선택 취소되었는지 확인합니다.

        >[AZURE.IMPORTANT] **Start JVM in suspended mode, waiting for a debugger connection** 옵션은 계산 에뮬레이터에서 고급 디버깅 시나리오를 위한 것입니다(클라우드 배포용이 아님). **Start JVM in suspended mode, waiting for a debugger connection** 옵션을 사용하는 경우 Eclipse 디버거가 해당 JVM에 연결될 때까지 서버의 시작 프로세스를 일시 중단합니다. 계산 에뮬레이터를 사용하여 디버깅 세션에 이 옵션을 사용할 수도 있지만 클라우드 배포에서 디버깅 세션에 사용하지 마십시오. 서버 초기화는 Azure 시작 작업에서 수행되고 Azure 클라우드는 시작 작업이 완료될 때까지 사용 가능한 공용 끝점을 만들지 않습니다. 따라서 외부 Eclipse 클라이언트에서 연결을 수신할 수 없기 때문에 클라우드 배포에서 이 옵션을 사용하는 경우 시작 프로세스가 올바르게 완료되지 않습니다.
    1. **Create Debug Configurations**를 클릭합니다.
1. **Azure Debug Configuration** 대화 상자에서:
    1. **Java project to debug**에 대해 **MyHelloWorld** 프로젝트를 선택합니다.
    1. **Configure debugging for**에 대해 **Azure compute emulator**를 선택합니다.
1. **OK**를 클릭하여 **Azure Debug Configuration** 대화 상자를 닫습니다.
1. **OK**를 클릭하여 **Properties for WorkerRole1 Debugging** 대화 상자를 닫습니다.
1. index.jsp에서 중단점 설정:
    1. Eclipse의 프로젝트 탐색기 내에서 **MyHelloWorld**를 확장하고 **WebContent**를 확장하고 **index.jsp**를 두 번 클릭합니다.
    1. index.jsp 내에서 Java 코드 왼쪽의 파란색 막대에서 마우스 오른쪽 단추로 클릭하고 다음에 표시된 것처럼 **Toggle Breakpoints**를 클릭합니다. ![][ic551537] 파란색 막대 안의 중단점 아이콘이 Java 코드 왼쪽에 표시되면 중단점이 설정됩니다.
1. Azure 도구 모음에서 **Run in Azure Emulator** 단추를 클릭하여 계산 에뮬레이터에서 응용 프로그램을 시작합니다.
1. Eclipse 메뉴 내에서 **Run**을 클릭한 다음 **Debug Configurations**를 클릭합니다.
1. **Debug Configurations** 대화 상자에서 왼쪽 창의 **Remote Java Application**을 확장하고 **Azure Emulator (WorkerRole1)**를 선택한 다음 **Debug**를 클릭합니다.
1. 브라우저 내에서 계산 에뮬레이터가 응용 프로그램이 실행 중임을 나타낸 후 ****http://localhost:8080/MyHelloWorld**을(를) 실행합니다. **Confirm Perspective Switch** 대화 상자로 메시지가 표시된 경우 **Yes**를 클릭합니다. 중단점을 설정한 코드 줄에 지금 디버그 세션을 실행해야 합니다.

계산 에뮬레이터에서 디버그하는 방법을 살펴보았습니다. 다음 섹션에서는 Azure에 배포된 응용 프로그램을 디버그하는 방법을 보여 줍니다.

## 디버깅 참고 사항 ##

* 디버깅 후 **Window**, **Open Perspective**를 클릭하고 사용하려는 관점을 선택하여 Eclipse의 메뉴 클릭을 통해 **Debug**에서 **Java**로 관점을 전환할 수 있습니다.
* GlassFish에서 원격 디버깅을 사용하려면 Eclipse용 Azure 도구 키트의 원격 디버깅 구성 기능을 사용하지 마십시오. 대신 GlassFish를 수동으로 구성합니다. GlassFish에서 환경 변수에서 미리 정의된 Java 옵션을 처리하는 방식으로 인해 도구 키트의 원격 디버깅 구성 기능이 GlassFish와 정상적으로 작동하지 않습니다. 도구 키트의 원격 디버깅 구성 기능을 사용하는 경우 GlassFish가 시작되지 않습니다.

## 참고 항목 ##

[Eclipse용 Azure 도구 키트][]

[Eclipse에서 Azure용 Hello World 응용 프로그램 만들기][]

[Eclipse용 Azure 도구 키트 설치][]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터][]를 참조하세요.

<!-- URL List -->

[Azure Java 개발자 센터]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure 관리 포털]: http://go.microsoft.com/fwlink/?LinkID=512959
[Eclipse용 Azure 도구 키트]: http://go.microsoft.com/fwlink/?LinkID=699529
[Eclipse에서 Azure용 Hello World 응용 프로그램 만들기]: http://go.microsoft.com/fwlink/?LinkID=699533
[Eclipse용 Azure 도구 키트 설치]: http://go.microsoft.com/fwlink/?LinkId=699546
[JSP에서 Azure 서비스 런타임 라이브러리 사용]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png

<!----HONumber=AcomDC_1210_2015-->
