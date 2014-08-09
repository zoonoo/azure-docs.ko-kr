<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="Multi-Tier Application" pageTitle=".NET Multi-Tier Application - Azure Tutorial" metaKeywords="Azure Service Bus queue tutorial, Azure queue tutorial, Azure worker role tutorial, Azure .NET queue tutorial, Azure C# queue tutorial, Azure C# worker role tutorial" description="A tutorial that helps you develop a multi-tier app in Azure that uses Service Bus queues to communicate between tiers. Samples in .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title=".NET Multi-Tier Application Using Service Bus Queues" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

서비스 버스 큐를 사용하는 .NET 다중 계층 응용 프로그램
======================================================

Visual Studio 2013 및 무료로 제공되는 Azure SDK for .NET을 사용하면 Azure용 개발이 간단합니다. 아직 Visual Studio 2013이 없는 경우 SDK에서 Visual Studio Express 2013을 자동으로 설치하므로 Azure용 개발을 무료로 시작할 수 있습니다. 이 가이드에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 가이드를 완료하면 로컬 환경에서 실행되고 다중 계층 응용 프로그램의 작동 방식을 보여 주는 여러 가지 Azure 리소스를 사용하는 응용 프로그램을 갖게 됩니다.

다음 내용을 배웁니다.

-   한 번 다운로드하고 설치하여 Azure 개발용 컴퓨터를 사용하도록 설정하는 방법
-   Visual Studio를 사용하여 Azure용으로 개발하는 방법
-   웹 및 작업자 역할을 사용하여 Azure에서 다중 계층 응용 프로그램을 만드는 방법
-   서비스 버스 큐를 사용하여 계층 간에 통신하는 방법

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

이 자습서에서는 Azure 클라우드 서비스에서 다중 계층 응용 프로그램을 빌드하고 실행합니다. 프런트 엔드는 ASP.NET MVC 웹 역할이고 백 엔드는 작업자 역할입니다. 클라우드 서비스가 아닌 Azure 웹 사이트에 배포되는 웹 프로젝트와 동일한 다중 계층 응용 프로그램(프런트 엔드 포함)을 만들 수 있습니다. Azure 웹 사이트 프런트 엔드를 다르게 수행할 작업에 대한 지침은 [다음 단계](#nextsteps) 섹션을 참조하십시오.

아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png)

**참고** Azure는 저장소 큐 기능도 제공합니다. Azure 저장소 큐 및 서비스 버스 큐에 대한 자세한 내용은 [Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh767287.aspx)를 참조하십시오.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

역할 간 통신시나리오 개요: 역할 간 통신
---------------------------------------

처리할 주문을 제출하려면 웹 역할에서 실행되는 프런트 엔드 UI 구성 요소가 작업자 역할에서 실행되는 중간 계층 논리와 상호 작용해야 합니다. 이 예제에서는 계층 간 통신에 서비스 버스 조정된 메시징을 사용합니다.

웹과 중간 계층 간에 조정된 메시징을 사용하면 두 구성 요소가 분리됩니다. 직접 메시징(즉, TCP 또는 HTTP)과 달리 웹 계층은 중간 계층에 직접 연결되지 않고 작업 단위를 메시지로 서비스 버스에 푸시하여 중간 계층에서 사용하고 처리할 준비가 될 때까지 안정적으로 유지합니다.

서비스 버스는 조정된 메시징을 지원하는 두 개의 엔터티인 큐와 토픽을 제공합니다. 큐를 사용하면 큐로 전송된 각 메시지가 단일 수신기에서 사용됩니다. 토픽은 게시된 각 메시지를 토픽에 등록된 각 구독에서 사용할 수 있게 하는 게시/구독 패턴을 지원합니다. 각 구독은 메시지의 고유한 큐를 논리적으로 유지 관리합니다. 또한 구독은 구독 큐에 전달되는 메시지 집합을 필터와 일치하는 메시지로 제한하는 필터 규칙을 사용하여 구성할 수 있습니다. 이 예제에서는 서비스 버스 큐를 사용합니다.

