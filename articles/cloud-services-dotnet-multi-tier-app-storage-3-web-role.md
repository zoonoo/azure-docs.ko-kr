<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Azure 전자 메일 서비스 응용 프로그램에 대한 웹 역할 구축 - 3/5

Azure 전자 메일 서비스 샘플 응용 프로그램을 빌드 및 배포하는 방법을 보여 주는 5개 자습서 시리즈의 세 번째 자습서입니다. 응용 프로그램 및 자습서 시리즈에 대한 자세한 내용은 [시리즈의 첫 번째 자습서][]를 참조하세요.

이 자습서에서는 다음에 대해 알아봅니다.

-   웹 역할과 작업자 역할을 사용하여 클라우드 서비스 프로젝트가 포함된 솔루션을 만드는 방법
-   MVC 5 컨트롤러 및 뷰에서 Azure 테이블, Blob 및 큐 작업을 수행하는 방법
-   Azure 테이블 작업을 수행할 때 동시성 충돌을 처리하는 방법

## 이 자습서의 부분

-   [Visual Studio 솔루션 만들기][]
-   [저장소 클라이언트 라이브러리 NuGet 패키지 업데이트][]
-   [저장소 에뮬레이터를 사용하도록 프로젝트 구성][]
-   [추적 구성 및 다시 시작 처리][]
-   [Application\_Start 메서드에 테이블, 큐 및 Blob 컨테이너를 만드는 코드 추가][]
-   [메일 그룹 만들기 및 테스트][]
-   [구독자 컨트롤러와 뷰 만들기 및 테스트][]
-   [메시지 컨트롤러와 뷰 만들기 및 테스트][]
-   [구독 취소 컨트롤러와 뷰 만들기 및 테스트][]
-   [다음 단계][]

## <a name="cloudproject"></a>Visual Studio 솔루션 만들기

먼저 웹 프런트 엔드에 대한 프로젝트와 백 엔드 Azure 작업자 역할 중 하나에 대한 프로젝트가 포함된 Visual Studio 솔루션을 만듭니다. 나중에 두 번째 작업자 역할을 추가할 것입니다.

### 웹 역할 및 작업자 역할을 사용하여 클라우드 서비스 프로젝트 만들기

1.  Visual Studio를 시작합니다.

2.  **파일** 메뉴에서 **새 프로젝트**를 선택합니다.

    ![새 프로젝트 메뉴][]

3.  **C\#**을 확장하고 **설치된 템플릿** 아래의 **클라우드**를 선택한 후 **Azure 클라우드 서비스**를 선택합니다.

4.  응용 프로그램 이름을 **AzureEmailService**로 지정하고 **확인**을 클릭합니다.

    ![새 프로젝트 대화 상자][]

5.  **새 Azure 클라우드 서비스** 대화 상자에서 **ASP.NET 웹 역할**을 선택하고 오른쪽을 가리키는 화살표를 클릭합니다.

    ![새 Azure 클라우드 프로젝트 대화 상자][]

6.  오른쪽 열에서 **WebRole1**을 포인터로 가리킨 후 연필 아이콘을 클릭하여 웹 역할의 이름을 변경합니다.

7.  MvcWebRole을 새 이름으로 입력하고 Enter 키를 누릅니다.

    ![새 Azure 클라우드 프로젝트 대화 상자 - 웹 역할 이름 바꾸기][]

8.  동일한 절차에 따라 **작업자 역할**을 추가하고 이름을 WorkerRoleA로 지정한 후 **확인**을 클릭합니다.

    ![새 Azure 클라우드 프로젝트 대화 상자 - 작업자 역할 추가][]

9.  **새 ASP.NET 프로젝트** 대화 상자에서 **MVC** 템플릿을 선택하고 **Web API** 확인란을 선택한 후 **인증 변경**을 클릭합니다.

    ![새 프로젝트 대화 상자][1]

10. **인증 변경** 대화 상자에서 **인증 없음**, **확인**을 차례로 클릭합니다.

    ![인증 없음][]

11. **새 ASP.NET 프로젝트** 대화 상자에서 **확인**을 클릭합니다.

### 페이지 머리글, 메뉴 및 바닥글 설정

이 섹션에서는 관리자 웹 UI의 모든 페이지에 표시되는 머리글, 바닥글 및 메뉴 항목을 업데이트합니다. 응용 프로그램에는 메일 그룹, 메일 그룹 구독자 및 메시지에 대해 각각 하나씩 세 개의 관리자 웹 페이지 집합이 있습니다.

1.  [완료된 솔루션][](영문)을 아직 다운로드하지 않은 경우 다음 단계로 진행하기 전에 다운로드합니다.

    이 자습서 나머지 부분에서 코드를 추가해야 할 때는 코드 조각을 복사하여 붙여 넣는 대신 다운로드한 프로젝트에서 새 프로젝트로 파일을 복사합니다. 자습서에서는 복사할 코드의 중요 부분을 보여 주고 설명합니다.

    다운로드한 프로젝트의 파일을 추가하려면 파일을 추가할 프로젝트나 폴더를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가 - 기존 항목**을 선택합니다. 그런 다음, 완료된 프로젝트를 다운로드한 위치로 이동하고 원하는 파일을 선택한 후 **추가**를 클릭합니다. **대상 파일 있음** 대화 상자가 나타나는 경우 **예**를 클릭합니다.

2.  MvcWebRole 프로젝트에서 다운로드한 프로젝트의 *Views\\Shared\_Layout.cshtml* 파일을 추가합니다(*Views* 아래에 있는 *Shared* 폴더를 마우스 오른쪽 단추로 클릭하여 파일 추가).

    머리글과 바닥글뿐만 아니라 메일 그룹, 메시지, 구독자 페이지 등의 메뉴 항목이 추가됩니다.

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### 로컬에서 응용 프로그램 실행

1.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다.

    Azure 클라우드 서비스 프로젝트가 아닌 웹 프로젝트를 시작하는 데 익숙하다면 브라우저에 홈페이지가 표시되기 전에 좀 더 오래 걸린다는 것을 알 수 있을 것입니다.

    ![홈페이지][]

    지연되는 이유는 Visual Studio가 Azure 계산 에뮬레이터와 Azure 저장소 에뮬레이터를 시작하기 때문입니다. Windows 시스템 트레이에서 계산 에뮬레이터 아이콘을 확인할 수 있습니다.

    ![시스템 트레이의 계산 에뮬레이터][]

2.  브라우저를 닫습니다.

## <a name="updatescl"></a>저장소 클라이언트 라이브러리 NuGet 패키지 업데이트

Azure 저장소 테이블, 큐 및 Blob 작업에 사용하는 API 프레임워크는 SCL(저장소 클라이언트 라이브러리)입니다. 이 API는 클라우드 서비스 프로젝트 템플릿의 NuGet 패키지에 포함되어 있습니다. 하지만 프로젝트 템플릿이 만들어진 후에 자주 SCL 업데이트가 릴리스되므로 항상 SCL NuGet 패키지의 업데이트를 사용할 수 있는지 여부를 확인하는 것이 좋습니다.

1.  Visual Studio의 **도구** 메뉴에서 **라이브러리 패키지 관리자**를 마우스로 가리키고 **솔루션에 대한 NuGet 패키지 관리**를 클릭합니다.

    ![메뉴의 솔루션에 대한 NuGet 패키지 관리][]

2.  **NuGet 패키지 관리** 대화 상자의 왼쪽 창에서 **업데이트**를 선택하고 **Azure 저장소** 패키지까지 아래로 스크롤한 후 **업데이트**를 클릭합니다.

    ![NuGet 패키지 관리 대화 상자의 Azure 저장소 패키지][]

3.  **프로젝트 선택** 대화 상자에서 두 프로젝트가 모두 선택되었는지 확인한 후 **확인**을 클릭합니다.

    ![프로젝트 선택 대화 상자에서 두 프로젝트 모두 선택][]

