<properties 
	pageTitle="Azure 앱 서비스 API 앱 트리거" 
	description="이 문서에서는 API 앱에서 트리거를 구현하는 방법을 보여 줍니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="guayan;tarcher"/>

# Azure 앱 서비스 API 앱 트리거

## 개요

이 문서에서는 API 앱 트리거를 구현하고 논리 앱에서 이를 사용하는 방법을 설명합니다.

[Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에서 [API 앱](app-service-api-apps-why-best-platform.md)을 처음 사용하는 경우, [API 앱 만들기](app-service-dotnet-create-api-app.md)에서 여러 요소 시리즈를 읽는 것이 좋습니다.

또한 이 항목의 모든 코드 조각은 [FileWatcher API 앱 코드 샘플](http://go.microsoft.com/fwlink/?LinkId=534802)에서 복사할 수 있습니다.

이 문서의 코드를 빌드 및 실행하려면 nuget 패키지 [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/)를 다운로드해야 합니다.

## API 앱 트리거 정의

일반적으로 API 앱은 API 앱 클라이언트가 이벤트에 대한 응답으로 적절한 조치를 취할 수 있도록 이벤트를 발생시킵니다. 이 시나리오를 지원하는 REST API 기반 메커니즘을 API 앱 트리거라고 합니다.

예를 들어 클라이언트 코드에서 [Twitter Connector API 앱](../app-service-logic/app-service-logic-connector-twitter.md)을 사용하는 경우 코드는 특정 단어가 포함된 새 트윗을 기반으로 작업을 수행해야 합니다. 이 경우 밀어넣기 또는 끌어오기 트리거를 설정하면 용이할 수 있습니다.

## 폴링 트리거와 밀어넣기 트리거

현재 다음 두 가지 유형의 트리거가 지원됩니다.

- 폴링 트리거 - 클라이언트가 발생한 이벤트의 알림에 대해 API 앱을 폴링합니다. 
- 밀어넣기 트리거 - 이벤트가 발생한 경우 API 앱에서 클라이언트로 알림을 보냅니다. 

### 폴링 트리거

폴링 트리거는 일반 REST API로 구현되며, 해당 클라이언트(예: 논리 앱)는 알림을 받기 위해 이를 폴링해야 합니다. 클라이언트는 상태를 유지 관리할 수 있지만 폴링 트리거 자체는 상태 비저장입니다.

요청 및 응답 패킷에 대한 다음 정보는 폴링 트리거 계약의 몇 가지 주요 측면을 보여 줍니다.

- 요청
    - HTTP 메서드: GET
    - 매개 변수
        - triggerState - 이 선택적 매개 변수는 폴링 트리거가 지정된 상태를 기반으로 알림 반환 여부를 적절히 결정할 수 있도록 클라이언트가 해당 상태를 지정할 수 있도록 합니다.
        - API 관련 매개 변수
- 응답
    - 상태 코드 **200** - 요청이 유효하며 트리거로부터의 알림이 있습니다. 알림 내용이 응답 본문이 됩니다. 응답의 "Retry-After" 헤더는 후속 요청 호출을 통해 추가 알림 데이터를 검색해야 함을 나타냅니다.
    - 상태 코드 **202** - 요청이 유효하지만 트리거로부터의 새 알림이 없습니다.
    - 상태 코드 **4xx** - 요청이 유효하지 않습니다. 클라이언트가 요청을 다시 시도할 수 없습니다.
    - 상태 코드 **5xx** - 요청 시 내부 서버 오류 및/또는 일시적인 문제가 발생했습니다. 클라이언트가 요청을 다시 시도해야 합니다.

다음 코드 조각은 폴링 트리거를 구현하는 방법의 예입니다.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

이 폴링 트리거를 테스트하려면 다음 단계를 따르세요.

1. **공용(익명)** 인증 설정으로 API 앱을 배포합니다.
2. **touch** 작업을 호출하여 파일을 터치합니다. 다음 그림에서는 Postman을 통한 샘플 요청을 보여 줍니다. ![Postman을 통해 Touch 작업 호출](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. 2단계 이전의 타임스탬프로 설정된 **triggerState** 매개 변수 집합을 사용하여 폴링 트리거를 호출합니다. 다음 그림에서는 Postman을 통한 샘플 요청을 보여 줍니다. ![Postman을 통해 폴링 트리거 호출](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### 밀어넣기 트리거

밀어넣기 트리거는 특정 이벤트가 발생하면 알림을 받도록 등록된 클라이언트로 알림을 푸시하는 일반 REST API로 구현됩니다.

요청 및 응답 패킷에 대한 다음 정보는 밀어넣기 트리거 계약의 몇 가지 주요 측면을 보여 줍니다.

- 요청
    - HTTP 메서드: PUT
    - 매개 변수
        - triggerId: 필수 - 밀어넣기 트리거의 등록을 나타내는 불투명 문자열(예: GUID)입니다.
        - callbackUrl: 필수 - 이벤트가 발생하면 호출할 콜백의 URL입니다. 호출은 간단한 POST HTTP 호출입니다.
        - API 관련 매개 변수
- 응답
    - 상태 코드 **200** - 클라이언트 등록 요청에 성공했습니다.
    - 상태 코드 **4xx** - 요청이 유효하지 않습니다. 클라이언트가 요청을 다시 시도할 수 없습니다.
    - 상태 코드 **5xx** - 요청 시 내부 서버 오류 및/또는 일시적인 문제가 발생했습니다. 클라이언트가 요청을 다시 시도해야 합니다.
- 콜백
    - HTTP 메서드: POST
    - 요청 본문: 알림 내용

다음 코드 조각은 밀어넣기 트리거를 구현하는 방법의 예입니다.

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

이 폴링 트리거를 테스트하려면 다음 단계를 따르세요.

1. **공용(익명)** 인증 설정으로 API 앱을 배포합니다.
2. [http://requestb.in/](http://requestb.in/)을 찾아서 콜백 URL로 사용할 RequestBin을 만듭니다.
3. **triggerId**를 GUID로 사용하고 **callbackUrl**을 RequestBin URL로 사용하여 밀어넣기 트리거를 호출합니다. ![Postman을 통해 밀어넣기 트리거 호출](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. **touch** 작업을 호출하여 파일을 터치합니다. 다음 그림에서는 Postman을 통한 샘플 요청을 보여 줍니다. ![Postman을 통해 Touch 작업 호출](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. RequestBin을 검사하여 밀어넣기 트리거 콜백이 속성 출력으로 호출되었는지 확인합니다. ![Postman을 통해 폴링 트리거 호출](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### API 정의에서 트리거 설명

트리거를 구현하고 API 앱을 Azure에 배포한 후에는 Azure Preview 포털에서 **API 정의** 블레이드로 이동하여 API 앱의 Swagger 2.0 API 정의로 구동되는 UI에서 트리거가 자동으로 인식되는지 확인합니다.

![API 정의 블레이드](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

**Swagger 다운로드** 단추를 클릭하고 JSON 파일을 열면 다음과 유사한 결과가 표시됩니다.

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

확장 속성 **x-ms-schedular-trigger**는 API 정의에서 트리거를 설명하는 방식이며, 게이트웨이 통해 API 정의를 요청할 때 다음 조건 중 하나에 해당하는 경우 API 앱 게이트웨이에 의해 자동으로 추가됩니다. 이 속성을 수동으로 추가할 수도 있습니다.

- 폴링 트리거
    - HTTP 메서드가 **GET**인 경우
    - **operationId** 속성에 문자열 **trigger**가 포함된 경우
    - **parameters** 속성에 **name** 속성이 **triggerState**로 설정된 매개 변수가 포함된 경우
- 밀어넣기 트리거
    - HTTP 메서드가 **PUT**인 경우
    - **operationId** 속성에 문자열 **trigger**가 포함된 경우
    - **parameters** 속성에 **name** 속성이 **triggerId**로 설정된 매개 변수가 포함된 경우

## 논리 앱에서 API 앱 트리거 사용

### 논리 앱 디자이너에서 API 앱 트리거 나열 및 구성

API 앱과 동일한 리소스 그룹에서 논리 앱을 만든 경우 클릭 작업만으로 디자이너 캔버스에 간단히 추가할 수 있습니다. 다음 그림에서는 이 과정을 보여 줍니다.

![논리 앱 디자이너의 트리거](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![논리 앱 디자이너에서 폴링 트리거 구성](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![논리 앱 디자이너에서 밀어넣기 트리거 구성](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## 논리 앱에 맞게 API 앱 트리거 최적화

API 앱에 트리거를 추가한 후에는 몇 가지 작업을 통해 논리 앱에서 API 앱을 사용할 때 환경을 개선할 수 있습니다.

예를 들어 논리 앱에서 폴링 트리거의 **triggerState** 매개 변수를 다음 식으로 설정해야 합니다. 이 식은 논리 앱의 마지막 트리거 호출을 평가하여 해당 값을 반환합니다.

	@coalesce(triggers()?.outputs?.body?['triggerState'], '')

참고: 위 식에서 사용된 함수에 대한 설명은 [논리 앱 워크플로 정의 언어](https://msdn.microsoft.com/library/azure/dn948512.aspx)의 설명서를 참조하세요.

논리 앱 사용자는 트리거를 사용하는 동안 위 식을 **triggerState** 매개 변수로 제공해야 합니다. 확장 속성 **x-ms-scheduler-recommendation**을 통해 논리 앱 디자이너가 이 값을 미리 설정할 수 있습니다. 매개 변수 자체가 디자이너에 표시되지 않도록 **x-ms-visibility** 확장 속성을 *internal* 값으로 설정할 수 있습니다. 다음 코드에서는 이러한 설정을 보여 줍니다.

    "/api/Messages/poll": {
      "get": {
	    "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

밀어넣기 트리거의 경우 **triggerId** 매개 변수는 논리 앱을 고유하게 식별해야 합니다. 다음 식을 사용하여 이 속성을 워크플로 이름으로 설정하는 것이 가장 좋습니다.

    @workflow().name

해당 API 정의에서 **x-ms-scheduler-recommendation** 및 **x-ms-visibility** 확장 속성을 사용하면 API 앱이 논리 앱 디자이너로 전달하여 사용자를 위해 이 식을 자동으로 설정할 수 있습니다.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### API 정의에서 확장 속성 추가

**x-ms-scheduler-recommendation** 및 **x-ms-visibility** 확장 속성과 같은 추가 메타데이터 정보를 정적 또는 동적으로 API 정의에 추가할 수 있습니다.

정적 메타데이터의 경우 프로젝트에서 직접 */metadata/apiDefinition.swagger.json* 파일을 편집하고 속성을 수동으로 추가할 수 있습니다.

동적 메타데이터를 사용하는 API 앱의 경우 SwaggerConfig.cs 파일을 편집하여 이러한 확장을 추가할 수 있는 작업 필터를 추가할 수 있습니다.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


다음은 동적 메타데이터 시나리오를 용이하게 하기 위해 이 클래스를 구현할 수 있는 방법의 예입니다.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 

<!---HONumber=July15_HO4-->