![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png)

이 통신 메커니즘은 직접 메시징에 비해 다음과 같은 여러 가지 장점이 있습니다.

-   **임시 분리.** 비동기 메시징 패턴을 사용하면 생산자와 소비자가 동시에 온라인 상태일 필요가 없습니다. 서비스 버스는 소비하는 쪽에서 메시지를 수신할 준비가 될 때까지 메시지를 안정적으로 저장합니다. 따라서 전체 시스템에는 영향을 주지 않고 자발적으로(예: 유지 관리의 경우) 또는 구성 요소 크래시로 인해 분산 응용 프로그램 구성 요소의 연결을 끊을 수 있습니다. 또한 소비 응용 프로그램은 하루 중 특정 기간 동안만 온라인 상태로 전환해야 할 수 있습니다.

-   **부하 평준화**. 많은 응용 프로그램에서 시스템 부하는 시간에 따라 다르지만 각 작업 단위에 필요한 처리 시간은 일반적으로 일정합니다. 큐를 사용한 메시지 생산자와 소비자 조정은 최대 부하가 아닌 평균 부하를 수용하려면 소비 응용 프로그램(작업자)만 프로비전해야 함을 의미합니다. 수신 부하가 변경됨에 따라 큐의 깊이가 증가하고 축소됩니다. 따라서 응용 프로그램 부하를 제공하는 데 필요한 인프라의 크기와 관련하여 비용이 직접적으로 절약됩니다.

-   **부하 분산.** 부하가 증가하면 뷰에서 읽을 작업자 프로세스가 더 추가될 수 있습니다. 각 메시지는 하나의 작업자 프로세스를 통해서만 처리됩니다. 또한 이 가져오기 기반 부하 분산에서는 작업자 컴퓨터가 최대 속도로 메시지를 가져올 때 처리 능력이 다른 경우에도 작업자 컴퓨터의 최적 사용률을 허용합니다. 이 패턴을 종종 경쟁 소비자 패턴이라고 합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png)

다음 섹션에서는 이 아키텍처를 구현하는 코드에 대해 설명합니다.

환경 설정개발 환경 설정
-----------------------

Azure 응용 프로그램 개발을 시작하려면 먼저 도구를 가져오고 개발 환경을 설정해야 합니다.

1.  Azure SDK for .NET을 설치하려면 아래 단추를 클릭합니다.

    [도구 및 SDK 얻기(영문)](http://go.microsoft.com/fwlink/?LinkId=271920)

2.  **SDK 설치**를 클릭합니다.

3.  사용하고 있는 Visual Studio 버전에 대한 링크를 선택합니다. 이 자습서의 단계에서는 Visual Studio 2013을 사용합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png)

4.  설치 파일을 실행할지 또는 저장할지 묻는 메시지가 표시되면 **실행**을 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png)

5.  웹 플랫폼 설치 관리자에서 **설치**를 클릭하여 설치를 계속합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png)

6.  설치가 완료되면 개발을 시작하는 데 필요한 내용이 모두 준비된 것입니다. SDK에는 Visual Studio에서 Azure 응용 프로그램을 쉽게 개발할 수 있는 도구가 포함되어 있습니다. Visual Studio가 설치되어 있지 않으면 무료로 제공되는 Visual Studio Express for Web도 설치해야 합니다.

네임스페이스 설정서비스 버스 네임스페이스 설정
----------------------------------------------

다음 단계에서는 서비스 네임스페이스를 만들고 공유 비밀 키를 얻습니다. 서비스 네임스페이스는 서비스 버스를 통해 노출된 각 응용 프로그램에 대해 응용 프로그램 경계를 제공합니다. 공유 암호 키는 서비스 네임스페이스를 만들 때 시스템에서 자동으로 생성됩니다. 서비스 네임스페이스 및 공유 암호 키 조합은 서비스 버스에 자격 증명을 제공하여 응용 프로그램에 대한 액세스를 인증합니다.

