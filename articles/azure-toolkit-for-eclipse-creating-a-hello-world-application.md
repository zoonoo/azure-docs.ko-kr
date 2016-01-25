<properties
    pageTitle="Eclipse에서 Azure용 Hello World 응용 프로그램 만들기"
    description="Eclipse용 Azure 도구 키트를 사용하여 간단한 Hello World 응용 프로그램을 만드는 방법을 알아봅니다."
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
    ms.date="01/09/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# Eclipse에서 Azure용 Hello World 응용 프로그램 만들기 #

다음 단계에서는 Eclipse용 Azure 도구 키트를 사용하여 Azure에 기본 JSP 응용 프로그램을 배포하는 방법을 보여줍니다. JSP 예제는 편의를 위해 표시되지만 Azure 배포가 관련되는 한 아주 유사한 단계는 Java 서블릿에 적합합니다.

응용 프로그램은 다음과 유사합니다.

![][ic600360]

## 필수 조건 ##

* JDK(Java 개발자 키트), v 1.7 이상
* Eclipse IDE for Java EE Developers, Indigo 이상. <http://www.eclipse.org/downloads/>에서 다운로드할 수 있습니다.
* Java 기반 웹 서버 또는 응용 프로그램 서버의 배포(예: Apache Tomcat, GlassFish, JBoss Application Server, Jetty 또는 IBM® WebSphere® Application Server Liberty Core)
* Azure 구독. <http://azure.microsoft.com/pricing/purchase-options/>에서 구입할 수 있습니다.
* Eclipse용 Azure 도구 키트 자세한 내용은 [Eclipse용 Azure 도구 키트 설치][]를 참조하세요.

## Hello World 응용 프로그램을 만들려면 ##

먼저 java 프로젝트를 만듭니다.

*  Eclipse를 시작하고 메뉴에서 **File**, **New**, **Dynamic Web Project**를 차례로 클릭합니다. (**파일**, **새로 만들기**를 차례로 클릭한 후 **Dynamic Web Project**가 사용 가능한 프로젝트로 표시되지 않는 경우 **파일**, **새로 만들기**, **프로젝트...**를 차례로 클릭한 후 **웹**을 확장하고 **Dynamic Web Project**를 클릭한 후 **다음**을 클릭합니다.)
*  이 자습서에서는 프로젝트의 이름을 **MyHelloWorld**로 지정합니다. (이 이름을 사용했는지 확인하세요. 이 자습서의 이후 단계에서는 WAR 파일의 이름이 MyHelloWorld라고 가정합니다.) 화면이 다음과 유사하게 나타납니다.
    ![][ic589576]
* **마침**을 클릭합니다.
* Eclipse의 Project Explorer 뷰 내에서 **MyHelloWorld**를 확장합니다. **WebContent**를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **JSP File**을 클릭합니다.
* **New JSP File** 대화 상자에서 파일의 이름을 **index.jsp**로 지정합니다. 다음에 표시된 것처럼 상위 폴더를 **MyHelloWorld/WebContent**로 유지합니다.

    ![][ic659262]
* **JSP 템플릿 선택** 대화 상자에서 이 자습서의 목적에 따라 **새 JSP 파일(html)**을 선택하고 **완료**를 클릭합니다.
* Eclipse에서 index.jsp 파일이 열리면 기존 `<body>` 요소 내에 **Hello World!**를 동적으로 표시하도록 텍스트를 추가합니다. 업데이트된 `<body>` 내용이 다음과 같이 나타납니다.
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* index.jsp를 저장합니다.

## 빠르고 간편한 방식으로 Azure에 응용 프로그램을 배포하려면 ##

Java 웹 응용 프로그램을 테스트할 준비가 되는 즉시 다음 바로 가기를 사용하여 Azure 클라우드에서 직접 체험할 수 있습니다.

1. Eclipse의 프로젝트 탐색기에서 **MyHelloWorld**를 클릭합니다.
1. Eclipse 도구 모음에서 **Azure 클라우드에 게시** 단추를 클릭합니다.
    ![][ic710882]
