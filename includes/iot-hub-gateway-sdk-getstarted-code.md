## 일반적인 출력

다음은 Hello World 샘플을 통해 파일을 기록하기 위해 작성한 출력의 예입니다. 읽기 쉽도록 새 줄과 탭 문자가 추가되었습니다.

```
[{
	"time": "Mon Apr 11 13:48:07 2016",
	"content": "Log started"
}, {
	"time": "Mon Apr 11 13:48:48 2016",
	"properties": {
		"helloWorld": "from Azure IoT Gateway SDK simple sample!"
	},
	"content": "aGVsbG8gd29ybGQ="
}, {
	"time": "Mon Apr 11 13:48:55 2016",
	"properties": {
		"helloWorld": "from Azure IoT Gateway SDK simple sample!"
	},
	"content": "aGVsbG8gd29ybGQ="
}, {
	"time": "Mon Apr 11 13:49:01 2016",
	"properties": {
		"helloWorld": "from Azure IoT Gateway SDK simple sample!"
	},
	"content": "aGVsbG8gd29ybGQ="
}, {
	"time": "Mon Apr 11 13:49:04 2016",
	"content": "Log stopped"
}]
```

## 코드 조각

이 섹션은 Hello World 샘플에서 코드의 주요 부분을 설명합니다.

### 게이트웨이 생성

개발자는 *게이트웨이 프로세스*를 작성해야 합니다. 이 프로그램은 내부 인프라(메시지 버스)를 생성하고, 모듈을 로드하고, 모든 것이 제대로 작동하도록 설정합니다. SDK는 JSON 파일에서 게이트웨이를 부트스트랩할 수 있도록 **Gateway\_Create\_From\_JSON** 함수를 제공합니다. **Gateway\_Create\_From\_JSON** 함수를 사용하려면 로드할 모듈을 지정하는 JSON 파일에 대한 경로를 전달해야 합니다.

Hello World 샘플의 게이트웨이 프로세스에 대한 코드는 [main.c][lnk-main-c] 파일에서 찾을 수 있습니다. 읽기 쉽도록, 아래 코드 조각에 게이트웨이 프로세스 코드의 간략한 버전을 보여줍니다. 이 프로그램은 게이트웨이를 만든 다음 게이트웨이를 허물기 전에 사용자가 **ENTER** 키를 누를 때까지 대기합니다.

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
	return 0;
}
```

JSON 설정 파일은 로드할 모듈 목록을 포함합니다. 각 모듈은 다음을 지정합니다.

- **module\_name**: 모듈의 고유한 이름.
- **module\_path**: 모듈을 포함하는 라이브러리에 대한 경로. Linux는 .so 파일이고, Windows는 .dll 파일입니다.
- **args**: 모듈에 필요한 구성 정보입니다.

다음 샘플은 Linux에서 Hello World 샘플을 구성하는 데 사용되는 JSON 설정 파일을 보여줍니다. 모듈에 인수가 필요한지 여부는 모듈의 디자인에 달려 있습니다. 이 예에서, 로거 모듈은 출력 파일에 대한 경로를 인수로 취하고, Hello World 모듈은 인수를 전혀 취하지 않습니다.

```
{
    "modules" :
    [ 
        {
            "module name" : "logger_hl",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "helloworld",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
			"args" : null
        }
    ]
}
```

### Hello World 모듈 메시지 게시

메시지를 게시하기 위해 "hello world" 모듈에 사용되는 코드는 ['hello\_world.c'][lnk-helloworld-c] 파일에서 찾을 수 있습니다. 아래 코드 조각은 읽기 쉽도록 코멘트가 추가되고 일부 오류 처리 코드가 제거된 수정 버전을 보여줍니다.

```
int helloWorldThread(void *param)
{
    // Create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);
    
    // Add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // Set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // Set the properties for the message
    msgConfig.sourceProperties = propertiesMap;
    
    // Create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // Publish the message to the bus
            (void)MessageBus_Publish(handleData->busHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### Hello World 모듈 메시지 처리

Hello World 모듈은 다른 모듈이 메시지 버스에 게시하는 어떠한 메시지도 처리할 필요가 없습니다. 따라서 Hello World 모듈 내 메시지 콜백 구현은 수행되지 않는 함수가 됩니다.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### 로거 모듈 메시지 게시 및 처리

로거 모듈은 메시지 버스로부터 메시지를 수신하고 이것을 파일에 기록합니다. 메시지 버스에 메시지를 절대 게시하지 않습니다. 따라서 로거 모듈의 코드는 **MessageBus\_Publish** 함수를 절대 호출하지 않습니다.

[logger.c][lnk-logger-c] 파일의 **Logger\_Recieve** 함수는 메시지 버스가 로거 모듈에 메시지를 전달하기 위해 호출하는 콜백입니다. 아래 코드 조각은 읽기 쉽도록 코멘트가 추가되고 일부 오류 처리 코드가 제거된 수정 버전을 보여줍니다.

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{"time":"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "","properties":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ","content":"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, ""}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## 다음 단계

Gateway SDK 사용법에 대해 알아보려면 다음을 참조하세요.

- [IoT Gateway SDK – Linux를 사용하는 시뮬레이션된 장치에서 장치-클라우드 메시지 보내기][lnk-gateway-simulated]
- GitHub의 [Azure IoT Gateway SDK][lnk-gateway-sdk].

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0713_2016-->