Visual Studio 서버 탐색기를 사용하여 네임스페이스 및 서비스 버스 메시징 엔터티를 관리할 수도 있지만 포털 내에서는 새 네임스페이스를 만들 수만 있습니다.

### 관리 포털을 사용하여 네임스페이스 설정

1.  [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2.  관리 포털의 왼쪽 탐색 창에서 **Service Bus**를 클릭합니다.

3.  관리 포털의 아래쪽 창에서 **만들기**를 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png)

4.  **새 네임스페이스 추가** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.
	
	![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png)

5.  네임스페이스 이름이 사용 가능한지 확인한 후 해당 네임스페이스를 호스트할 국가 또는 지역을 선택합니다(계산 리소스를 배포할 국가/지역과 같아야 함).

    중요: 응용 프로그램을 배포하도록 선택할 지역과 **같은 지역**을 선택합니다. 그러면 최상의 성능을 얻을 수 있습니다.

6.  확인 표시를 클릭합니다. 이제 시스템이 서비스 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png)

7.  주 창에서 서비스 네임스페이스의 이름을 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png)

8.  **연결 정보**를 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png)

9.  **Access 연결 정보** 창에서 **기본 발급자** 및 **기본 키** 값을 찾습니다.

10. 키를 적어 두거나 클립보드에 복사해 둡니다.

### Visual Studio 서버 탐색기를 사용하여 네임스페이스 및 메시징 엔터티 관리

관리 포털 대신 Visual Studio를 사용하여 네임스페이스를 관리하고 연결 정보를 가져오려면 [여기](http://http://msdn.microsoft.com/ko-kr/library/windowsazure/ff687127.aspx) 나오는 **Visual Studio에서 Azure에 연결하려면** 섹션에서 설명하는 절차를 따르십시오. Azure에 로그인하면 서버 탐색기의 **Microsoft Azure** 트리 아래에 있는 **Service Bus** 노드에 이미 만들어진 네임스페이스가 자동으로 채워집니다. 네임스페이스를 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭하여 연결 문자열 및 Visual Studio **속성** 창에 이 네임스페이스와 관련하여 표시되는 다른 메타데이터를 표시합니다.

**SharedAccessKey** 값을 적어 두거나 클립보드에 복사해 둡니다.

![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png)

웹 역할 만들기웹 역할 만들기
----------------------------

이 섹션에서는 응용 프로그램의 프런트 엔드를 빌드합니다. 먼저 응용 프로그램에서 표시하는 다양한 페이지를 만듭니다. 그런 다음 서비스 버스 큐에 항목을 제출하고 큐에 대한 상태 정보를 표시하는 코드를 추가합니다.

### 프로젝트 만들기

1.  관리자 권한을 사용하여 Microsoft Visual Studio 2013 또는 Microsoft Visual Studio Express를 시작합니다. 관리자 권한으로 Visual Studio를 시작하려면 **Microsoft Visual Studio 2013(또는 Microsoft Visual Studio Express)**을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 클릭합니다. 이 가이드의 뒷부분에서 설명하는 Azure 계산 에뮬레이터를 사용하려면 Visual Studio를 관리자 권한으로 실행해야 합니다.

    Visual Studio의 **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png)

2.  **설치된 템플릿**의 **Visual C\#** 아래에서 **클라우드**를 클릭한 다음 **Azure 클라우드 서비스**를 클릭합니다. 프로젝트의 이름을 **MultiTierApp**으로 지정합니다. 그런 다음 **확인**을 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg)

3.  **.NET Framework 4.5** 역할에서 **ASP.NET 웹 역할**을 두 번 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png)

4.  **Azure 클라우드 서비스 솔루션** 아래의 **WebRole1**을 가리키고 연필 아이콘을 클릭한 다음 웹 역할의 이름을 **FrontendWebRole**로 변경합니다. 그런 다음 **확인**을 클릭합니다. "FrontEnd"가 아니라 소문자 "e"를 사용하여 "Frontend"를 입력해야 합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png)