1. 처음으로 Azure에 이 응용 프로그램을 게시하고 이전에 이 응용 프로그램에 대한 Azure 배포 프로젝트를 만들지 않은 경우 Azure 배포 프로젝트가 자동으로 만들어집니다. 또한 다음과 같은 메시지가 표시되어야 하며 이는 응용 프로그램을 실행하기 위해 자동으로 배포되는 JDK 패키지 및 응용 프로그램 서버를 나열합니다.

    ![][ic789598]
    이 바로 가기 방식을 사용하면 기본값과 다른 특정 서버 또는 JDK를 구성할 필요 없이 Azure에서 응용 프로그램을 테스트하는 빠르고 쉬운 방법이 가능합니다. 기본값에 만족할 경우 **확인**을 클릭하여 다음 단계를 계속할 수 있습니다.
    그러나 응용 프로그램에 사용할 JDK 또는 응용 프로그램 서버를 변경하려는 경우 나중에 사용자를 위해 자동으로 만들어진 Azure 배포 프로젝트를 편집하여 수행하거나 지금 **취소**를 클릭하고 이 자습서의 **Azure 배포 프로젝트 섹션 정보**를 읽을 수 있습니다.
1. **Azure에 게시** 대화 상자에서:
    1. 아직 **구독** 목록에서 선택할 구독이 없는 경우 다음이 단계를 수행하여 구독 정보를 가져옵니다.
        1. **게시 설정 파일에서 가져오기**를 클릭합니다.
        1. **구독 정보 가져오기** 대화 상자에서 **게시-설정 파일 다운로드**를 클릭합니다. Azure 계정에 로그인하지 않은 경우에는 로그인을 요청하는 메시지가 표시됩니다. 그 후 Azure 게시 설정 파일을 저장하도록 요청하는 메시지가 표시됩니다. 로컬 컴퓨터에 저장합니다.
        1. **구독 정보 가져오기** 대화 상자에서 **찾아보기** 단추를 클릭하고 이전 단계에서 로컬에 저장해 놓은 게시 설정 파일을 선택한 다음 **열기**를 클릭합니다. 화면은 다음과 유사해야 합니다.

            ![][ic644267]
        1. **확인**을 클릭합니다.
    1. **구독**의 경우 배포에 사용하려는 구독을 선택합니다.
    1. **저장소 계정**의 경우 사용하려는 저장소 계정을 선택하거나 **새로 만들기**를 클릭하여 새 저장소 계정을 만듭니다.
    1. **서비스 이름**의 경우 사용하려는 클라우드 서비스를 선택하거나 **새로 만들기**를 클릭하여 새 클라우드 서비스를 만듭니다.
    1. **대상 OS**의 경우 배포에 사용하려는 운영 체제의 버전을 선택합니다.
    1. **대상 환경**의 경우 자습서의 목적에 따라 **준비**를 선택합니다. (프로덕션 사이트에 배포할 준비가 되면 **프로덕션**에 이를 변경합니다.)
    1. 선택 사항: 새 배포가 자동으로 이전 배포를 덮어쓰도록 하려면 **이전 배포 덮어쓰기**가 선택되어야 합니다. 이 옵션을 사용하면 동일한 위치에 게시할 때 "409 충돌" 문제가 발생하지 않습니다.
        **Azure에 게시** 대화 상자는 **원격 액세스**에 대한 섹션을 포함합니다. 기본적으로 원격 액세스가 설정되지 않으며 이 예에서도 사용하지 않습니다. 원격 액세스를 설정하려면 사용자 이름 및 암호를 입력하여 원격으로 로그인할 때 사용합니다. 원격 액세스에 대한 자세한 내용은 [Eclipse에서 Azure 배포에 원격 액세스 사용][]
        을 참조하세요. **Azure에 게시** 대화 상자는 다음과 유사하게 표시됩니다.
        ![][ic719488]
