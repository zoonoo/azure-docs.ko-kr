---
title: "mbed에서 C를 사용하여 장치 연결 | Microsoft Docs"
description: "mbed 장치에서 실행되는 C로 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: df9772796796f7383aafc583b01f299a53679d88
ms.openlocfilehash: 12535cbb6fa63c24dd63903380d697f8f38db6f9
ms.contentlocale: ko-kr
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>미리 구성된 원격 모니터링 솔루션에 장치 연결(mbed)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>C 샘플 솔루션 빌드 및 실행

다음 지침은 [mbed 사용 가능 Freescale FRDM-K64F][lnk-mbed-home] 장치를 원격 모니터링 솔루션에 연결하기 위한 단계를 설명합니다.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>네트워크 및 데스크톱 컴퓨터에 mbed 장치 연결

1. 이더넷 케이블을 사용하여 mbed 장치를 네트워크에 연결합니다. 이 단계는 샘플 응용 프로그램에서 인터넷에 액세스해야 하기 때문에 필요합니다.

1. [mbed 시작][lnk-mbed-getstarted]을 참조하여 mbed 장치를 데스크톱 PC에 연결합니다.

1. 데스크톱 PC에서 Windows를 실행 중인 경우 [PC 구성][lnk-mbed-pcconnect]을 참조하여 mbed 장치에 대한 직렬 포트 액세스를 구성합니다.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>mbed 프로젝트 만들기 및 샘플 코드 가져오기

해당 단계를 수행하여 mbed 프로젝트에 몇 가지 샘플 코드를 추가합니다. 원격 모니터링 시작 프로젝트를 가져온 다음 AMQP 프로토콜 대신 MQTT 프로토콜을 사용하도록 프로젝트를 변경합니다. 현재 IoT Hub의 장치 관리 기능을 사용하려면 MQTT 프로토콜을 사용해야 합니다.

1. 웹 브라우저에서 mbed.org [개발자 사이트](https://developer.mbed.org/)로 이동합니다. 아직 등록하지 않은 경우 새 계정(무료)을 만들 수 있는 옵션이 표시됩니다. 그렇지 않은 경우 계정 자격 증명으로 로그인합니다. 그런 다음 페이지의 오른쪽 위 모서리에서 **컴파일러** 를 클릭합니다. 그러면 *작업 영역* 인터페이스로 이동합니다.

1. 사용 중인 하드웨어 플랫폼이 창의 오른쪽 위 모서리에 표시되는지 확인하거나 오른쪽 모서리에 있는 아이콘을 클릭하여 하드웨어 플랫폼을 선택합니다.

1. 주 메뉴에서 **가져오기** 를 클릭합니다. 그런 다음 **URL에서 가져오려면 여기를 클릭**을 클릭합니다.
   
    ![mbed 작업 영역으로 가져오기 시작][6]

1. 팝업 창에서 샘플 코드에 대한 링크인 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/을 입력한 다음 **가져오기**를 클릭합니다.
   
    ![mbed 작업 영역에 샘플 코드 가져오기][7]

1. mbed 컴파일러 창에서 이 프로젝트를 가져오면 다양한 라이브러리도 가져온다는 사실을 확인할 수 있습니다. 일부는 Azure IoT 팀([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/))에서 제공 및 유지 관리되며 일부는 mbed 라이브러리 카탈로그에서 사용할 수 있는 타사 라이브러리입니다.
   
    ![mbed 프로젝트 보기][8]

1. **프로그램 작업 영역**에서 마우스 오른쪽 단추로 **iothub\_amqp\_transport** 라이브러리를 클릭하고 **삭제**를 클릭한 다음 **확인**을 클릭하여 확인합니다.

1. **프로그램 작업 영역**에서 마우스 오른쪽 단추로 **azure\_amqp\_c** 라이브러리를 클릭하고 **삭제**를 클릭한 다음 **확인**을 클릭하여 확인합니다.

1. **프로그램 작업 영역**에서 마우스 오른쪽 단추로 **remote_monitoring** 프로젝트를 클릭하고 **라이브러리 가져오기**를 선택한 다음 **URL에서**를 선택합니다.
   
    ![mbed 작업 영역으로 라이브러리 가져오기 시작][6]

1. 팝업 창에서 MQTT 전송 라이브러리에 대한 링크인 https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/를 입력한 다음 **가져오기**를 클릭합니다.
   
    ![mbed 작업 영역으로 라이브러리 가져오기][12]

1. 이전 단계를 반복하여 https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c/에서 MQTT 라이브러리를 추가합니다.

1. 이제 작업 영역이 다음과 같이 표시됩니다.

    ![mbed 작업 영역 보기][13]

1. remote\_monitoring\remote_monitoring.c 파일을 열고 기존 `#include` 문을 다음 코드로 바꿉니다.

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. remote\_monitoring\remote\_monitoring.c 파일에서 나머지 모든 코드를 삭제합니다.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

코드를 추가하여 **remote\_monitoring\_run** 함수를 호출한 다음 장치 응용 프로그램을 빌드하고 실행합니다.

1. remote\_monitoring.c 파일 끝에 다음 코드로 **main** 함수를 추가하여 **remote\_monitoring\_run** 함수를 호출합니다.
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. **컴파일** 을 클릭하여 프로그램을 빌드합니다. 안전하게 모든 경고를 무시할 수 있지만 빌드가 오류를 생성하는 경우 계속하기 전에 해결하십시오.

1. 빌드가 성공한 경우 mbed 컴파일러 웹 사이트에서는 프로젝트 이름이 지정된 .bin 파일을 생성하여 로컬 컴퓨터에 다운로드합니다. .bin 파일을 장치에 복사합니다. .bin 파일을 장치에 저장하면 장치가 다시 시작하고 .bin 파일에 포함된 프로그램이 실행됩니다. 언제든지 mbed 장치의 리셋 단추를 눌러 프로그램을 수동으로 다시 시작할 수 있습니다.

1. PuTTY와 같은 SSH 클라이언트 응용 프로그램을 사용하여 장치에 연결합니다. Windows 장치 관리자를 확인하여 장치가 사용하는 직렬 포트를 확인할 수 있습니다.
   
    ![][11]

1. PuTTY에서 **직렬** 연결 형식을 클릭합니다. 장치는 일반적으로 9600 보드로 연결되므로 **속도** 상자에 9600을 입력합니다. 그런 다음 **열기**를 클릭합니다.

1. 프로그램이 실행을 시작합니다. 연결할 때 프로그램이 자동으로 시작되지 않는 경우 보드를 다시 설정해야 할 수 있습니다(CTRL + Break를 누르거나 보드의 다시 설정 단추를 누름).
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