5.  **새 ASP.NET 프로젝트** 대화 상자의 **템플릿 선택** 목록에서 **MVC**를 클릭한 다음 **확인**을 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png)

6.  **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 또는 **라이브러리 패키지 참조 추가**를 클릭합니다.

7.  대화 상자의 왼쪽에서 **온라인**을 선택합니다. "**WindowsAzure**"를 검색하고 **Azure 서비스 버스** 항목을 선택합니다. 그런 다음 설치를 완료하고 이 대화 상자를 닫습니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png)

8.  이제 필요한 클라이언트 어셈블리가 참조되고 몇 가지 새 코드 파일이 추가되었습니다.

9.  **솔루션 탐색기**에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 다음 **클래스**를 클릭합니다. 이름 상자에 **OnlineOrder.cs** 이름을 입력합니다. 그런 다음 **추가**를 클릭합니다.

### 웹 역할에 대한 코드 작성

이 섹션에서는 응용 프로그램에서 표시하는 다양한 페이지를 만듭니다.

1.  Visual Studio의 **OnlineOrder.cs** 파일에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다.

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  **솔루션 탐색기**에서 **Controllers\\HomeController.cs**를 두 번 클릭합니다. 파일 맨 위에 다음 **using** 문을 추가하여 서비스 버스뿐만 아니라 방금 만든 모델에 대한 네임스페이스를 포함합니다.

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  또한 Visual Studio의 **HomeController.cs** 파일에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다. 이 코드에는 큐에 대한 항목 제출을 처리하는 메서드가 포함되어 있습니다.

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form 
                [HttpPost]
                // Attribute to help prevent cross-site scripting attacks and 
                // cross-site request forgery  
                [ValidateAntiForgeryToken] 
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.
                        
                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  **빌드** 메뉴에서 **솔루션 빌드**를 클릭합니다.

5.  이제 위에서 만든 **Submit()** 메서드에 대한 보기를 만듭니다. Submit() 메서드 내부를 마우스 오른쪽 단추로 클릭하고 **보기 추가**를 선택합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png)

6.  보기를 만들 수 있는 대화 상자가 나타납니다. **모델 클래스** 드롭다운에서 **OnlineOrder** 클래스를 선택하고 **템플릿** 드롭다운에서 **만들기**를 선택합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png)

7.  **추가**를 클릭합니다.

8.  이제 응용 프로그램의 표시 이름을 변경합니다. **솔루션 탐색기**에서 **Views\\Shared\\\_Layout.cshtml** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.

9.  나타나는 **My ASP.NET MVC Application**을 모두 **LITWARE'S Awesome Products**로 바꿉니다.

10. **"your logo here"**를 **LITWARE'S Awesome Products**로 바꿉니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png)

11. **홈**, **정보** 및 **연락처** 링크를 제거합니다. 강조 표시된 코드를 삭제합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png)

12. 마지막으로 큐에 대한 일부 정보를 포함하도록 제출 페이지를 수정합니다. **솔루션 탐색기**에서 **Views\\Home\\Submit.cshtml** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다. **Submit** 뒤에 다음 줄을 추가합니다. 지금은 **ViewBag.MessageCount**가 비어 있습니다. 나중에 채웁니다.

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>

13. 이제 UI를 구현했습니다. **F5** 키를 눌러 응용 프로그램을 실행하고 예상대로 나타나는지 확인할 수 있습니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png)

### 서비스 버스 큐에 항목을 제출하는 코드 작성

이제 큐에 항목을 제출하는 코드를 추가합니다. 먼저 서비스 버스 큐 연결 정보를 포함하는 클래스를 만듭니다. 그런 다음 **Global.aspx.cs**에서 연결을 초기화합니다. 마지막으로 앞에서 만든 제출 코드를 **HomeController.cs**에서 업데이트하여 항목을 서비스 버스 큐에 실제로 제출합니다.