4.  사용 조건에 동의하여 패키지 설치를 완료한 후 **NuGet 패키지 관리** 대화 상자를 닫습니다.

## <a name="configurestorage"></a>저장소 에뮬레이터를 사용하도록 프로젝트 구성

나중에 추가할 웹 역할 코드 및 작업자 역할 코드에는 Azure 저장소에 연결하는 StorageConnectionString이라는 연결 문자열이 사용됩니다. 이 섹션에서는 역할 속성에 설정을 추가하고 저장소 에뮬레이터를 사용하도록 설정을 구성합니다. 이 시리즈의 두 번째 자습서에서는 Azure 저장소 계정을 사용하도록 연결 문자열을 구성하는 방법을 보여 줍니다.

1.  **솔루션 탐색기**에서 **AzureEmailService** 클라우드 프로젝트의 **역할** 아래에 있는 **MvcWebRole**을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

    ![웹 역할 속성][]

2.  **서비스 구성** 드롭다운 목록에서 **모든 구성**이 선택되어 있는지 확인합니다.

3.  **설정** 탭을 선택한 후 **설정 추가**를 클릭합니다.

4.  **이름** 열에 "StorageConnectionString"을 입력합니다.

5.  **형식** 드롭다운 목록에서 **연결 문자열**을 선택합니다.

6.  줄의 오른쪽 끝에 있는 줄임표(**...**) 단추를 클릭하여 **저장소 계정 연결 문자열** 대화 상자를 엽니다.

    ![마우스 오른쪽 단추 클릭 속성][]

