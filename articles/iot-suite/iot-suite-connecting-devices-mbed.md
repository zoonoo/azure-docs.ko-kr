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
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: ef7b78f85a787f8fbe22c0e26aa34f0cd1685d58
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017

---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>미리 구성된 원격 모니터링 솔루션에 장치 연결(mbed)

## <a name="scenario-overview"></a>시나리오 개요
이 시나리오에서는 원격 모니터링 [미리 구성된 솔루션][lnk-what-are-preconfig-solutions]에 다음과 같은 원격 분석 데이터를 보낼 장치를 만듭니다.

* 외부 온도
* 내부 온도
* 습도

간소함을 위하여 장치의 코드가 샘플 값을 생성하지만, 사용자는 자신의 장치에 실제 센서를 연결하고 실제 원격 분석 데이터를 보내어 샘플을 확장할 것을 권장합니다.

장치는 솔루션 대시보드에서 호출된 메서드 및 솔루션 대시보드에 설정된 desired 속성 값에 응답할 수도 있습니다.

이 자습서를 완료하려면 활성 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.

## <a name="before-you-start"></a>시작하기 전에
장치에 대한 코드를 작성하기 전에, 미리 구성된 원격 모니터링 솔루션을 프로비전하고 이 솔루션에 새로운 사용자 지정 장치를 프로비전해야 합니다.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>미리 구성된 사용자의 원격 모니터링 솔루션 프로비전
이 자습서에서 만드는 장치는 미리 구성된 [원격 모니터링][lnk-remote-monitoring] 솔루션의 인스턴스에 데이터를 전송합니다. Azure 계정에서 미리 구성된 원격 모니터링 솔루션을 미리 프로비전하지 않은 경우 다음 단계를 사용합니다.

1. <https://www.azureiotsuite.com/> 페이지에서 솔루션을 만들려면 **+**를 클릭합니다.
2. **원격 모니터링** 패널에서 **선택**을 클릭하여 솔루션을 만듭니다.
3. **원격 모니터링 솔루션 만들기** 페이지에서 선택한 **솔루션 이름**을 입력하고, 배포하려는 **지역**을 선택한 후, 사용하려는 Azure 구독을 선택합니다. 그런 다음 **솔루션 만들기**를 클릭합니다.
4. 프로비전 프로세스가 완료될 때까지 기다립니다.

> [!WARNING]
> 미리 구성된 솔루션에서는 청구 가능한 Azure 서비스를 사용합니다. 불필요한 비용을 방지하기 위해 완료된 후에는 미리 구성된 솔루션을 구독에서 제거해야 합니다. <https://www.azureiotsuite.com/> 페이지를 방문하여 미리 구성된 솔루션을 구독에서 완전히 제거할 수 있습니다.
> 
> 

원격 모니터링 솔루션의 프로비전 프로세스가 완료되면 **시작** 을 클릭하여 브라우저에서 솔루션 대시보드를 엽니다.

![솔루션 대시보드][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>원격 모니터링 솔루션에서 장치 프로비전
> [!NOTE]
> 솔루션에 장치가 이미 프로비전되어 있으면 이 단계를 건너뜁니다. 클라이언트 응용 프로그램을 만들 때 장치 자격 증명을 알아야 합니다.
> 
> 

미리 구성된 솔루션에 연결하는 장치는 유효한 자격 증명을 사용하여 IoT Hub에 자신을 식별할 수 있어야 합니다. 솔루션 대시보드에서 장치 자격 증명을 검색할 수 있습니다. 이 자습서의 뒷부분에서는 클라이언트 응용 프로그램에 있는 장치 자격 증명을 포함합니다.

원격 모니터링 솔루션에 장치를 추가하려면 솔루션 대시보드에서 다음 단계를 완료합니다.

1. 대시보드의 왼쪽 아래 모서리에서 **장치 추가**를 클릭합니다.
   
   ![장치 추가][1]
2. **사용자 지정 장치** 패널에서 **새로 추가**를 클릭합니다.
   
   ![사용자 지정 장치 추가][2]
3. **직접 나만의 장치 ID 정의**를 선택합니다. 장치 ID(예: **mydevice**)를 입력하고 **ID 확인**을 클릭하여 해당 이름이 이미 사용되고 있는지 확인한 후 **만들기**를 클릭하여 장치를 프로비전합니다.
   
   ![장치 ID 추가][3]
4. 장치 자격 증명(장치 ID, IoT Hub 호스트 이름 및 장치 키)을 적어 둡니다. 원격 모니터링 솔루션에 연결하려면 클라이언트 응용 프로그램에 이러한 값이 필요합니다. **완료**를 클릭합니다.
   
    ![장치 자격 증명 보기][4]
5. 솔루션 대시보드의 장치 목록에서 장치를 선택합니다. 그런 다음 **장치 세부 정보** 패널에서 **장치 사용**을 클릭합니다. 현재 장치 상태는 **실행 중**입니다. 이제 원격 모니터링 솔루션은 장치에서 원격 분석을 수신하고 장치에서 메서드를 호출할 수 있습니다.

[img-dashboard]: ./media/iot-suite-connecting-devices-mbed/dashboard.png
[1]: ./media/iot-suite-connecting-devices-mbed/suite0.png
[2]: ./media/iot-suite-connecting-devices-mbed/suite1.png
[3]: ./media/iot-suite-connecting-devices-mbed/suite2.png
[4]: ./media/iot-suite-connecting-devices-mbed/suite3.png

[lnk-what-are-preconfig-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

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

