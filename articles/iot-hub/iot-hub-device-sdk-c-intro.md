<properties
	pageTitle="C용 Azure IoT 장치 SDK 소개"
	description="C용 Azure IoT 장치 SDK에 대해 알아보고 샘플 코드로 작업을 시작합니다."
	services="iot-hub"
	documentationCenter="na"
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/30/2015"
     ms.author="michelb"/>

# C용 Azure IoT 장치 SDK 소개

**Azure IoT 장치 SDK**는 이벤트를 전송하고 **Azure IoT Hub** 서비스에서 메시지를 수신하는 프로세스를 단순화하도록 설계된 라이브러리 집합입니다. 각각 특정 플랫폼을 대상으로 하는 다양하게 변형된 SDK가 제공되지만 이 문서에서는 **C용 Azure IoT 장치 SDK**에 중점을 둡니다.

C용 Azure IoT 장치 SDK는 이식성을 최대화하기 위해 ANSI C(C99)로 작성됩니다. 이렇게 하면 다양한 플랫폼 및 장치에서 잘 작동하며 특히 디스크 및 메모리 사용 공간을 최소화하는 것이 우선 시 되는 곳에서 적합합니다.

이 SDK는 광범위한 플랫폼에서 테스트되었습니다(자세한 내용은 [SDK 설명서](https://github.com/Azure/azure-iot-sdks/tree/master/c) 참조). 이 문서는 *Windows* 플랫폼에서 실행되는 샘플 코드 연습을 포함하지만 아래 설명된 코드는 지원되는 모든 플랫폼에서 정확히 동일합니다.

이 문서에서는 C용 Azure IoT 장치 SDK의 아키텍처를 소개합니다. 장치 라이브러리를 초기화하고 IoT Hub로 이벤트를 전송하며 메시지를 수신하는 방법을 보여줍니다. 이 문서의 정보로 SDK 사용을 시작하기에 충분하지만 라이브러리에 대한 추가 정보를 얻을 수 있는 위치에 대한 포인터도 제공합니다.


## SDK 아키텍처

다음 GitHub 리포지토리에서 **C용 Azure IoT 장치 SDK**를 찾을 수 있습니다.

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

최신 버전의 라이브러리를 이 리포지토리의 **마스터** 분기에서 찾을 수 있습니다.

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

이 리포지토리는 Azure IoT 장치 SDK의 전체 제품군을 포함합니다. 하지만 이 문서는 **c** 폴더에서 찾을 수 있는 *C용* Azure IoT 장치 SDK에 대해 다룹니다.

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

SDK의 핵심 구현은 리포지토리의 **common**, **iothub\_client** 및 **serializer** 폴더에서 찾을 수 있습니다. **common** 폴더에는 라이브러리 전반에 걸쳐 사용되는 공유 코드가 포함됩니다(일반적으로 **common** 폴더의 코드는 직접 사용하지 않음). 그러나 **iothub\_client** 및 **serializer** 폴더에는 코드에 사용할 고유한 두 계층의 SDK 구현이 포함됩니다.

-   **IoTHubClient** - **iothub\_client** 폴더에는 SDK, **IoTHubClient** 라이브러리에서 최하위 API 계층의 구현이 포함됩니다. 이 **IoTHubClient** 라이브러리에는 IoT Hub로 이벤트 전송 및 메시지 수신을 위한 API가 포함됩니다. 이 라이브러리를 사용하는 경우 사용자가 메시지 직렬화를 구현해야 하며 IoT Hub와 통신하기 위한 기타 세부 사항도 직접 처리해야 합니다.

-   **직렬 변환기** - **직렬 변환기** 폴더에는 **직렬 변환기** 라이브러리의 구현이 포함됩니다. **직렬 변환기** 라이브러리는 **IoTHubClient**에서 제공하는 기능 위쪽에 모델링 기능을 추가합니다. **직렬 변환기** 라이브러리를 사용하는 경우 IoT Hub로 전송할 이벤트와 수신하려는 메시지를 지정하는 모델을 정의하는 것으로 시작합니다. 하지만 모델을 정의한 후에는 SDK에서 API 노출 영역을 제공하므로 직렬화 세부 사항에 대해 신경쓰지 않고도 이벤트 및 메시지에 대해 쉽게 작업할 수 있습니다.

이러한 모든 내용은 몇 가지 예제 코드를 살펴보면서 쉽게 이해할 수 있습니다. 다음 섹션에서는 SDK에 포함된 몇 가지 샘플 응용 프로그램을 안내합니다. SDK 아키텍처 계층의 다양한 기능에 대해 쉽게 설명하고 API의 작동 방식을 소개합니다.

## 샘플을 실행하기 전에

C용 Azure IoT 장치 SDK에서 샘플을 실행하려면 두 가지 작업을 완료해야 합니다. 개발 환경을 준비하고 장치 자격 증명을 얻어야 합니다. SDK에 포함된 [추가 정보 파일](https://github.com/Azure/azure-iot-sdks/tree/master/c)은 두 작업에 대한 지침을 제공합니다. 또한 다음 섹션에서는 이러한 지침에 대한 몇 가지 추가 설명을 포함합니다.

### 개발 환경 준비

먼저, GitHub에서 SDK의 복사본을 가져온 후 소스를 빌드해야 합니다. GitHub 리포지토리의 **마스터** 분기에서 원본의 복사본을 가져와야 합니다.

<https://github.com/Azure/azure-iot-sdks>

소스 복사본을 다운로드한 경우 코드를 실행하기 전에 몇 가지 단계를 완료해야 합니다. [개발 환경 준비](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md)라는 SDK 문서에 이러한 단계가 요약되어 있습니다. Windows에서 수행해야 하는 작업은 대부분 [Qpid Proton 라이브러리 준비](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#preparing-qpid-proton-libraries-in-windows) 뿐만 아니라 [환경 확인](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#verify-your-environment)을 포함합니다. 하지만 여기에는 준비 가이드에 설명된 절차를 완료하는 데 도움이 되는 몇 가지 팁이 있습니다.

-   **PROTON\_PATH** 환경 변수를 만들 때는 여기에 표시된 대로 **시스템** 환경 변수로 만듭니다.

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   **CMake** 유틸리티를 설치하는 경우 **CMake**를 **모든 사용자**를 위한 시스템 PATH에 추가하는 옵션을 선택합니다(**현재 사용자**를 추가하는 것도 잘 작동함).

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   Python의 올바른 버전을 설치해야 합니다. 일반적으로 Windows에서는 x86 버전을 설치해야 합니다.

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

설치 관리자에서 기본 옵션을 사용할 수 있습니다.

- Git 도구 Python 디렉터리를 시스템 **PATH** 환경 변수에 추가합니다. Windows에서는 다음과 같이 표시됩니다. ![](media/iot-hub-device-sdk-c-intro/16-PythonPath.PNG)

-   **VS2015용 개발자 명령 프롬프트**를 열기 전에 *Git 명령줄 도구*를 설치합니다. 이러한 도구를 설치하려면 다음 단계를 수행합니다.

	1. **Visual Studio 2015** 설치 프로그램을 시작하거나 **프로그램 및 기능** 제어판에서 **Microsoft Visual Studio 2015**를 선택하고 **변경**을 선택합니다.
	2. 설치 프로그램에서 **Windows용 Git** 기능이 선택되어 있지만 **Visual Studio용 GitHub 확장** 옵션을 확인하여 IDE 통합 기능을 제공하려 할 수도 있습니다. ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)
	3. 설치 마법사를 완료하여 도구를 설치합니다.
	4. Git 도구 **bin** 디렉터리를 시스템 **PATH** 환경 변수에 추가합니다. Windows에서는 다음과 같이 표시됩니다. ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

명령 프롬프트에서 **build\_proton.cmd**를 실행하면 스크립트가 GitHub 리포지토리에서 Proton 원본을 가져온 후 Proton 라이브러리를 빌드합니다.

MQTT 샘플을 실행하려는 경우 SDK 설명서에서 [Apache MQTT 라이브러리 빌드](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#building-the-apache-mqtt-library-in-windows)에 대한 지침을 수행하려 합니다. 아무튼 마지막으로 **build.cmd**을 실행해야 합니다.

이전 단계를 완료했다면 샘플 응용 프로그램을 컴파일할 준비가 되었습니다.

### 장치 자격 증명 가져오기

이제 개발 환경이 설정되었으며 마지막으로 장치 자격 증명 집합을 가져오면 됩니다. IoT Hub에 액세스할 수 있는 장치의 경우 장치를 IoT Hub 장치 레지스트리에 먼저 추가해야 합니다. 장치를 추가하면 장치를 IoT Hub에 연결하기 위해 필요한 장치 자격 증명 집합을 얻게 됩니다. 다음 섹션에서 살펴볼 샘플 응용 프로그램은 이러한 자격 증명을 **장치 연결 문자열**로 예상합니다.

[장치 탐색기](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) 도구(Azure IoT 장치 SDK에 포함)는 Azure IoT 서비스 라이브러리를 사용하여 장치 추가 등 IoT Hub에서 다양한 기능을 수행합니다. 장치 탐색기를 사용하여 장치를 추가하는 경우 해당 연결 문자열을 얻게 됩니다. 이 연결 문자열은 샘플 응용 프로그램을 실행하는 데 필요합니다.

이러한 프로세스에 익숙하지 않은 사용자를 위해 다음 절차에서는 장치 탐색기를 사용하여 장치를 추가하고 장치 연결 문자열을 가져오는 방법에 대해 설명합니다.

**Visual Studio 2015**에서 **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)**을 열고 솔루션을 빌드합니다. 프로그램을 실행하면 다음 인터페이스가 표시됩니다.

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

첫 번째 필드에 **IoT Hub 연결 문자열**을 입력하고 **업데이트** 단추를 클릭합니다. 그러면 IoT Hub와 통신할 수 있도록 도구가 구성됩니다.

IoT Hub 연결 문자열이 구성되었으면 **관리** 탭을 클릭합니다.

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

여기서 IoT Hub에 등록된 장치를 관리할 수 있습니다.

**만들기** 단추를 클릭하여 장치를 만들 수 있습니다. 키 집합(기본 및 보조)이 모두 채워진 상태로 대화 상자가 표시됩니다. 이제 **장치 ID**를 입력하고 **만들기** 단추를 클릭하면 됩니다.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

장치를 만들었으면 방금 만든 장치와 함께 등록된 모든 장치로 장치 목록이 업데이트됩니다. 새 장치를 마우스 오른쪽 단추로 클릭할 경우 이 메뉴가 표시됩니다.

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

**선택한 장치에 대한 연결 문자열 복사** 옵션을 선택하면 장치에 대한 연결 문자열이 클립보드에 복사됩니다. 이 복사본을 보관하세요. 아래에 설명된 샘플 응용 프로그램을 실행할 때 필요합니다.

위의 단계를 완료했으면 일부 코드를 실행할 준비가 되었습니다. 아래 설명된 두 샘플에는 주요 원본 파일의 맨 위에 연결 문자열을 입력할 수 있는 상수가 포함되어 있습니다. 예를 들어 다음은 **iothub\_client\_sample\_amqp**의 해당 줄입니다.

```
static const char* connectionString = "[device connection string]";
```

다음을 수행하려면 여기에 장치 연결 문자열을 입력하고 솔루션을 다시 컴파일하면 샘플을 실행할 수 있습니다.

## IoTHubClient

azure-iot-sdks 리포지토리의 **iothub\_client** 폴더 내에는 **iothub\_client\_sample\_amqp**라는 응용 프로그램이 포함된 **samples** 폴더가 있습니다.

Windows 버전의 **iothub\_client\_sample\_ampq** 응용 프로그램은 다음과 같은 Visual Studio 솔루션을 포함합니다.

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

이 솔루션에는 다음의 단일 프로젝트가 포함됩니다. 하지만 이 솔루션에 설치된 네 가지 GitHub 패키지는 주목할 만한 가치가 있습니다.

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

SDK를 사용하여 작업하는 경우 항상 **Microsoft.Azure.IoTHub.Common** 패키지가 필요합니다. 또한 이 샘플은 AMQP에 의존하므로 **Apache.QPID.Proton.AzureIoT** 및 **Microsoft.Azure.IoTHub.AmqpTransport** 패키지를 포함해야 합니다.(HTTP에 대한 해당 패키지가 존재함) 이 샘플에서는 **IoTHubClient** 라이브러리를 사용하므로 솔루션에 **Microsoft.Azure.IoTHub.IoTHubClient** 프로젝트도 포함해야 합니다.

**iothub\_client\_sample\_amqp.c** 원본 파일에서 샘플 응용 프로그램에 대한 구현을 찾을 수 있습니다.

  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

이 샘플 응용 프로그램을 사용하여 **IoTHubClient** 라이브러리를 사용하는 데 필요한 내용을 안내합니다.


### 라이브러리 초기화

라이브러리로 작업을 시작하려면 먼저 IoT Hub 클라이언트 핸들을 할당해야 합니다.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

장치 탐색기에서 가져온 이 함수에 장치 연결 문자열의 복사본 전달합니다. 또한 사용하려는 프로토콜도 지정합니다. 이 예에서는 AMQP를 사용하지만 HTTP도 옵션으로 선택할 수 있습니다.

유효한 **IOTHUB\_CLIENT\_HANDLE**이 있는 경우 API 호출을 시작하여 이벤트를 전송하고 IoT Hub에서 메시지를 수신할 수 있습니다. 이에 대해서는 다음에 살펴보겠습니다.

### 이벤트 전송

IoT Hub로 이벤트를 전송하려면 다음 단계를 완료해야 합니다.

먼저, 메시지를 만듭니다.

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

다음으로 메시지를 전송합니다.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

메시지를 전송할 때마다 데이터가 전송될 때 호출되는 콜백 함수에 대한 참조를 지정합니다.

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

메시지 작업을 완료했으면 **IoTHubMessage\_Destroy** 함수에 대한 호출을 확인합니다. 이 작업을 수행하여 메시지를 만들 때 할당된 리소스를 해제해야 합니다.

### 메시지 수신

메시지 수신은 비동기 작업입니다. 먼저, 장치에서 메시지를 수신할 때 호출할 콜백을 등록합니다.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

마지막 매개 변수는 원하는 항목에 대한 void 포인터입니다. 이 샘플에서는 정수에 대한 포인터지만 보다 복잡한 데이터 구조체에 대한 포인터일 수 있습니다. 이렇게 하면 이 함수의 호출자와 함께 콜백 함수가 공유 상태로 작동하도록 할 수 있습니다.

장치가 메시지를 수신할 때는 등록된 콜백 함수가 호출됩니다.

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

**IoTHubMessage\_GetByteArray** 함수를 사용하여 메시지를 검색하는 방법을 확인합니다. 이 예에서는 문자열입니다.

### 라이브러리 초기화 취소

이벤트 전송 및 메시지 수신을 완료하면 다음 함수 호출로 IoT 라이브러리를 초기화 취소할 수 있습니다.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

이 작업은 **IoTHubClient\_CreateFromConnectionString** 함수로 이전에 할당한 리소스를 해제합니다.

표시된 것처럼 **IoTHubClient** 라이브러리로 간편하게 이벤트를 전송하고 메시지를 수신할 수 있습니다. 라이브러리가 사용할 프로토콜 등 IoT Hub와의 통신에 대한 세부 사항을 처리합니다(개발자 관점에서는 간단한 구성 옵션).

**IoTHubClient** 라이브러리로 장치에서 IoT Hub로 전송한 이벤트를 직렬화하는 방법을 정밀하게 제어할 수도 있습니다. 이것이 장점인 경우도 있지만 신경쓰지 않아도 되는 구현 세부 사항인 경우도 있습니다. 이 경우 **serializer** 라이브러리를 사용하는 것이 좋습니다. 이 내용은 다음에 다룹니다.

## 직렬 변환기

개념적으로 **serializer** 라이브러리는 SDK의 **IoTHubClient** 라이브러리 위쪽에 있습니다. IoT Hub와의 기본 통신에 **IoTHubClient**를 사용하지만 개발자가 메시지 직렬화를 처리하는 부담을 없애주는 모델링 기능을 추가합니다. 이 라이브러리가 동작하는 방식은 예제를 통해 가장 잘 이해할 수 있습니다.

azure-iot-sdks 리포지토리의 **serializer** 폴더 내에는 **simplesample\_amqp**라는 응용 프로그램이 포함된 **samples** 폴더가 있습니다. Windows 버전의 이 샘플은 다음과 같은 Visual Studio 솔루션을 포함합니다.

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

이전 샘플과 마찬가지로 이 하나에는 여러 NuGet 패키지가 포함됩니다.

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

이전 샘플에서 이 중 대부분을 살펴봤지만 **Microsoft.Azure.IoTHub.Serializer**는 새롭습니다. **직렬 변환기** 라이브러리를 사용할 경우 필요합니다.

**iothub\_client\_sample\_amqp.c** 코드 파일에서 샘플 응용 프로그램에 대한 구현을 찾을 수 있습니다.

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

다음 섹션에서는 이 샘플의 주요 부분을 안내합니다.

### 라이브러리 초기화

**직렬 변환기** 라이브러리 작업을 시작하려면 초기화 API를 호출해야 합니다.

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

**serializer\_init** 함수에 대한 호출은 기본 라이브러리를 초기화하는 데 사용되는 일회성 호출입니다. 그런 다음 **IoTHubClient** 샘플에서 본 것과 같은 API인 **IoTHubClient\_CreateFromConnectionString** 함수를 호출합니다. 이 호출은 장치 연결 문자열을 설정하며 이 연결 문자열로도 사용할 프로토콜을 선택할 수 있습니다. 이 샘플에서는 전송으로 AMQP를 사용하지만 HTTP를 사용할 수도 있습니다.

마지막으로 **CREATE\_MODEL\_INSTANCE** 함수를 호출합니다. **WeatherStation**은 모델의 네임스페이스이며 **ContosoAnemometer**는 모델의 이름입니다. 모델 인스턴스를 만들면 이를 사용하여 이벤트 전송 및 메시지 수신을 시작할 수 있습니다. 하지만 먼저 모델을 이해하는 것이 중요합니다.

### 모델 정의

**serializer** 라이브러리의 모델은 장치에서 IoT Hub로 전송할 수 있는 이벤트와 모델링 언어에서 *동작*이라고 하는 수신할 수 있는 메시지를 정의합니다. **simplesample\_amqp** 샘플 응용 프로그램에서처럼 C 매크로 집합을 사용하여 모델을 정의합니다.

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

**BEGIN\_NAMESPACE** 및 **END\_NAMESPACE** 매크로 모두 인수로 모델의 네임스페이스를 사용합니다. 이러한 매크로 사이에 있는 모든 내용은 모델의 정의와 모델에서 사용하는 데이터 구조체로 간주됩니다.

이 예에서는 **ContosoAnemometer**라는 단일 모델이 있습니다. 이 모델은 장치에서 IoT Hub로 전송할 수 있는 두 이벤트로 **DeviceId** 및 **WindSpeed**를 정의합니다. 또한 장치에서 수신할 수 있는 세 가지 동작(메시지)으로 **TurnFanOn**, **TurnFanOff** 및 **SetAirResistance**를 정의합니다. 각 이벤트에는 형식이 있고 각 동작에는 이름(필요에 따라 매개 변수 집합)이 있습니다.

모델에 정의된 이벤트 및 동작은 이벤트를 IoT Hub에 전송하고 장치에 전송된 메시지에 응답하는 데 사용할 수 있는 API 노출 영역을 정의합니다. 예제를 통해 가장 잘 이해할 수 있습니다.

### 이벤트 전송

모델은 IoT Hub로 전송할 수 있는 이벤트를 정의합니다. 이 샘플에서는 **WITH\_DATA** 매크로를 사용하여 정의된 두 이벤트 중 하나입니다. 예를 들어 **WindSpeed** 이벤트를 IoT Hub에 전송하려면 몇 가지 단계를 거쳐야 합니다. 먼저, 전송할 데이터를 설정합니다.

```
myWeather->WindSpeed = 15;
```

이전에 정의한 모델을 통해 구조체의 멤버를 설정하여 간단하게 수행할 수 있습니다. 다음으로 전송할 이벤트를 직렬화해야 합니다.

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

다음 코드는 이벤트를 버퍼에 직렬화합니다(**대상**으로 참조). 마지막으로 다음 코드로 이벤트를 IoT Hub로 전송합니다.

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

다음은 직렬화된 이벤트를 IoT Hub로 전송하는 샘플 응용 프로그램의 도우미 함수입니다.

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

이 코드는 바이트 배열에서 메시지를 만든 후 **IoTHubClient\_SendEventAsync**를 사용하여 IoT Hub로 전송했던 **iothub\_client\_sample\_amqp** 응용 프로그램의 내용과 매우 유사합니다. 그런 다음 이전에 할당한 메시지 핸들 및 직렬화된 데이터 버퍼를 해제해야 합니다.

**IoTHubClient\_SendEventAsync**의 끝에서 두 번째 매개 변수는 데이터를 성공적으로 전송할 때 호출되는 콜백 함수에 대한 참조입니다. 다음은 콜백 함수의 예입니다.

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

두 번째 매개 변수는 사용자 컨텍스트에 대한 포인터로, **IoTHubClient\_SendEventAsync**로 전달한 것과 같은 포인터입니다. 이 경우 이 컨텍스트는 간단한 카운터이지만 사용자가 원하는 대로 정할 수 있습니다.

이제 이벤트 전송이 끝났습니다. 이제 메시지를 수신하는 방법을 알아보겠습니다.

### 메시지 수신

메시지 수신은 **IoTHubClient** 라이브러리에서 메시지가 작동하는 방식과 유사하게 작동합니다. 먼저 메시지 콜백 함수를 등록합니다.

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

그런 다음 메시지를 수신할 때 호출되는 콜백 함수를 작성합니다.

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

다음 코드는 상용구로 모든 솔루션에 대해 동일합니다. 이 함수는 메시지를 수신하고 **EXECUTE\_COMMAND** 호출을 통해 적절한 함수로 라우팅합니다. 기본적으로 해당 시점에 어떤 함수를 호출하느냐는 모델에서 동작 정의에 따라 달라집니다...

모델에서 동작을 정의할 때 장치에서 해당 메시지를 수신할 때 호출되는 함수를 구현해야 합니다. 예를 들어 모델에서 다음 동작을 정의하는 경우:

```
WITH_ACTION(SetAirResistance, int, Position)
```

다음 서명과 함께 함수를 정의해야 합니다.

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

함수 이름은 모델에서 동작 이름과 일치하고 함수 매개 변수는 동작에 대해 지정된 매개 변수와 일치하는 것을 알 수 있습니다. 첫 번째 매개 변수는 항상 필수이며 모델 인스턴스에 대한 포인터를 포함합니다.

장치에서 이 서명과 일치하는 메시지를 수신하면 해당 함수가 호출됩니다. 따라서 **IoTHubMessage**의 상용구 코드를 포함해야 하는 것 외에도, 메시지 수신은 모델에 정의된 각 동작에 대한 간단한 함수를 정의하는 정도의 문제입니다.

### 라이브러리 초기화 취소

데이터 전송 및 메시지 수신을 완료했으면 IoT 라이브러리를 초기화 취소할 수 있습니다.

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

이러한 세 함수 각각은 앞에서 설명한 세 초기화 함수에 맞춰집니다. 이러한 API를 호출하면 이전에 할당된 리소스를 해제합니다.

## 다음 단계

이 문서에서는 **C용 Azure IoT 장치 SDK**에서 라이브러리 사용에 대한 기본 사항을 다룹니다. SDK에 포함된 사항, 아키텍처 및 Windows 샘플 작업을 시작하는 방법을 이해하기에 충분한 정보를 제공합니다. 다음 문서에서는 [IoTHubClient 라이브러리에 대한 자세한 정보](iot-hub-device-sdk-c-iothubclient.md)를 설명하여 SDK를 계속 설명합니다.

<!---HONumber=Oct15_HO2-->