1. **게시**를 클릭하여 스테이징 환경에 게시합니다.
    전체 빌드를 수행하라는 메시지가 나타나면 **예**를 클릭합니다.
    첫 번째 작성에 몇 분 정도 걸릴 수 있습니다. **Azure 활동 로그**는 Eclipse가 탭된 뷰 섹션에 표시됩니다.
    ![][ic719489]
    **콘솔** 보기와 같이 이 로그를 사용하여 배포의 진행 상황을 볼 수 있습니다. [Azure 관리 포털][]에 로그인하거나 **클라우드 서비스** 섹션을 사용해서도 상태를 모니터링할 수 있습니다.
1. 배포가 성공적으로 배포될 때 **Azure 활동 로그**는 **게시된** 상태를 보여줍니다. 다음 그림에 표시된 것처럼 **게시**를 클릭하고 브라우저가 배포의 인스턴스를 엽니다.
    ![][ic719490]

스테이징 환경에 대한 배포이기 때문에 DNS 이름은 http://&lt;*guid*&gt;.cloudapp.net 양식이고 URL은 DNS 이름과 응용 프로그램에 대한 접미사를 포함합니다. 예: http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld (**MyHelloWorld** 부분은 대/소문자를 구분합니다.) 또한 Azure Platform Management 포털(관리 포털의 클라우드 서비스 부분 내)에서 배포 이름을 클릭하는 경우 DNS 이름을 볼 수 있습니다.

이 연습이 스테이징 환경에 대한 배포용이지만 프로덕션에 배포는 **Azure에 게시** 대화 상자를 제외하고 동일한 단계를 따르고 **대상 환경**에 대한 **준비** 대신 **프로덕션**을 선택합니다. 프로덕션에 대한 배포는 스테이징에 사용되는 GUID 대신 선택한 DNS 이름을 기반으로 한 URL을 발생시킵니다.

>[AZURE.WARNING]이 시점에서 클라우드에 Azure 응용 프로그램을 배포했습니다. 그러나 계속하기 전에 배포된 응용 프로그램이 실행되지 않을 경우에도 구독에 대한 청구 가능 시간을 계속 실현한다는 점을 중요합니다. 따라서 Azure 구독에서 원하지 않는 배포를 삭제하는 것이 매우 중요합니다.

## Azure 배포 프로젝트 정보 ##

하나 이상의 Java 응용 프로그램을 Azure에 배포하려면 Azure 배포 프로젝트가 필요합니다. 응용 프로그램이 Azure에 게시되기 위해 래핑되어야 하는 "패키지"의 역할을 수행합니다.

또한 응용 프로그램에 대한 내용 외에도 Azure 배포 프로젝트는 배포의 다른 주요 구성 요소에 대한 정보를 포함합니다. 가장 중요한 부분은 웹앱을 실행하려는 응용 프로그램 서버 컨테이너 및 실행할 Java 런타임입니다. Azure에서는 선택할 수 있는 여러 Java 런타임 및 Java 응용 프로그램을 지원합니다.

여기에 사용된 예제가 교육을 위한 목적으로 간단하지만 Azure 배포 프로젝트는 응용 프로그램을 사용하여 복잡하고 확장성 있는 고가용성의 다중 계층 클라우드 서비스를 만들 수 있도록 다른 중요한 구성 정보를 포함할 수도 있습니다. **세션 선호도("고정 세션")**, **빠른 캐싱**, **원격 디버깅**, **SSL 오프 로딩**, **방화벽/포트 라우팅**, **원격 액세스** 및 다른 다양한 강력한 기능을 사용할 수 있습니다.

이 자습서("Azure에 응용 프로그램 배포, 빠르고 간편한 방식")의 이전 섹션을 완료한 경우 자동으로 생성 ehls "**MyHelloWorld\_onAzure**"라는 프로젝트 탐색기에서 새 Azure 배포 프로젝트를 이제 확인합니다

또한 먼저 빈 Azure 배포 프로젝트를 직접 만들고 응용 프로그램을 추가하여 이 자습서를 시작했습니다. 긴 과정이지만 처음부터 초기 구성을 강하게 제어할 수 있습니다.

새 Azure 배포 프로젝트를 처음부터 새로 만들려면 **새 Azure 배포 프로젝트** 단추 ![][ic710876]를 클릭합니다.