1.  솔루션 탐색기에서 **FrontendWebRole**(역할이 아닌 프로젝트를 마우스 오른쪽 단추로 클릭)을 마우스 오른쪽 단추로 클릭합니다. **추가**를 클릭한 후 **클래스**를 클릭합니다.

2.  클래스 이름을 **QueueConnector.cs**로 지정합니다. **추가**를 클릭하여 클래스를 만듭니다.

3.  이제 연결 정보를 캡슐화하고 서비스 버스 큐에 대한 연결을 초기화하는 메서드를 포함하는 코드를 붙여 넣습니다. QueueConnector.cs에서 다음 코드를 붙여 넣고 **Namespace**, **IssuerName** 및 **IssuerKey**에 대한 값을 입력합니다. 이러한 값은 [관리 포털](http://manage.windowsazure.com)이나 Visual Studio 서버 탐색기의 **Service Bus** 노드에서 가져올 수 있습니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to 
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  이제 **Initialize** 메서드를 호출합니다. **솔루션 탐색기**에서 **Global.asax\\Global.asax.cs**를 두 번 클릭합니다.

5.  **Application\_Start** 메서드 맨 아래에 다음 줄을 추가합니다.

        FrontendWebRole.QueueConnector.Initialize();

6.  마지막으로 앞에서 만든 웹 코드를 업데이트하여 항목을 큐에 제출합니다. **솔루션 탐색기**에서, 앞에서 만든 **Controllers\\HomeController.cs**를 두 번 클릭합니다.

7.  **Submit()** 메서드를 다음과 같이 업데이트하여 큐에 대한 메시지 수를 가져옵니다.

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  **Submit(OnlineOrder order)** 메서드를 다음과 같이 업데이트하여 주문 정보를 큐에 제출합니다.

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);
                    
                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  이제 응용 프로그램을 다시 실행할 수 있습니다. 주문을 제출할 때마다 메시지 수가 증가합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png)

구성 관리자클라우드 구성 관리자
-------------------------------

Azure는 Microsoft 클라우드 서비스 전체에서 Azure 서비스 클라이언트(예: 서비스 버스)의 새 인스턴스를 만드는 일관된 방법을 제공하는 관리되는 API 집합을 지원합니다. 이러한 API를 사용하면 온-프레미스, Microsoft 클라우드 서비스, 웹 사이트, 영구적 VM 역할 등 응용 프로그램이 호스트되는 위치에 관계없이 이러한 클라이언트(예: **CloudBlobClient**, **QueueClient**, **TopicClient**)를 인스턴스화할 수 있습니다. 또한 이러한 API를 사용하면 이러한 클라이언트를 인스턴스화하는 데 필요한 구성 정보를 검색하고, 호출 응용 프로그램을 다시 배포하지 않고도 구성을 변경할 수 있습니다. API는 [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] 클래스에 있습니다. 클라이언트 쪽에도 API가 있습니다.

### 연결 문자열

클라이언트(예: 서비스 버스 **QueueClient**)를 인스턴스화하려면 구성 정보를 연결 문자열로 나타낼 수 있습니다. 클라이언트 쪽에는 해당 연결 문자열을 사용하여 해당 클라이언트 유형을 인스턴스화하는 **CreateFromConnectionString()** 메서드가 있습니다. 예를 들어 다음과 같은 구성 섹션을 지정합니다.

    <ConfigurationSettings>
    ...
        <Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
    </ConfigurationSettings>

다음은 연결 문자열을 검색하고, 큐를 만들며, 큐에 대한 연결을 초기화하는 코드입니다.

    QueueClient Client; 

    string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString); 

    if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

    // Initialize the connection to Service Bus Queue
    Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

다음 섹션의 코드에서는 이러한 구성 관리 API를 사용합니다.

작업자 역할 만들기작업자 역할 만들기
------------------------------------

