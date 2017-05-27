## <a name="typical-output"></a>일반적인 출력

다음은 Hello World 샘플을 통해 파일을 기록하기 위해 작성한 출력의 예입니다. 쉽게 읽을 수 있도록 출력 형식을 지정합니다.

```json
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

## <a name="code-snippets"></a>코드 조각

이 섹션은 hello\_world 샘플에서 코드의 일부 주요 섹션을 설명합니다.

### <a name="iot-edge-gateway-creation"></a>IoT Edge 게이트웨이 만들기

개발자는 *게이트웨이 프로세스*를 작성해야 합니다. 이 프로그램은 내부 인프라(broker)를 만들고, IoT Edge 모듈을 로드하며, 모든 기능이 제대로 작동하도록 설정합니다. IoT Edge는 JSON 파일에서 게이트웨이를 부트스트랩할 수 있도록 **Gateway\_Create\_From\_JSON** 함수를 제공합니다. **Gateway\_Create\_From\_JSON** 함수를 사용하려면 로드할 IoT Edge 모듈을 지정하는 JSON 파일에 대한 경로를 이 함수에 전달해야 합니다.

Hello World 샘플의 게이트웨이 프로세스에 대한 코드는 [main.c][lnk-main-c] 파일에서 찾을 수 있습니다. 읽기 쉽도록, 다음 코드 조각에서는 게이트웨이 프로세스 코드의 간략한 버전을 보여줍니다. 이 예제 프로그램은 게이트웨이를 만든 다음 게이트웨이를 허물기 전에 사용자가 **ENTER** 키를 누를 때까지 대기합니다.

```c
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

JSON 설정 파일에는 로드할 IoT Edge 모듈의 목록과 모듈 간의 링크가 포함되어 있습니다. 각각의 IoT Edge 모듈에서 다음을 지정해야 합니다.

* **name**: 모듈의 고유한 이름.
* **loader**: 원하는 모듈을 로드하는 방법을 알고 있는 로더. 로더는 다양한 유형의 모듈을 로드하기 위한 확장 지점입니다. Native C, Node.js, Java 및 .NET으로 작성된 모듈을 사용하기 위해 로더를 제공합니다. 헬로 월드 샘플에 있는 모든 모듈이 C 언어로 작성된 동적 라이브러리이므로 이 샘플에서는 네이티브 C 로더만 사용합니다. 다른 언어로 작성된 IoT Edge 모듈을 사용하는 방법에 대한 자세한 내용은 [Node.js](https://github.com/Azure/iot-edge/blob/master/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample) 또는 [.NET](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample) 샘플을 참조하세요.
    * **name**: 모듈을 로드하는 데 사용되는 로더의 이름.
    * **entrypoint**: 모듈을 포함하는 라이브러리에 대한 경로. 이 라이브러리는 Linux에서 .so 파일이고 Windows에서 .dll 파일입니다. 진입점은 사용된 로더의 유형에 따라 지정됩니다. Node.js 로더의 진입점은 .js 파일입니다. Java 로더의 진입점은 클래스 경로와 클래스 이름입니다. .NET 로더의 진입점은 어셈블리 이름과 클래스 이름입니다.

* **args**: 모듈에 필요한 구성 정보입니다.

다음 코드에서는 Linux에서 헬로 월드 샘플에 대한 모든 IoT Edge 모듈을 선언하는 데 사용된 JSON을 보여 줍니다. 모듈에 인수가 필요한지 여부는 모듈의 디자인에 달려 있습니다. 이 예에서 로거 모듈은 출력 파일에 대한 경로를 인수로 사용하고 hello\_world 모듈에는 인수가 없습니다.

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

또한 JSON 파일에는 broker에 전달되는 모듈 간의 링크가 포함되어 있습니다. 링크에는 두 가지 속성이 있습니다.

* **source**: `modules` 섹션 또는 "\*"의 모듈 이름입니다.
* **sink**: `modules` 섹션의 모듈 이름입니다.

각 링크는 메시지 경로 및 방향을 정의합니다. 모듈 `source`의 메시지는 모듈 `sink`에 전달됩니다. `source`은 "\*"로 설정되며 이는 모듈의 메세지가 `sink`에서 수신된다는 사실을 나타냅니다.

다음 코드에서는 Linux의 hello\_world 샘플에서 사용된 모듈 간의 링크를 구성하는 데 사용된 JSON을 보여 줍니다. 모듈 `hello_world`에 의해 생성된 모든 메시지는 모듈 `logger`에서 사용됩니다.

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>Hello\_world 모듈 메시지 게시

메시지를 게시하기 위해 hello\_world 모듈에 사용되는 코드는 ['hello_world.c'][lnk-helloworld-c] 파일에서 찾을 수 있습니다. 다음 코드 조각은 읽기 쉽도록 코멘트가 추가되고 일부 오류 처리 코드가 제거된 수정 버전을 보여 줍니다.

```c
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
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
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="helloworld-module-message-processing"></a>Hello\_world 모듈 메시지 처리

hello\_world 모듈에서는 다른 IoT Edge 모듈이 broker에 게시하는 메시지를 절대로 처리하지 않습니다. 따라서 hello\_world 모듈에서 메시지 콜백의 구현은 수행되지 않는 함수입니다.

```c
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>로거 모듈 메시지 게시 및 처리

로거 모듈은 broker에서 메시지를 수신하고 파일에 기록합니다. 메시지를 게시하지 않습니다. 따라서 로거 모듈의 코드는 **Broker_Publish** 함수를 절대 호출하지 않습니다.

[logger.c][lnk-logger-c] 파일의 **Logger_Recieve** 함수는 broker가 로거 모듈에 메시지를 전달하기 위해 호출하는 콜백입니다. 다음 코드 조각은 읽기 쉽도록 코멘트가 추가되고 일부 오류 처리 코드가 제거된 수정 버전을 보여 줍니다.

```c
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
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>다음 단계

Azure IoT Edge를 사용하는 방법에 대해 알아보려면 다음 문서를 참조하세요.

* [IoT Edge – Linux를 사용하는 시뮬레이션된 장치에서 장치-클라우드 메시지 보내기][lnk-gateway-simulated].
* GitHub의 [Azure IoT Edge][lnk-iot-edge]

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/iot-edge/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/iot-edge/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/iot-edge/blob/master/modules/logger/src/logger.c
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md