기존의 Azure 배포 프로젝트로 작업하거나 처음부터 새로 만드는지에 관계 없이 JDK 또는 응용 프로그램 서버와 같은 해당 구성 설정 및 구성 요소를 동일하게 언제든지 쉽게 변경할 수 있습니다.

기존 Azure 배포 프로젝트의 JDK, 응용 프로그램 서버 또는 응용 프로그램 목록을 변경하려면 :

1. 프로젝트 탐색기에서 프로젝트 노드 확장(예: **MyHelloWorld\_onAzure**)
2. 마우스 오른쪽 단추로 **WorkerRole1** 클릭
3. 상황에 맞는 메뉴에서 **Azure** 하위 메뉴 확장
4. **서버 구성** 클릭

위에 표시된 대로 기존 Azure 배포 프로젝트를 편집하거나 처음부터 새로 만들지에 관계 없이 서버 구성 단계를 시작하면 JDK, 서버 및 응용 프로그램 구성 요소를 구성할 수 있는 동일한 형식의 대화 상자가 표시됩니다. 해당 대화 상자에서 JDK인 응용 프로그램 서버를 변경하거나 배포에서는 응용 프로그램을 제거하는 등 설정을 변경하는 방법을 자세히 알아보려면 [서버 구성 속성][] 문서를 참조하세요.

## Windows에만 해당: 계산 에뮬레이터에 응용 프로그램을 배포하려면 ##

>[AZURE.NOTE]Azure 에뮬레이터는 Windows에서만 사용할 수 있습니다. Windows 이외의 운영 체제를 사용하는 경우 이 섹션을 건너뜁니다.

이전에 암시적으로 설명한 단계에 따라 Azure에 응용 프로그램을 게시하여 새 Azure 배포 프로젝트를 만든 경우 JDK 및 응용 프로그램 서버는 로컬 에뮬레이션이 아닌 클라우드에 대해 구성됩니다. 로컬 에뮬레이터에서 프로젝트를 테스트할 준비를 하려면 다음 단계를 따릅니다.

1. Eclipse의 프로젝트 탐색기에서 **MyHelloWorld\_onAzure**를 클릭합니다.
1. 마우스 오른쪽 단추로 **WorkerRole1**를 클릭합니다.
1. 상황에 맞는 메뉴에서 **Azure** 하위 메뉴를 확장합니다.
1. **서버 구성**을 클릭합니다.
1. **JDK** 탭에서 도구 키트에 미리 구성된 기본 로컬 JDK가 있는지 확인합니다. 그렇지 않은 경우 또는 가정된 기본값을 변경하려는 경우 **로컬로 테스트를 위해 이 파일 경로에서 JDK 사용** 확인란이 선택되어 있고 사용하려는 JDK 설치 위치가 지정되어 있어야 합니다. 변경하려는 경우 찾아보기 제어를 사용하여 **찾아보기**를 클릭하고 사용할 JDK의 디렉터리 위치를 선택합니다.
1. **서버** 탭을 클릭합니다.
1. 대화 상자 맨 아래에 있는 **로컬 서버 경로** 텍스트 상자에서 **이 형식의 서버 배포** 확인란의 대화 상자 맨 아래에서 선택한 서버의 형식 및 주 버전 번호와 일치하는 로컬에 설치된 서버의 경로를 입력합니다. 응용 프로그램 서버의 다른 형식 또는 주 버전을 사용하려는 경우 먼저 해당 확인란에서 선택을 변경합니다.
1. **확인**을 클릭합니다.
1. Eclipse 도구 모음에서 **Azure 에뮬레이터에서 실행** 단추 ![][ic710879]를 클릭합니다. **Azure 에뮬레이터에서 실행** 단추가 활성화되지 않으면 **MyHelloWorld\_onAzure**가 Eclipse의 프로젝트 탐색기에서 선택되고 Eclipse 프로젝트 탐색기에 포커스가 현재 창으로 포커스되어 있도록 합니다. 그러면 먼저 프로젝트의 전체 빌드를 시작하고 계산 에뮬레이터에서 Java 웹 응용 프로그램을 시작합니다. (컴퓨터의 성능 특성에 따라 첫 번째 빌드는 몇 초에서 몇 분까지 걸릴 수 있지만 후속 빌드는 훨씬 빨라집니다.) 첫 번째 빌드 단계가 완료된 후에 Windows 사용자 계정 컨트롤(UAC)에서 메시지가 표시되어 이 명령이 컴퓨터를 변경할 수 있도록 합니다. **예**를 클릭합니다.

