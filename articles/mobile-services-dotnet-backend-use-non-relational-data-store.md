<properties 
	pageTitle="비관계형 데이터 저장소를 사용하여 서비스 구축 -Azure 모바일 서비스" 
	description=".NET 기반 모바일 서비스에서 MongoDB 나 Azure 테이블 저장소와 같은 비관계형 데이터 저장소를 사용하는 방법에 대해 알아봅니다." 
	services="" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="mahender"/>

# .NET 백 엔드에서 데이터 저장소로 MongoDB를 사용하여 서비스 구축

이 항목에서는 모바일 서비스에 대한 비관계형 데이터 저장소를 사용하는 방법을 보여 줍니다. 이 자습서에서는 데이터 저장소로 SQL 대신 MongoDB를 사용하도록 모바일 서비스 퀵 스타트 프로젝트를 수정합니다.

이 자습서에서는 비관계형 저장소를 설정하는 단계에 대해 설명합니다.

1. [비관계형 저장소 만들기]
2. [데이터 및 컨트롤러 수정]
3. [응용 프로그램 테스트]

이 자습서를 수행하려면 먼저 [모바일 서비스 시작] 또는 [데이터 시작] 자습서를 완료해야 합니다.

## <a name="create-store"></a>비관계형 저장소 만들기

1. [Azure 관리 포털]에서 **새로 만들기**를 클릭한 후 **저장소**를 선택합니다.

2. **MongoLab** 추가 기능을 선택하고 마법사에 따라 계정을 등록합니다. MongoLab에 대한 자세한 내용은 [MongoLab 추가 기능 페이지]를 참조하세요.

    ![][0]

2. 계정이 설정된 다음에는 **연결 정보**를 선택하고 연결 문자열을 복사합니다.

3. 포털의 모바일 서비스 섹션으로 이동하고 **구성** 탭을 선택합니다.

4. **앱 설정** 아래에서 "MongoConnectionString" 키가 포함된 연결 문자열을 입력하고 **저장**을 클릭합니다.

    ![][1]

2.  `TodoItemController`에 다음을 추가합니다.

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    이 코드는 응용 프로그램 설정을 로드하고 모바일 서비스가 이를  `TableController`에서 사용할 수 있는 연결로 취급하도록 지정합니다. 나중에  `TodoItemController`가 호출될 때 이 메서드를 호출합니다.



## <a name="modify-service"></a>데이터 및 컨트롤러 수정

1. **WindowsAzure.MobileServices.Backend.Mongo** NuGet 패키지를 설치합니다.

2.  `EntityData` 대신  `DocumentData`에서 파생되도록  `TodoItem`을 수정합니다.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3.  `TodoItemController`에서  `Initialize` 메서드를 다음으로 바꿉니다.

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. 위의  `Initialize` 메서드에 대한 코드에서 **YOUR-DATABASE-NAME**를 MongoLab 추가 기능을 프로비전할 때 선택한 이름으로 바꿉니다.


## <a name="test-application"></a>응용 프로그램 테스트

1. 모바일 서비스 백 엔드 프로젝트를 다시 게시합니다.

2. 클라이언트 응용 프로그램을 실행합니다. 퀵 스타트 자습서에서 SQL 데이터베이스에 이전에 저장된 모든 항목이 표시되지는 않습니다.

3. 새 항목을 만듭니다. 앱이 이전과 같이 작동하지만 이제는 데이터가 비관계형 저장소로 이동됩니다.


<!-- Anchors. -->
[비관계형 저장소 만들기]: #create-store
[데이터 및 컨트롤러 수정]: #modify-service
[응용 프로그램 테스트]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Azure 관리 포털]: https://manage.windowsazure.com/
[테이블 서비스 정의]: /ko-kr/documentation/articles/storage-dotnet-how-to-use-tables/#what-is
[MongoLab 추가 기능 페이지]: /ko-kr/gallery/store/mongolab/mongolab


<!--HONumber=42-->