이제 주문 제출을 처리하는 작업자 역할을 만듭니다. 이 예제에서는 **Worker Role with Service Bus Queue** Visual Studio 프로젝트 템플릿을 사용합니다. 먼저 Visual Studio의 서버 탐색기를 사용하여 필요한 자격 증명을 얻습니다.

1.  Visual Studio를 Azure 계정에 이미 연결한 경우 **Visual Studio 서버 탐색기를 사용하여 네임스페이스 설정** 섹션에서 설명한 대로 5단계로 건너뜁니다.

2.  Visual Studio의 메뉴 모음에서 **보기**를 선택한 다음 **서버 탐색기**를 클릭합니다. **Service Bus** 노드가 다음 그림과 같이 서버 탐색기 계층 구조 내 **Azure** 아래에 나타납니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png)

3.  서버 탐색기에서 **Azure**를 확장한 다음 **Service Bus**를 마우스 오른쪽 단추로 클릭하고 **새 연결 추가**를 클릭합니다.

4.  **연결 추가** 대화 상자에서 서비스 네임스페이스의 이름, 발급자 이름 및 발급자 키를 입력합니다. 그런 다음 **확인**을 클릭하여 연결합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png)

5.  Visual Studio의 **솔루션 탐색기**에서 **MultiTierApp** 프로젝트 아래의 **역할** 폴더를 마우스 오른쪽 단추로 클릭합니다.

6.  **추가**를 클릭한 후 **새 작업자 역할 프로젝트**를 클릭합니다. **새 역할 프로젝트 추가** 대화 상자가 나타납니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png)

7.  다음 그림과 같이 **새 역할 프로젝트 추가 대화 상자**에서 **Worker Role with Service Bus Queue**를 클릭합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png)

8.  **이름** 상자에서 프로젝트 이름을 **OrderProcessingRole**로 지정합니다. 그런 다음 **추가**를 클릭합니다.

9.  서버 탐색기에서 서비스 네임스페이스 이름을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다. Visual Studio **속성** 창의 첫 번째 항목에는 필요한 권한 부여 자격 증명을 포함하는 서비스 네임스페이스 끝점으로 채워지는 연결 문자열이 포함됩니다. 예를 들어 다음 그림을 참조하십시오. **ConnectionString**을 두 번 클릭한 다음 **Ctrl+C**를 눌러 이 문자열을 클립보드에 복사합니다.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png)

10. 솔루션 탐색기에서, 7단계에서 만든 **OrderProcessingRole**을 마우스 오른쪽 단추로 클릭합니다. 이때 클래스가 아니라 **역할** 아래의 **OrderProcessingRole**을 마우스 오른쪽 단추로 클릭해야 합니다. 그런 다음 **속성**을 클릭합니다.

11. **속성** 대화 상자의 **설정** 탭에서 **Microsoft.ServiceBus.ConnectionString**에 대한 **값** 상자 내부를 클릭한 다음 8단계에서 복사한 끝점 값을 붙여 넣습니다.

	![][25]

1.  **OnlineOrder** 클래스를 만들어 큐에서 처리할 때 주문을 나타냅니다. 이미 만든 클래스를 다시 사용할 수 있습니다. 솔루션 탐색기에서 **OrderProcessingRole** 프로젝트(역할이 아닌 프로젝트를 마우스 오른쪽 단추로 클릭)를 마우스 오른쪽 단추로 클릭합니다. **추가**를 클릭한 후 **기존 항목**을 클릭합니다.

2.  **FrontendWebRole\\Models**에 대한 하위 폴더로 이동하고 **OnlineOrder.cs**를 두 번 클릭하여 이 프로젝트에 추가합니다.

3.  다음 코드와 같이, WorkerRole.cs에서 **WorkerRole.cs**의 **QueueName** 변수 값을 `"ProcessingQueue"`에서 `"OrdersQueue"`로 바꿉니다.

        // The name of your queue
        const string QueueName = "OrdersQueue";