>[AZURE.IMPORTANT]UAC 프롬프트가 표시되지 않으면 UAC 아이콘에 대한 Windows 작업 표시줄을 확인하고 먼저 클릭합니다. 때로는 UAC 프롬프트가 최상위 창으로 표시되지 않고 작업 표시줄 아이콘으로 표시됩니다.

1. 계산 에뮬레이터 UI의 출력을 검사하여 프로젝트에 문제가 있는지 확인합니다. 배포 콘텐츠에 따라 응용 프로그램이 계산 에뮬레이터 내에서 완전히 시작되려면 일 분 정도 걸릴 수 있습니다.
1. 브라우저를 시작하고 URL `http://localhost:8080/MyHelloWorld`을 주소로 사용합니다.(URL의 `MyHelloWorld` 부분은 대/소문자 구분함) MyHelloWorld 응용 프로그램(index.jsp의 출력)을 확인하면 다음 이미지와 비슷해야 합니다.
    ![][ic589579]

계산 에뮬레이터에서 실행되는 응용 프로그램을 중지할 준비가 되면 Eclipse 도구 모음에서 **Azure 에뮬레이터 다시 설정** 단추 ![][ic710880]를 클릭합니다.

## 배포를 삭제하려면 ##

Eclipse용 Azure 도구 모음 내에서 배포를 삭제하려면 Eclipse의 프로젝트 탐색기에서 **MyHelloWorld\_onAzure**을 선택하고 Eclipse 프로젝트 탐색기가 현재 창에 포커스를 맞춘 다음 **게시 취소** 단추 ![][ic710883]을 Eclipse 도구 모음에서 클릭해야 합니다. (Eclipse의 프로젝트 탐색기에서 마우스 오른쪽 단추로 **MyHelloWorld\_onAzure**를 클릭하고 **Azure**를 클릭한 다음 **Azure 클라우드에서 배포 취소**를 클릭하여 동일한 작업을 수행할 수 있습니다.) **Azure 프로젝트 게시 취소** 대화 상자가 표시됩니다.

![][ic719491]

배포를 포함하는 구독 및 클라우드 서비스를 선택하고 삭제하려는 배포를 선택한 다음 **게시 취소**를 클릭합니다.

(배포를 삭제하기 위해 도구 키트를 사용하는 대신 Azure 관리 포털의 **클라우드 서비스** 섹션을 사용할 수 있습니다. 배포로 이동하고 선택한 다음 **삭제** 단추를 클릭합니다. 배포를 중지한 다음 삭제합니다. 배포를 중지하지만 삭제하지 않으려는 경우 **삭제** 단추 대신 **중지**를 클릭하지만 위에서 언급한 대로 배포를 삭제하지 않으면 중지된 경우에도 배포에 청구 가능한 요금이 계속 발생합니다.)

## 참고 항목 ##

[Eclipse용 Azure 도구 키트][]

[Eclipse용 Azure 도구 키트 설치][]

[Eclipse용 Azure 도구 키트의 새로운 기능][]

Java와 함께 Azure를 사용하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터][]를 참조하세요.

<!-- URL List -->

[Azure Java 개발자 센터]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure 관리 포털]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Eclipse용 Azure 도구 키트]: http://go.microsoft.com/fwlink/?LinkID=699529
[Eclipse에서 Azure 배포에 원격 액세스 사용]: http://go.microsoft.com/fwlink/?LinkID=699538
[Eclipse용 Azure 도구 키트 설치]: http://go.microsoft.com/fwlink/?LinkId=699546
[서버 구성 속성]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Eclipse용 Azure 도구 키트의 새로운 기능]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png

<!---HONumber=AcomDC_0114_2016-->