7.  **저장소 연결 문자열 만들기** 대화 상자에서 **Azure 저장소 에뮬레이터** 라디오 단추를 클릭한 다음 **확인**을 클릭합니다.

    `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 연결 문자열은 기본적으로 저장소 에뮬레이터로 설정되므로 이것을 변경할 필요가 없습니다.

8.  MvcWebRole에 사용한 것과 동일한 절차에 따라 WorkerRoleA 역할에 대한 저장소 연결 문자열을 추가합니다.

**설정 추가** 단추를 사용하여 새 설정을 추가한 경우 *ServiceDefinition.csdf* 파일과 *.cscfg* 구성 파일 2개의 XML에 새 설정이 추가되었습니다. Visual Studio에서 *ServiceDefinition.csdf* 파일에 다음 XML을 추가합니다.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

각 *.cscfg* 구성 파일에는 다음 XML이 추가됩니다.

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

*ServiceDefinition.csdf* 파일과 두 개의 *.cscfg* 구성 파일에 수동으로 설정을 추가할 수 있지만 연결 문자열에 속성 편집기를 사용하면 다음과 같은 이점이 있습니다.

-   한 장소에서만 새 설정을 추가하면 세 개의 파일에 모두 올바른 설정 XML이 추가됩니다.
-   세 개의 설정 파일에 맞는 XML이 생성됩니다. *ServiceDefinition.csdf* 파일은 각 *.cscfg* 구성 파일에 포함되어야 하는 설정을 정의합니다. *ServiceDefinition.csdf* 파일과 *.cscfg* 구성 파일 두 개의 설정이 일치하지 않는 경우 Visual Studio에서 다음 오류 메시지가 표시될 수 있습니다. *현재 서비스 모델이 동기화되지 않았습니다. 서비스 구성과 정의 파일이 둘 다 유효한지 확인하세요.*

    ![잘못된 서비스 구성 및 정의 파일 오류][]

이 오류가 표시되는 경우 파일을 편집하여 수동으로 불일치 문제를 해결할 때까지 속성 편집기가 작동하지 않습니다.

## <a name="tracing"></a>추적 구성 및 다시 시작 처리

1.  MvcWebRole 프로젝트에서 다운로드한 프로젝트의 *WebRole.cs* 파일을 추가합니다.

로깅을 구성하는 메서드가 추가되어 웹 역할이 시작되면 실행되는 `OnStart` 메서드에서 호출됩니다. 새 `ConfigureDiagnostics` 메서드의 코드에 대해서는 [두 번째 자습서l][]에서 설명합니다.

그러면 웹 역할이 종료될 예정임을 통지받으면 실행되는 코드도 추가됩니다. Azure 클라우드 서비스 응용 프로그램은 운영 체제 업데이트를 위해 대략 매달 두 번 다시 시작됩니다. OS 업데이트에 대한 자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작][](영문)을 참조하세요. 웹 응용 프로그램을 종료하는 경우 `OnStop` 이벤트가 발생합니다. Visual Studio에서 만든 웹 역할 보일러 플레이트는 `OnStop` 메서드를 재정의하지 않으므로 종료되기 전에 응용 프로그램에서 몇 초 내에 HTTP 요청 처리를 마쳐야 합니다. 종료가 정상적으로 처리되도록 `OnStop` 메서드를 재정의하는 코드를 추가할 수 있습니다.

방금 추가한 파일에는 `OnStop` 메서드 재정의가 포함되어 있습니다.

        public override void OnStop()
        {
            Trace.TraceInformation("OnStop called from WebRole");
            var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
            while (rcCounter.NextValue() > 0)
            {
                Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
                System.Threading.Thread.Sleep(1000);
            }
        }

응용 프로그램이 종료되기 전에 최대 5분 내에 `OnStop` 메서드를 종료해야 합니다. 현재 요청을 처리할 최대 시간을 응용 프로그램에 제공하기 위해 `OnStop` 메서드에 5분 대기 모드 호출을 추가할 수 있지만 응용 프로그램 크기가 올바르게 조정된 경우 5분보다 훨씬 짧은 시간에 나머지 요청을 처리할 수 있어야 합니다. 응용 프로그램이 가능한 한 빨리 다시 시작되고 요청 처리를 계속할 수 있도록 최대한 빨리 중지되는 것이 좋습니다.

Azure가 역할을 오프라인으로 전환하면 부하 분산 장치에서 역할 인스턴스로 요청 전송을 중지하고 그 후에 `OnStop` 메서드가 호출됩니다. 다른 역할 인스턴스가 없는 경우 역할 종료가 완료되고 다시 시작될 때까지(일반적으로 몇 분 정도 걸림) 요청이 처리되지 않습니다. Azure 서비스 수준 계약에서 작동 시간 보증을 이용하려면 각 역할 인스턴스를 두 개 이상 사용하도록 요구하는 것은 이런 이유 때문입니다.

표시된 `OnStop` 메서드 코드에서는 `Requests Current`에 대한 ASP.NET 성능 카운터가 생성됩니다. `Requests Current` 카운터 값에는 큐에 대기 중이거나, 현재 실행 중이거나, 클라이언트에 쓰기 위해 대기 중인 요청 등 현재 요청 수가 포함됩니다. `Requests Current` 값은 초 단위로 확인되며, 0까지 떨어지면 `OnStop` 메서드가 반환됩니다. `OnStop` 반환 시 역할이 종료됩니다.

[주문형 전송][]을 수행하지 않고 `OnStop` 메서드에서 호출된 경우에는 추적 데이터가 저장되지 않습니다. 원격 데스크톱 연결에서 [dbgview][] 유틸리티를 사용하여 실시간으로 `OnStop` 추적 정보를 볼 수 있습니다.

## <a name="createifnotexists"></a>Application\_Start 메서드에 테이블, 큐 및 Blob 컨테이너를 만드는 코드 추가

웹 응용 프로그램은 `MailingList` 테이블, `Message` 테이블, `azuremailsubscribequeue` 큐 및 `azuremailblobcontainer` Blob 컨테이너를 사용합니다. Azure 저장소 탐색기 등의 도구를 사용하여 이러한 항목을 수동으로 만들 수 있지만 새 저장소 계정으로 응용 프로그램을 사용할 때마다 수동으로 만들어야 합니다. 이 섹션에서는 응용 프로그램을 시작할 때 실행되고 필요한 테이블, 큐 및 Blob 컨테이너가 있는지 확인한 후 없으면 새로 만드는 코드를 추가합니다.

*WebRole.cs* 파일의 `OnStart` 메서드나 *Global.asax* 파일에 이 일회성 시작 코드를 추가할 수 있습니다. 이 자습서에서는 *Global.asax* 파일의 Azure 저장소를 초기화합니다.

1.  **솔루션 탐색기**에서 MvcWebRole 프로젝트를 마우스 오른쪽 단추로 클릭하고 다운로드한 프로젝트의 *Global.asax.cs* 파일을 추가합니다.

이제 `Application_Start` 메서드에서 호출되는 새 메서드를 추가했습니다.

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();

            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();

            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }

다음 섹션에서는 웹 응용 프로그램의 구성 요소를 빌드하며 먼저 테이블, 큐 및 Blob 컨테이너를 수동으로 만들지 않고도 개발 저장소 또는 저장소 계정으로 테스트할 수 있습니다.

## <a name="mailinglist"></a>메일 그룹 컨트롤러와 뷰 만들기 및 테스트

**Mailing List** 웹 UI는 관리자가 "Contoso University History Department announcements", "Fabrikam Engineering job postings" 등의 메일 그룹을 만들고 편집 및 표시하는 데 사용합니다.

### Models 폴더에 MailingList 엔터티 클래스 추가

`MailingList` 엔터티 클래스는 메일 그룹으로 전송된 전자 메일의 "보낸 사람" 전자 메일 주소, 메일 그룹 설명 등의 메일 그룹 정보가 포함된 `MailingList` 테이블의 행에 사용됩니다.

1.  MVC 프로젝트의 `Models` 폴더에서 다운로드한 프로젝트의 *MailingList.cs* 파일을 추가합니다.

    메일 그룹 테이블에서 메일 그룹 행을 읽고 작성하는 데 `MailingList` 엔터티 클래스를 사용합니다.

        public class MailingList : TableEntity
        {
            public MailingList()
            {
                this.RowKey = "mailinglist";
            }

            [Required]
            [RegularExpression(@"[\w]+",
             ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
            [Display(Name = "List Name")]
            public string ListName
            {
                get
                {
                    return this.PartitionKey;
                }
                set
                {
                    this.PartitionKey = value;
                }
            }

            [Required]
            [Display(Name = "'From' Email Address")]
            public string FromEmailAddress { get; set; }

            public string Description { get; set; }
        }

    Azure 저장소 API에서는 테이블 작업을 위한 엔터티 클래스가 [TableEntity][]에서 파생되어야 합니다. `TableEntity`는 `PartitionKey`, `RowKey`, `TimeStamp`, `ETag` 필드를 정의합니다. `TimeStamp`, `ETag` 속성은 시스템에서 사용됩니다. 자습서의 뒷부분에서 `ETag` 속성이 동시성 처리에 사용되는 방법을 확인합니다.

    미리 정의된 모델 클래스를 사용하는 대신 키 값 쌍의 사전 컬렉션으로 테이블 행 작업을 수행하려는 경우 사용할 [DynamicTableEntity][] 클래스도 있습니다. 자세한 내용은 [Azure 저장소 클라이언트 라이브러리 2.0 테이블 심층 검토][](영문)를 참조하세요.

    `mailinglist` 테이블 파티션 키는 메일 그룹 이름입니다. 이 엔터티 클래스에서는 `PartitionKey` 속성(`TableEntity` 클래스에 정의됨) 또는 `ListName` 속성(`MailingList` 클래스에 정의됨)을 사용하여 파티션 키 값에 액세스할 수 있습니다. `ListName` 속성은 `PartitionKey`를 지원 변수로 사용합니다. `ListName` 속성을 정의하면 코드에서 더 설명적인 변수 이름을 사용할 수 있으며, `ListName` 속성에 형식 지정 및 유효성 검사 DataAnnotations 특성을 추가할 수 있으므로 웹 UI를 더 쉽게 프로그래밍할 수 있지만 `PartitionKey` 속성에 직접 추가할 수는 없습니다.

    `ListName` 속성의 `RegularExpression` 특성을 사용하면 MVC에서 사용자 입력의 유효성을 검사하여 입력된 목록 이름에 영숫자 문자나 밑줄만 포함되어 있는지 확인합니다. 이 제한은 URL의 쿼리 문자열에 쉽게 사용할 수 있도록 목록 이름을 간단하게 유지하기 위해 구현되었습니다.

    > [WACOM.NOTE] 목록 이름 형식을 덜 제한적으로 설정하려는 경우 쿼리 문자열에 사용될 때 다른 문자와 URL로 인코드된 목록 이름을 허용할 수 있습니다. 그러나 특정 문자는 Azure 테이블 파티션 키 또는 행 키에 허용되지 않으며 적어도 이러한 문자는 제외해야 합니다. 허용되지 않거나 파티션 키 또는 행 키 필드에서 문제를 발생시키는 문자에 대한 자세한 내용은 [테이블 서비스 데이터 모델 이해][] 및 [PartitionKey 또는 RowKey의 % 문자][]를 참조하세요.

    `MailingList` 클래스는 이 테이블의 모든 메일 그룹 행에 해당 값이 행 키로 포함되어 있으므로 `RowKey`를 하드 코드된 문자열 "mailinglist"로 설정하는 기본 생성자를 정의합니다. 테이블 구조에 대한 설명은 [시리즈의 첫 번째 자습서][]를 참조하세요. 이 테이블의 구독자 행에 대한 행 키인 전자 메일 주소와 다르기만 하면 모든 상수 값을 이 목적으로 선택할 수 있습니다.

    새 `MailingList` 엔터티를 만들 때 목록 이름과 "보낸 사람" 전자 메일 주소는 항상 입력해야 하므로 `Required` 특성이 없습니다.

    `Display` 특성은 MVC UI의 필드에 사용할 기본 캡션을 지정합니다.

### MailingList MVC 컨트롤러 추가

1.  MVC 프로젝트의 *Controllers* 폴더에서 다운로드한 프로젝트의 *MailingListController.cs* 파일을 추가합니다.

    컨트롤러의 기본 생성자는 `mailinglist` 테이블 작업에 사용하는 `CloudTable` 개체를 만듭니다.

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    코드는 저장소 계정에 연결하기 위해 클라우드 서비스 프로젝트 설정 파일에서 Azure 저장소 계정의 자격 증명을 가져옵니다. 컨트롤러를 테스트하기 전에 이 자습서의 뒷부분에서 이러한 설정을 구성합니다.

    다음은 메일 그룹 항목 편집 등을 위해 컨트롤러가 `MailingList` 테이블의 특정 메일 그룹 항목을 조회해야 할 때마다 호출되는 `FindRowAsync` 메서드입니다. 코드는 전달된 파티션 키 및 행 키 값을 사용하여 단일 `MailingList` 엔터티를 검색합니다. 이 컨트롤러에서 편집하는 행은 "MailingList"가 행 키로 포함된 행이므로 행 키에 "MailingList"가 하드 코드되었을 수 있지만 파티션 키와 행 키를 둘 다 지정하는 것이 모든 컨트롤러의 `FindRow` 메서드에 사용되는 패턴입니다.

    > [WACOM.NOTE] 응용 프로그램은 서버 리소스를 효율적으로 사용하기 위해 웹 역할의 I/O 작업을 위한 ASP.NET 4.5 비동기 코드를 사용합니다. 웹 응용 프로그램의 비동기 코드에 대한 자세한 내용은 [.NET 4.5의 비동기 지원을 사용하여 호출 차단 방지][](영문)를 참조하세요.

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    이 `FindRow` 메서드의 코드는 메일 그룹 행을 반환합니다. `Subscriber` 컨트롤러의 해당 `FindRow` 메서드의 코드는 동일한 `mailinglist` 테이블에서 구독자 행을 반환합니다. 두 메서드에 있는 코드는 [TableOperation.Retrieve][] 메서드와 함께 사용되는 모델 형식이라는 점을 제외하고는 동일합니다.

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    `TableOperation.Retrieve` 메서드에서 반환된 `TableOperation` 개체는 쿼리에서 반환해야 하는 행의 스키마(속성)를 지정합니다. 단일 테이블에 여러 행의 각기 다른 스키마가 포함될 수 있습니다. 일반적으로 행을 읽을 때는 행을 생성하는 데 사용된 것과 동일한 모델 유형을 지정합니다.

    **인덱스** 페이지에는 메일 그룹 행이 모두 표시되므로 `Index` 메서드의 쿼리에서 "mailinglist"가 행 키로 포함된 `MailingList` 엔터티를 모두 반환합니다(테이블의 다른 행은 전자 메일 주소를 행 키로 사용하며 구독자 정보를 포함함).

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    `ExecuteQuerySegmentedAsync` 메서드는 대규모 결과 집합을 세그먼트로 나눕니다. 최대 1,000개의 행을 반환합니다. 1,000개가 넘는 행을 검색하는 쿼리를 실행하면 1,000개의 행과 연속 토큰이 나타납니다. 연속 토큰을 사용하여 이전 쿼리가 중단한 위치에서 시작하는 다른 쿼리를 실행할 수 있습니다. 표시된 코드는 샘플 응용 프로그램에 맞춰 단순화된 것입니다. 모든 세그먼트를 하나의 목록에 수집합니다. 프로덕션 응용 프로그램의 경우 페이징 코드를 구현합니다. 큰 결과 집합 및 연속 토큰에 대한 자세한 내용은 [Azure 테이블을 활용하는 방법][](영문) 및 [Azure 테이블: 연속 토큰 필요][](영문)를 참조하세요.

    `OperationContext` 개체를 만들 때는 Azure 저장소에서 작성되는 로그에 포함될 고유 식별자를 제공하기 위해 `ClientID` 속성 값을 설정할 수 있습니다. 저장소 서비스 활동을 발생시킨 코드에 대한 저장소 작업 로그를 추적할 때 이 식별자를 사용할 수 있습니다. Azure 저장소 로깅에 대한 자세한 내용은 [Azure 저장소 로깅: 로그를 사용하여 저장소 요청 추적][](영문)을 참조하세요.

    SCL 2.1 이상 API에서는 테이블 쿼리에 LINQ를 사용할 수도 있습니다. LINQ를 사용하는 방법을 보여 주는 코드 샘플은 [PhluffyFotos][](영문)를 참조하세요.

    재시도 정책을 지정하지 않으면 API에서 시간 초과 제한을 기하급수적으로 증가시켜 자동으로 3회 다시 시도합니다. 페이지가 나타날 때까지 사용자가 기다리는 웹 인터페이스의 경우 이로 인해 대기 시간이 너무 길어질 수 있습니다. 따라서 이 코드는 선형 다시 시도(시간 초과 제한이 매번 증가하지 않음) 및 사용자가 기다리기에 적합한 시간 초과 제한을 지정합니다. 재시도 정책은 `ExecuteQuerySegmentedAsync` 메서드에 전달된 `webUIRetryPolicy` 개체에서 지정됩니다. `webUIRetryPolicy` 개체는 컨트롤러 생성자에서 정의됩니다.

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    `Index` 메서드에는 시간 초과 조건을 처리하도록 설계된 try-catch 블록이 들어 있습니다.

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    사용자가 **만들기** 페이지에서 **만들기** 단추를 클릭하면 MVC 모델 바인더가 뷰에 입력된 입력에서 `MailingList` 엔터티를 만들고 `HttpPost Create` 메서드가 엔터티를 테이블에 추가합니다.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    **편집** 페이지의 경우 `HttpPost Edit` 메서드는 행을 업데이트합니다.

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    `HttpPost Edit` 메서드에서 catch 블록은 동시성 오류를 처리합니다.

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
                if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                {
                    ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                }
                if (currentMailingList.Description != editedMailingList.Description)
                {
                    ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                }
                ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                    + "was modified by another user after you got the original value. The "
                    + "edit operation was canceled and the current values in the database "
                    + "have been displayed. If you still want to edit this record, click "
                    + "the Save button again. Otherwise click the Back to List hyperlink.");
                 ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
            }
            else
            {
                throw; 
            }
        }

    사용자가 편집을 위해 메일 그룹을 선택하는 경우 브라우저에서 **편집** 페이지가 표시되는 동안 다른 사용자가 동일한 메일 그룹을 편집하면 동시성 예외가 발생합니다. 이 경우 코드에 경고 메시지가 표시되고 다른 사용자가 변경한 필드가 어느 것인지 표시됩니다. TSL API는 `ETag`를 사용하여 동시성 충돌을 확인합니다. 테이블 행이 업데이트될 때마다 `ETag` 값이 변경됩니다. 편집할 행을 가져올 때 `ETag` 값을 저장하고 저장해 둔 `ETag` 값을 업데이트 또는 삭제 작업 실행 시에 전달합니다. 보기에는 `Edit` 값이 담긴 숨은 필드가 있습니다.) 업데이트 작업에서 업데이트 중인 레코드에 대한 `ETag` 값이 업데이트 작업에 전달한 `ETag` 값과 다르다는 것을 발견하는 경우 동시성 예외가 발생합니다. 동시성 충돌에 개의치 않는 경우 업데이트 작업에 전달하는 엔터티에서 ETag 필드를 별표("\*")로 설정하면 됩니다. 그러면 충돌이 무시됩니다.

    참고: HTTP 412는 동시성 오류에 고유한 오류가 아닙니다. SCL API에 의한 다른 오류에 대해서도 발생합니다.

    **삭제** 페이지의 경우 `HttpPost Delete` 메서드는 `MailingList` 행을 삭제하며, 이때 `MailingList` 테이블과 연관된 `Subscriber` r행도 함께 삭제합니다.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    많은 수의 구독자를 삭제해야 하는 경우 이 코드는 레코드를 일괄 처리로 삭제합니다. 행 하나를 삭제하는 트랜잭션 비용은 100개의 행을 일괄 처리로 삭제하는 것과 같습니다. 한 번의 일괄 처리에서 수행할 수 있는 최대 작업 수는 100개입니다.

    루프에서 `MailingList` 행과 `Subscriber` r행을 모두 처리하지만 이들 행을 모두 `MailingList` 엔터티 클래스로 읽어 들입니다. `Delete` 작업에 필요한 필드는 `PartitionKey`, `RowKey`, `ETag` 필드뿐이기 때문입니다.

### MailingList MVC 뷰 추가

1.  MVC 프로젝트의 *Views* 폴더에서 새 폴더를 만들고 폴더 이름을 *MailingList*로 지정합니다.

2.  새 *Views\\MailingList* 폴더에서 다운로드한 프로젝트의 4개 *.cshtml* 파일을 모두 추가합니다.

*Edit.cshtml* 파일에서 동시성 충돌을 처리하는 데 사용되는 `ETag` 값을 유지하기 위해 포함되는 숨겨진 필드를 확인합니다.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

또한 `ListName` 필드에는 `EditorFor` 도우미 대신에 `DisplayFor` 도우미가 있습니다.

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

컨트롤러에 복잡한 코드가 있어야 하므로 **편집** 페이지에서 목록 이름을 변경할 수 있도록 하지 않았습니다. `HttpPost Edit` 메서드가 기존의 메일 그룹 행 및 연결된 모든 구독자 행을 삭제하고 새로운 키 값으로 모두 다시 삽입해야 합니다. 프로덕션 응용 프로그램에서는 더 복잡한 코드가 유용하다고 결정할 수도 있습니다. 나중에 살펴보겠지만 `Subscriber` 컨트롤러는 한 번에 하나의 행만 영향을 받기 때문에 목록 이름 변경을 허용합니다.

*Index.cshtml* 파일에서 **편집** 및 **삭제** 하이퍼링크는 특정 행을 식별하기 위해 파티션 키 및 행 키 쿼리 문자열 매개 변수를 지정합니다. 엔터티의 경우 행 키가 항상 `MailingList`이므로 실제로 파티션 키만 필요하지만 모든 컨트롤러와 뷰에서 MVC 뷰 코드가 일치하도록 둘 다 유지됩니다.

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### MailingList를 기본 컨트롤러로 설정

1.  *App\_Start* 폴더의 *Route.config.cs*를 엽니다.

2.  기본값을 지정하는 줄에서 기본 컨트롤러를 "Home"에서 "MailingList"로 변경합니다.

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### 응용 프로그램 테스트

1.  Ctrl+F5를 눌러 프로젝트를 실행합니다.

    ![빈 MailingList 인덱스 페이지][]

2.  **만들기** 기능을 사용하여 일부 메일 그룹을 추가하고 **편집** 및 **삭제** 기능을 시도하여 작동하는지 확인합니다.

    ![행이 있는 MailingList 인덱스 페이지][]

## <a name="subscriber"></a><span class="short-header">구독자</span>구독자 컨트롤러와 뷰 만들기 및 테스트

**Subscriber** 웹 UI는 관리자가 메일 그룹에 새 구독자를 추가하고 기존 구독자를 편집, 표시 및 삭제하는 데 사용합니다.

### Models 폴더에 Subscriber 엔터티 클래스 추가

`Subscriber` 엔터티 클래스는 메일 그룹 구독자 정보가 포함된 `MailingList` 테이블의 행에 사용됩니다. 이러한 행에는 개인의 전자 메일 주소, 주소가 확인되었는지 여부 등의 정보가 포함됩니다.

1.  MVC 프로젝트의 *Models* 폴더에서 다운로드한 프로젝트의 *Subscriber.cs* 파일을 추가합니다.

`MailingList` 엔터티 클래스와 마찬가지로 `Subscriber` 엔터티 클래스는 `mailinglist` 테이블의 행을 읽고 쓰는 데 사용됩니다.

            public class Subscriber : TableEntity
            {
                [Required]
                public string ListName
                {
                    get
                    {
                        return this.PartitionKey;
                    }
                    set
                    {
                        this.PartitionKey = value;
                    }
                }
        
                [Required]
                [Display(Name = "Email Address")]
                public string EmailAddress
                {
                    get
                    {
                        return this.RowKey;
                    }
                    set
                    {
                        this.RowKey = value;
                    }
                }
        
                public string SubscriberGUID { get; set; }
        
                public bool? Verified { get; set; }
            }
        

`Subscriber` 행은 상수 "mailinglist" 대신 전자 메일 주소를 행 키에 사용합니다. 테이블 구조에 대한 설명은 [시리즈의 첫 번째 자습서][]를 참조하세요. 따라서 `ListName`이 `PartitionKey`를 지원 필드로 사용하는 것과 동일한 방식으로 `RowKey` 속성을 지원 필드로 사용하는 `EmailAddress` 속성이 정의됩니다. 이 경우 앞에서 설명한 대로 형식 지정 및 유효성 검사 DataAnnotations 특성을 속성에 배치할 수 있습니다.

`SubscriberGUID` 값은 `Subscriber` 엔터티를 만들 때 생성됩니다. 권한 있는 개인만 전자 메일 주소를 구독하거나 구독 취소할 수 있도록 하기 위해 구독 및 구독 취소 링크에 사용됩니다.

새 구독자에 대한 행을 처음에 만들 때는 `Verified` 값이 `false`입니다. 새 구독자가 환영 전자 메일에서 **Confirm** 하이퍼링크를 클릭한 후에만 `Verified` 값이 `true`로 변경됩니다. 구독자에 대한 `Verified` = `false`일 때 메시지가 메일 그룹으로 전송되는 경우 해당 구독자에게는 전자 메일이 전송되지 않습니다.

`Subscriber` 엔터티의 `Verified` 속성은 nullable로 정의됩니다. 쿼리에서 `Subscriber` 엔터티를 반환하도록 지정하는 경우 검색된 행 중 일부에 `Verified` 속성이 없을 수도 있습니다. 따라서 `Subscriber` 엔터티는 *Verified* 속성이 없는 테이블 행이 쿼리에서 반환되는 경우 행의 실제 콘텐츠를 더 정확하게 반영할 수 있도록 해당 `Verified` 속성을 nullable로 정의합니다. 테이블의 모든 행에 동일한 스키마가 있는 SQL Server 테이블 작업에 익숙할 수도 있습니다. Azure 저장소 테이블의 각 행은 속성 컬렉션일 뿐이며 각 행에 다른 속성 집합이 있을 수 있습니다. 예를 들어 Azure 전자 메일 서비스 샘플 응용 프로그램에서 "MailingList"가 행 키로 포함된 행에는 `Verified` 속성이 없습니다. 쿼리에서 `Verified` 속성이 없는 테이블 행을 반환하는 경우 `Subscriber` 엔터티 클래스가 인스턴스화될 때 엔터티 개체의 `Verified` 속성은 Null입니다. 속성이 nullable이 아닌 경우 `Verified` = `false`인 행과 `Verified` 속성이 없는 행에 대해 동일한 값 `false`가 사용됩니다. 따라서 Azure 테이블 작업의 모범 사례는 각기 다른 엔터티 클래스나 현재 엔터티 클래스의 각기 다른 버전을 사용하여 만든 행을 정확하게 읽기 위해 엔터티 클래스의 각 속성을 nullable로 설정하는 것입니다.

### Subscriber MVC 컨트롤러 추가

1.  **솔루션 탐색기**에서 MVC 프로젝트의 *Controllers* 폴더를 마우스 오른쪽 단추로 클릭하고 **기존 항목 추가**를 클릭합니다.

2.  샘플 응용 프로그램을 다운로드한 폴더로 이동하고 *Controllers* 폴더에서 *SubscriberController.cs* 파일을 선택한 후 **추가**를 클릭합니다. *Subscribe.cs*가 아니라 *Subscriber.cs*를 가져와야 합니다. 나중에 *Subscribe.cs*를 추가할 것입니다.

이 컨트롤러의 코드는 대부분 `MailingList` 컨트롤러에서 확인한 코드와 유사합니다. 구독자 정보가 `MailingList` 테이블에 저장되므로 테이블 이름도 같습니다.

`FindRowAsync` 메서드 외에도 `FindRow` 메서드가 있습니다. 이 메서드를 catch 블록에서 호출해야 하며 catch 블록에서 비동기 메서드를 호출할 수 없기 때문입니다.

`FindRow` 메서드 뒤에 `GetListNamesAsync` 메서드가 표시됩니다. 이 메서드는 전자 메일 주소를 가입시킬 메일 그룹을 선택할 수 있는 **만들기** 및 **편집** 페이지의 드롭다운 목록에 대한 데이터를 가져옵니다.

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

이 쿼리는 `MailingList` 컨트롤러에서 확인한 것과 동일한 쿼리입니다. 드롭다운 목록에 메일 그룹 정보가 포함된 행을 사용하려 하므로 RowKey = "mailinglist"인 행만 선택합니다.

**인덱스** 페이지에 대한 데이터를 검색하는 메서드의 경우 구독자 정보가 있는 행을 사용하려 하므로 RowKey = "MailingList"가 없는 행을 모두 선택합니다.

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

쿼리에서 `<Subscriber>`를 지정하여 데이터를 `Subscriber` 개체로 읽어오지만 `mailinglist` 테이블에서 데이터를 읽도록 지정합니다.

구독자 수가 너무 많아져서 단일 쿼리로 이 방법을 처리할 수 없는 경우도 있습니다. 앞에서 설명했듯이 프로덕션 응용 프로그램에서는 연속 토큰을 사용하여 페이징을 구현합니다.

`HttpGet Create` 메서드에서 드롭다운 목록에 대한 데이터를 설정하고 `HttpPost` 메서드에서 새 엔터티를 저장하기 전에 기본값을 설정합니다.

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

`HttpPost Edit` 페이지는 `Subscriber` 페이지에서 목록 이름이나 전자 메일 주소를 변경할 수 있고 둘 다 키 필드이기 때문에 `MailingList` 컨트롤러에서 확인한 페이지보다 더 복잡합니다. 사용자가 이러한 필드 중 하나를 변경하는 경우 기존 레코드를 업데이트하는 대신 기존 레코드를 삭제하고 새 레코드를 추가해야 합니다. 다음 코드는 서로 다른 키 변경 절차와 키가 아닌 변경 절차를 처리하는 edit 메서드의 일부를 보여 줍니다.

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

MVC 모델 바인더가 `Edit` 메서드에 전달하는 매개 변수에는 원래 목록 이름 및 전자 메일 주소 값(`partitionKey` 및 `rowKey` 매개 변수)과 사용자가 입력한 값(`listName` 및 `emailAddress` 매개 변수)이 포함됩니다.

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

`UpdateModel` 메서드에 전달된 매개 변수는 `PartitionKey` 및 `RowKey` 속성을 모델 바인딩에서 제외합니다.

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

`ListName` 및 `EmailAddress` 속성이 `PartitionKey` 및 `RowKey`를 지원 속성으로 사용하며 사용자가 이러한 값 중 하나를 변경했을 수 있기 때문입니다. 모델 바인더가 `ListName` 속성을 설정하여 모델을 업데이트하는 경우 `PartitionKey` 속성이 자동으로 업데이트됩니다. 모델 바인더가 `PartitionKey` 속성을 업데이트한 후 `ListName` 속성을 해당 속성의 원래 값으로 업데이트하는 경우 `ListName` 속성에 의해 설정된 새 값을 덮어씁니다. `EmailAddress` 속성은 동일한 방법으로 `RowKey` 속성을 자동으로 업데이트합니다.

`editedSubscriber` 모델 개체를 업데이트한 후 코드에서 파티션 키 또는 행 키가 변경되었는지 여부를 확인합니다. 키 값이 변경된 경우 기존 구독자 행을 삭제하고 새 행을 삽입해야 합니다. 행 키만 변경된 경우 원자성 일괄 처리 트랜잭션에서 삭제 및 삽입을 수행할 수 있습니다.

코드에서 `Delete` 작업에 전달할 새 엔터티를 만듭니다.

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

일괄 처리로 작업에 전달하는 엔터티는 개별 엔터티여야 합니다. 예를 들어 `Subscriber` 엔터티를 만들고 `Delete` 작업에 전달한 후 동일한 `Subscriber` 엔터티에서 값을 변경하고 `Insert` 작업에 전달할 수는 없습니다. 이 경우 속성 변경 후의 엔터티 상태는 Delete 및 Insert 작업 둘 다에 적용됩니다.

일괄 처리의 작업은 모두 동일한 파티션에 있어야 합니다. 목록 이름을 변경하면 파티션 키가 변경되므로 트랜잭션에서 수행할 수 없습니다.

### Subscriber MVC 뷰 추가

1.  **솔루션 탐색기**에서 MVC 프로젝트의 *Views* 폴더 아래에 새 폴더를 만들고 이름을 *Subscriber*로 지정합니다.

2.  새 *Views\\Subscriber* 폴더를 마우스 오른쪽 단추로 클릭하고 **기존 항목 추가**를 선택합니다.

3.  샘플 응용 프로그램을 다운로드한 폴더로 이동하고 *Views\\Subscriber* 폴더에서 .cshtml 파일 5개를 모두 선택한 후 **추가**를 클릭합니다.

*Edit.cshtml* 파일에는 `SubscriberGUID` 값에 대한 숨겨진 필드가 포함되어 있습니다. 이 필드는 표시되지 않으므로 양식 필드에 자동으로 제공되지 않습니다.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### 응용 프로그램 테스트

1.  Ctrl+F5를 눌러 프로젝트를 실행하고 **Subscribers**를 클릭합니다.

    ![빈 Subscriber 인덱스 페이지][]

2.  **만들기** 기능을 사용하여 일부 메일 그룹을 추가하고 **편집** 및 **삭제** 기능을 시도하여 작동하는지 확인합니다.

    ![행이 있는 Subscribers 인덱스 페이지][]

## <a name="message"></a>메시지 컨트롤러와 뷰 만들기 및 테스트

**Message** 웹 UI는 관리자가 메일 그룹에 전송되도록 예약된 메시지 정보를 만들고, 편집 및 표시하는 데 사용합니다.

### Models 폴더에 Message 엔터티 클래스 추가

1.  MVC 프로젝트의 *Models* 폴더에서 다운로드한 프로젝트의 *Message.cs* 파일을 추가합니다.

`Message` 엔터티 클래스는 메일 그룹에 전송되도록 예약된 메시지 정보가 포함된 `Message` 테이블의 행에 사용됩니다. 이러한 행에는 제목 줄, 메시지를 보낼 메일 그룹, 예약된 전송 날짜 등의 정보가 포함됩니다.

        public class Message : TableEntity
        {
            private DateTime? _scheduledDate;
            private long _messageRef;

            public Message()
            {
                this.MessageRef = DateTime.Now.Ticks;
                this.Status = "Pending";
            }

            [Required]
            [Display(Name = "Scheduled Date")]
            // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
            [DataType(DataType.Date)]
            public DateTime? ScheduledDate 
            {
                get
                {
                    return _scheduledDate;
                }
                set
                {
                    _scheduledDate = value;
                    this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
                }
            }
            
            public long MessageRef 
            {
                get
                {
                    return _messageRef;
                }
                set
                {
                    _messageRef = value;
                    this.RowKey = "message" + value.ToString();
                }
            }

            [Required]
            [Display(Name = "List Name")]
            public string ListName { get; set; }

            [Required]
            [Display(Name = "Subject Line")]
            public string SubjectLine { get; set; }

            // Pending, Queuing, Processing, Complete
            public string Status { get; set; }
        }
        

`Message` 클래스는 `MessageRef` 속성을 메시지에 고유한 값으로 설정하는 기본 생성자를 정의합니다. 이 값은 행 키의 일부이므로 `MessageRef` 속성의 setter에서 `RowKey` 속성도 자동으로 설정합니다. `MessageRef` 속성 setter는 "message" 리터럴과 `MessageRef` 값을 연결하고 `RowKey` 속성에 저장합니다.

`MessageRef` 값은 `DateTime.Now`에서 `Ticks` 값을 가져와서 만듭니다. 이 경우 웹 UI에 메시지를 표시할 때 기본적으로 주어진 예약 날짜에 대해 생성된 순서대로 표시됩니다(`ScheduledDate`는 파티션 키임). GUID를 사용하여 메시지 행을 고유하게 만들 수 있지만 기본 검색 순서는 임의로 지정됩니다.

또한 기본 생성자는 새 `message` 행에 대해 기본 상태 Pending을 설정합니다.

`Message` 테이블 구조에 대한 자세한 내용은 [시리즈의 첫 번째 자습서][]를 참조하세요.

### Message MVC 컨트롤러 추가

1.  MVC 프로젝트의 *Controllers* 폴더에서 다운로드한 프로젝트의 *MessageController.cs* 파일을 추가합니다.

이 컨트롤러의 코드는 대부분 `Subscriber` 컨트롤러에서 확인한 코드와 유사합니다. 여기서 새로 추가된 코드는 Blob 작업을 위한 코드입니다. 각 메시지에 대해 전자 메일의 HTML 및 일반 텍스트 콘텐츠가 .htm 및 .txt 파일 형식으로 업로드되고 Blob에 저장됩니다.

Blob은 Blob 컨테이너에 저장됩니다. Azure 전자 메일 서비스 응용 프로그램은 "azuremailblobcontainer"라는 단일 Blob 컨테이너에 모든 Blob을 저장하며, 컨트롤러 생성자의 코드에서 이 Blob 컨테이너에 대한 참조를 가져옵니다.

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

사용자가 업로드를 선택하는 각 파일에 대해 MVC 뷰는 파일 정보가 포함된 `HttpPostedFile` 개체를 제공합니다. 사용자가 새 메시지를 만드는 경우 `HttpPostedFile` 개체가 파일을 Blob에 저장하는 데 사용됩니다. 사용자가 메시지를 편집하는 경우 대체 파일을 업로드하거나 Blob을 변경하지 않고 그대로 유지할 수 있습니다.

컨트롤러에는 `HttpPost Create` 및 `HttpPost Edit` 메서드가 Blob을 저장하기 위해 호출하는 메서드가 포함되어 있습니다.

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

`HttpPost Create` 메서드는 두 개의 Blob을 저장한 후 `Message` 테이블 행을 추가합니다. `MessageRef` 값과 파일 이름 확장명 ".htm" 또는 ".txt"를 연결하여 Blob 이름이 지정됩니다.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }
        
            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }
        
            if (ModelState.IsValid)
            {
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

`HttpGet Edit` 메서드는 작업자 역할 B가 처리를 시작한 후에는 사용자가 메시지를 변경할 수 없도록 검색된 메시지가 `Pending` 상태인지 확인합니다. 유사한 코드가 `HttpPost Edit` 메서드와 `Delete`, `DeleteConfirmed` 메서드에도 있습니다.

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

`HttpPost Edit` 메서드의 코드는 사용자가 새 파일을 업로드하도록 선택한 경우에만 새 Blob을 저장합니다.

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

예약 날짜가 변경된 경우 파티션 키가 변경되었으며 행을 삭제하고 삽입해야 합니다. 두 개 이상의 파티션에 영향을 주기 때문에 단일 트랜잭션에서 수행할 수 없습니다.

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

`HttpPost Delete` 메서드는 테이블의 행을 삭제할 때 Blob을 삭제합니다.

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### Message MVC 뷰 추가

1.  MVC 프로젝트에서 *Views* 폴더 아래에 새 폴더를 만들고 이름을 `Message`로 지정합니다.

2.  새 *Views\\Message* 폴더에서 다운로드한 프로젝트의 5개 *.cshtml* 파일을 모두 추가합니다.

`HttpPost Edit` 메서드에는 파티션 키와 행 키가 필요하므로 *Edit.cshtml* 파일의 코드에서 숨겨진 필드에 이러한 키를 제공합니다.

        @using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
        {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
            <fieldset>
                <legend>Message</legend>
                @Html.HiddenFor(model => model.MessageRef)
                @Html.HiddenFor(model => model.PartitionKey)
                @Html.HiddenFor(model => model.RowKey)
                

숨겨진 필드가 `Subscriber` 컨트롤러에는 필요하지 않습니다. (a)`Subscriber` 모델의 `ListName`, `EmailAddress` 속성이 `PartitionKey`, `RowKey` 속성을 업데이트하고, (b) `ListName`, `EmailAddress` 성이 편집 뷰에 `EditorFor` 도우미와 함께 포함되었기 때문입니다. `Subscriber` 모델에 대한 MVC 모델 바인더가 `ListName`속성을 업데이트하는 경우 `PartitionKey` 속성이 자동으로 업데이트되고, MVC 모델 바인더가 `Subscriber` 모델에서 `EmailAddress` 속성을 업데이트하는 경우 `RowKey` 속성이 자동으로 업데이트됩니다. `Message` 모델에서 파티션 키와 행 키에 매핑되는 필드는 편집 가능한 필드가 아니므로 이런 방식으로 설정되지 않습니다.

`MessageRef` 속성에 대해 숨겨진 필드도 포함됩니다. 파티션 키와 동일한 값이지만 `HttpPost Edit` 메서드에서 코드의 명확도를 향상시키기 위해 포함됩니다. `MessageRef` 숨겨진 필드를 포함하면 `HttpPost Edit` 메서드의 코드에서 Blob에 대한 파일 이름을 생성할 때 해당 이름으로 `MessageRef` 값을 참조할 수 있습니다.

*Index.cshtml* 파일의 메시지 **인덱스** 보기는 다른 **인덱스** 보기와 다릅니다. 구체적으로 말하면, **편집** 및 **삭제** 링크가 `Pending` 상태인 메시지에 대해서만 표시된다는 점입니다.

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

이 경우 작업자 역할 A가 처리를 시작한 후에는 사용자가 메시지를 변경할 수 없습니다.

### 응용 프로그램 테스트

1.  Ctrl+F5를 눌러 프로젝트를 실행하고 **Messages**를 클릭합니다.

    ![빈 Message 인덱스 페이지][]

2.  **만들기** 기능을 사용하여 일부 메일 그룹을 추가하고 **편집** 및 **삭제** 기능을 시도하여 작동하는지 확인합니다.

    ![행이 있는 Subscribers 인덱스 페이지][2]

## <a name="unsubscribe"></a>구독 취소 컨트롤러와 뷰 만들기 및 테스트

다음에는 구독 취소 프로세스에 대한 UI를 구현하겠습니다.

이 자습서에서는 구독 프로세스가 아니라 구독 취소 프로세스에 대한 컨트롤러만 빌드하는 지침을 제공됩니다. [첫 번째 자습서][시리즈의 첫 번째 자습서]에서 설명한 대로 구독 프로세스에 대한 UI 및 서비스 메서드는 다운로드에 포함되었지만 샘플 응용 프로그램이 서비스 메서드에 대한 보안을 구현하지 않기 때문에 자습서 지침에서 제외되었습니다. 테스트 목적으로 **구독자** 관리자 페이지를 사용하여 전자 메일 주소를 메일 그룹에 가입시킬 수 있습니다.

### Models 폴더에 Unsubscribe 뷰 모델 추가

1.  MVC 프로젝트의 `Models` 폴더에서 다운로드한 프로젝트의 `UnsubscribeVM.cs` 파일을 추가합니다.

`UnsubscribeVM` 뷰 모델은 `Unsubscribe` 컨트롤러와 해당 뷰 간에 데이터를 전달하는 데 사용됩니다.

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

구독 취소 링크에는 `SubscriberGUID` 코드가 포함됩니다. 해당 값은 `MailingList` 테이블에서 전자 메일 주소, 목록 이름 및 목록 설명을 가져오는 데 사용됩니다. 뷰에는 구독 취소되는 메일 그룹의 전자 메일 주소 및 설명이 표시되며, 사용자가 구독 취소 프로세스를 완료하기 위해 클릭해야 하는 **확인** 단추도 표시됩니다.

### Unsubscribe 컨트롤러 추가

1.  MVC 프로젝트의 `Controllers` 폴더에서 다운로드한 프로젝트의 *UnsubscribeController.cs* 파일을 추가합니다.

이 컨트롤러에는 초기 구독 취소 페이지를 표시하는 `HttpGet Index` 메서드와 **확인** 또는 **취소** 단추를 처리하는 `HttpPost Index` 메서드가 있습니다.

`HttpGet Index` 메서드는 쿼리 문자열에 GUID 및 목록 이름을 사용하여 구독자에 대한 `MailingList` 테이블 행을 가져옵니다. 그런 다음 뷰에 필요한 모든 정보를 뷰 모델에 배치하고 **구독 취소** 페이지를 표시합니다. 뷰에 초기 버전의 **구독 취소** 페이지를 표시하도록 지정하기 위해 `Confirmed` 속성을 Null로 설정합니다.

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

참고: SubscriberGUID는 파티션 키나 행 키에 없으므로 파티션 크기(메일 그룹의 전자 메일 주소 수)가 증가하면 이 쿼리의 성능이 저하됩니다. 이 쿼리를 더 확장 가능하게 만드는 대안에 대한 자세한 내용은 [시리즈의 첫 번째 자습서][]를 참조하세요.

`HttpPost Index` 메서드가 다시 GUID 및 목록 이름을 사용하여 구독자 정보를 가져오고 뷰 모델 속성을 채웁니다. 그런 다음 **확인** 단추가 클릭된 경우 `MailingList` 테이블에서 구독자 행을 삭제합니다. **확인** 단추를 누른 경우 `Confirm` 속성도 `true`로 설정합니다. 그렇지 않으면 `Confirm` 속성을 `false`로 설정합니다. `Confirm` 속성 값은 뷰에 확인 또는 취소된 버전의 **구독 취소** 페이지를 표시하도록 지정합니다.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;
        
            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }
        
            return View(unsubscribeVM);
        }

### MVC 뷰 만들기

1.  MVC 프로젝트의 *Views* 폴더에서 새 폴더를 만들고 이름을 *Unsubscribe*로 지정합니다.

2.  새 *Views\\Unsubscribe* 폴더에서 다운로드한 프로젝트의 *Index.cshtml* 파일을 추가합니다.

*Index.cshtml* 파일에서 `Layout = null` 줄은 이 페이지를 표시하는 데 \_Layout.cshtml 파일을 사용하지 않도록 지정합니다. **구독 취소** 페이지에는 관리자 페이지에 사용되는 머리글 및 바닥글이 없는 간단한 UI가 표시됩니다.

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

페이지 본문에서 `Confirmed` 속성은 페이지에 표시되는 내용을 결정합니다(속성이 null인 경우 **확인** 및 **취소** 단추, 속성이 true인 경우 구독 취소-확인 메시지, 속성이 false인 경우 구독 취소-취소 메시지).

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>Unsubscribe from Mailing List</legend>
                @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
                @if (Model.Confirmed == null) {
                    <p>
                        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
                   </p>
                    <br />
                    <p>
                        <input type="submit" value="Confirm" name="action"/> 
                        &nbsp; &nbsp;
                        <input type="submit" value="Cancel" name="action"/>
                    </p>
                }
                @if (Model.Confirmed == false) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
                @if (Model.Confirmed == true) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
            </fieldset>
        }

### 응용 프로그램 테스트

1.  Ctrl+F5를 눌러 프로젝트를 실행하고 **Subscribers**를 클릭합니다.

2.  **만들기**를 클릭하고, 앞에서 테스트할 때 만든 모든 메일 그룹에 대해 새 구독자를 만듭니다.

    **Subscribers** **인덱스** 페이지에서 브라우저 창을 열어 둡니다.

3.  **서버 탐색기**를 열고 **Azure / Storage / (Development)** 노드를 선택합니다.

4.  **테이블**을 확장하고 **MailingList** 테이블을 마우스 오른쪽 단추로 클릭한 다음 **테이블 보기**를 클릭합니다.

5.  추가한 구독자 행을 두 번 클릭합니다.

6.  **엔터티 편집** 대화 상자에서 `SubscriberGUID` 값을 선택하고 복사합니다.

    ![Azure 저장소 탐색기][]

7.  브라우저 창으로 다시 전환합니다. 브라우저 주소 표시줄에서 URL의 "Subscriber"를 "unsubscribe?ID=[guidvalue]&listName=[listname]"으로 변경합니다. 여기서 [guidvalue]는 Azure 저장소 탐색기에서 복사한 GUID이고, [listname]은 메일 그룹의 이름입니다. 예를 들면 다음과 같습니다.

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    확인을 요청하는 **구독 취소** 페이지의 버전이 표시됩니다.

    ![구독 취소 페이지][]

8.  **확인**을 클릭하면 전자 메일 주소가 구독 취소되었다는 확인이 표시됩니다.

    ![구독 취소 확인 페이지][]

9.  **Subscribers** **인덱스** 페이지로 돌아가서 구독자 행이 더 이상 없는지 확인합니다.

## <a name="nextsteps"></a>다음 단계

[시리즈의 첫 번째 자습서][]에서 설명한 대로 이 자습서에서는 구독 프로세스의 구성 요소를 보여 주지 않습니다. ASP.NET Web API 서비스 메서드가 공유 비밀 보안을 구현하지 않기 때문입니다. 그러나 두 번째 자습서에서 설정한 IP 제한도 서비스 메서드를 보호하며, 다운로드한 프로젝트에서 다음 파일을 복사하여 구독 기능을 추가할 수 있습니다.

ASP.NET Web API 서비스 메서드의 경우

-   Controllers\\SubscribeAPI.cs

서비스 메서드에 의해 생성된 전자 메일에서 **확인** 링크를 클릭할 때 구독자에게 표시되는 웹 페이지의 경우

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

[다음 자습서][]에서는 전자 메일을 예약하는 작업자 역할인 작업자 역할 A를 구성 및 프로그래밍합니다.

Azure 저장소 테이블, 큐 및 Blob 작업 관련 추가 리소스에 대한 링크는 [이 시리즈의 마지막 자습서][]를 참조하세요.


<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">자습서 4</a></div>

  [시리즈의 첫 번째 자습서]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Visual Studio 솔루션 만들기]: #cloudproject
  [저장소 클라이언트 라이브러리 NuGet 패키지 업데이트]: #updatescl
  [저장소 에뮬레이터를 사용하도록 프로젝트 구성]: #configurestorage
  [추적 구성 및 다시 시작 처리]: #tracing
  [Application\_Start 메서드에 테이블, 큐 및 Blob 컨테이너를 만드는 코드 추가]: #createifnotexists
  [메일 그룹 만들기 및 테스트]: #mailinglist
  [구독자 컨트롤러와 뷰 만들기 및 테스트]: #subscriber
  [메시지 컨트롤러와 뷰 만들기 및 테스트]: #message
  [구독 취소 컨트롤러와 뷰 만들기 및 테스트]: #unsubscribe
  [다음 단계]: #nextsteps
  [새 프로젝트 메뉴]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [새 프로젝트 대화 상자]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [새 Azure 클라우드 프로젝트 대화 상자]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [새 Azure 클라우드 프로젝트 대화 상자 - 웹 역할 이름 바꾸기]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [새 Azure 클라우드 프로젝트 대화 상자 - 작업자 역할 추가]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [인증 없음]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [완료된 솔루션]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [홈페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [시스템 트레이의 계산 에뮬레이터]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [메뉴의 솔루션에 대한 NuGet 패키지 관리]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [NuGet 패키지 관리 대화 상자의 Azure 저장소 패키지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [프로젝트 선택 대화 상자에서 두 프로젝트 모두 선택]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [웹 역할 속성]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [마우스 오른쪽 단추 클릭 속성]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [잘못된 서비스 구성 및 정의 파일 오류]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [두 번째 자습서l]: /en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [OS 업그레이드로 인해 역할 인스턴스 다시 시작]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [주문형 전송]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [Azure 저장소 클라이언트 라이브러리 2.0 테이블 심층 검토]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [테이블 서비스 데이터 모델 이해]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx
  [PartitionKey 또는 RowKey의 % 문자]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [.NET 4.5의 비동기 지원을 사용하여 호출 차단 방지]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [Azure 테이블을 활용하는 방법]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Azure 테이블: 연속 토큰 필요]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [Azure 저장소 로깅: 로그를 사용하여 저장소 요청 추적]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [빈 MailingList 인덱스 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [행이 있는 MailingList 인덱스 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [빈 Subscriber 인덱스 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [행이 있는 Subscribers 인덱스 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [빈 Message 인덱스 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Azure 저장소 탐색기]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [구독 취소 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [구독 취소 확인 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [다음 자습서]: /en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [이 시리즈의 마지막 자습서]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
