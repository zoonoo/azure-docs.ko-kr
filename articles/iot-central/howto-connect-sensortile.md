---
title: SensorTile 장치를 Azure IoT Central 응용 프로그램에 연결 | Microsoft Docs
description: 장치 개발자는 SensorTile 장치를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 알아봅니다.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: ce0c5abe6e89094623c07afa2d1c85903e0e7ee7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877441"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>SensorTile 장치를 Azure IoT Central 응용 프로그램에 연결

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

이 문서에서는 장치 개발자가 Microsoft Azure IoT Central 응용 프로그램에 SensorTile 장치를 연결 하는 방법을 설명 합니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서의 단계를 완료 하려면 다음 리소스가 필요 합니다.

* SensorTile 장치입니다. 자세한 내용은 [SensorTile](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html)를 참조 하세요.
* Android 장치에 설치 된 ST의 센서 앱은 [여기에서 다운로드할](https://play.google.com/store/apps/details?id=com.st.bluems)수 있습니다. 자세한 내용은 다음을 참조 하세요. [ST 센서](https://www.st.com/stblesensor)
* Devkits 응용 프로그램 템플릿에서 만든 Azure IoT Central 응용 프로그램입니다. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
* **장치 템플릿** 페이지를 방문 하 고 **+ 새로 만들기**를 클릭 한 다음 **SensorTile** 템플릿을 선택 하 여 IoT Central 응용 프로그램에 **SensorTile** 장치 템플릿을 추가 합니다.

### <a name="get-your-device-connection-details"></a>장치 연결 정보 가져오기

Azure IoT Central 응용 프로그램에서 **SensorTile** 장치 템플릿의 실제 장치를 추가 하 고 장치 연결 정보를 적어 둡니다. **범위 ID**, **장치 id**및 **기본 키**:

1. Device Explorer에서 장치를 추가 합니다. **+ 새로 만들기** 를 선택 하 여 실제 장치를 추가 > 합니다.

    * 소문자 **장치 id**를 입력 하거나 제안 된 **장치 id**를 사용 합니다.
    * **장치 이름을**입력 하거나 제안 된 이름을 사용 합니다.

    ![장치 추가](media/howto-connect-sensortile/real-device.png)

1. 장치 연결 정보, **범위 ID**, **장치 id**및 **기본 키**를 가져오려면 장치 페이지에서 **연결** 을 선택 합니다.

    ![연결 세부 정보](media/howto-connect-sensortile/connect-device.png)

1. 연결 정보를 적어 둡니다. 다음 단계에서 DevKit 장치를 준비할 때 인터넷에서 일시적으로 연결을 끊었습니다.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>모바일 응용 프로그램을 사용 하 여 SensorTile 설정

이 섹션에서는 응용 프로그램 펌웨어를 장치에 푸시하는 방법에 대해 알아봅니다. 그런 다음, Bluetooth 저 에너지 (이 하) 연결을 사용 하 여 ST의 센서 모바일 앱을 통해 IoT Central에 장치 데이터를 전송 하는 방법을 설명 합니다.

1. ST의 센서 앱을 열고 **새 앱 만들기** 단추를 누릅니다.

    ![앱 만들기](media/howto-connect-sensortile/create-app.png)

1. **Barometer** 응용 프로그램을 선택 합니다.
1. 업로드 단추를 누릅니다.

    ![Barometer 업로드](media/howto-connect-sensortile/barometer-upload.png)

1. SensorTile와 연결 된 재생 단추를 누릅니다.
1. 프로세스가 완료 되 면 SensorTile는 전체 온도, 압력 및 습도를 스트리밍합니다.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>SensorTile를 클라우드에 연결

이 섹션에서는 모바일 응용 프로그램에 SensorTile를 연결 하 고 모바일 응용 프로그램을 클라우드에 연결 하는 방법에 대해 알아봅니다.

1. 왼쪽 메뉴를 사용 하 여 **클라우드 로깅** 단추를 선택 합니다.

    ![클라우드 로깅](media/howto-connect-sensortile/cloud-logging.png)

1. 클라우드 공급자로 **Azure IoT Central** 를 선택 합니다.
1. 이전에 언급 한 장치 ID 및 범위 ID를 삽입 합니다.

    ![자격 증명](media/howto-connect-sensortile/credentials.png)

1. **응용 프로그램 키** 라디오 단추를 선택 합니다.
1. **연결** 을 클릭 하 고 업로드 하려는 원격 분석 데이터를 선택 합니다.
1. 몇 초 후에 IoT Central 응용 프로그램 대시보드에 데이터가 표시 됩니다.

## <a name="sensortilebox-device-template-details"></a>SensorTile 장치 템플릿 정보

SensorTile 장치 템플릿에서 만든 응용 프로그램은 다음과 같은 특성을 사용 합니다.

### <a name="telemetry"></a>원격 분석

| 필드 이름     | 단위  | 최소 | 최대값 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 1              |
| gyroscopeZ     | dps   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에 SensorTile을 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 고유한 IoT 장치에 대 한 [사용자 지정 장치 템플릿을 설정 하는 방법을](howto-set-up-template.md) 배우는 것입니다.