4.  WorkerRole.cs 파일 맨 위에 다음 using 문을 추가합니다.

        using FrontendWebRole.Models;

5.  `OnMessage` 호출 내의 `Run()` 함수에서 `try` 절 내에 다음 코드를 추가합니다.

        Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
        // View the message as an OnlineOrder
        OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
        Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
        receivedMessage.Complete();

6.  응용 프로그램을 완성했습니다. 앞에서 수행한 대로 F5 키를 눌러 전체 응용 프로그램을 테스트할 수 있습니다. 작업자 역할에서 큐의 항목을 처리하고 완료로 표시하므로 메시지 수가 증가하지 않습니다. Azure 계산 에뮬레이터 UI를 표시하여 작업자 역할의 추적 출력을 확인할 수 있습니다. 이 작업은 작업 표시줄의 알림 영역에 있는 에뮬레이터 아이콘을 마우스 오른쪽 단추로 클릭하고 **Show Compute Emulator UI**를 선택하여 수행할 수 있습니다.

	![][19]

	![][20]

다음 단계다음 단계
------------------

서비스 버스에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 서비스 버스](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee732537.aspx)
-   [서비스 버스 사용 방법](/en-us/manage/services/service-bus/)
-   [서비스 버스 큐를 사용하는 방법](/en-us/develop/net/how-to-guides/service-bus-queues/)

다중 계층 시나리오에 대해 자세히 알아보거나 클라우드 서비스에 응용 프로그램을 배포하는 방법을 알아보려면 다음을 참조하십시오.

-   [저장소 테이블, 큐 및 Blob을 사용하는 .NET 다중 계층 응용 프로그램](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)

Azure 클라우드 서비스가 아닌 Azure 웹 사이트에서 다중 계층 응용 프로그램의 프런트 엔드를 구현할 수 있습니다. 웹 사이트와 클라우드 서비스의 차이점에 대한 자세한 내용은 [Azure 실행 모델](http://www.windowsazure.com/ko-kr/develop/net/fundamentals/compute/)(영문)을 참조하십시오.

이 자습서에서 만든 응용 프로그램을 클라우드 서비스 웹 역할이 아닌 표준 웹 프로젝트로 구현하려면 다음 차이점을 고려하여 이 자습서의 단계를 수행하십시오.

1.  프로젝트를 만들 때 **클라우드** 범주의 **클라우드 서비스** 템플릿이 아니라 **웹** 범주의 **ASP.NET MVC 4 웹 응용 프로그램** 프로젝트 템플릿을 선택합니다. 그런 다음 **클라우드 구성 관리자** 섹션에 도달할 때까지 MVC 응용 프로그램을 만드는 것과 동일한 지침을 수행합니다.

2.  작업자 역할을 만들 때 새로운 별도의 솔루션에 만들며, 이는 웹 역할에 대한 원래 지침과 유사합니다. 그러나 클라우드 서비스 프로젝트에서는 작업자 역할만 만듭니다. 그런 다음 작업자 역할을 만드는 것과 동일한 지침을 수행합니다.

3.  프런트 엔드와 백 엔드를 별도로 테스트하거나 별도의 Visual Studio 인스턴스에서 둘 다를 동시에 실행할 수 있습니다.

Azure 웹 사이트에 프런트 엔드를 배포하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포](http://www.windowsazure.com/ko-kr/develop/net/tutorials/get-started/)(영문)를 참조하십시오. Azure 클라우드 서비스에 백 엔드를 배포하는 방법에 대한 자세한 내용은 [저장소 테이블, 큐 및 Blob을 사용하는 .NET 다중 계층 응용 프로그램](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)(영문)을 참조하십시오.


[0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Azure Management Portal]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [sbmsdn]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee732537.aspx  
  [sbwacom]: /en-us/manage/services/service-bus/  
  [sbwacomqhowto]: /en-us/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://www.windowsazure.com/ko-kr/develop/net/fundamentals/compute/