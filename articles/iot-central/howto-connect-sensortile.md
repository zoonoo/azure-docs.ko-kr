---
title: Azure IoT Central 응용 프로그램에 SensorTile.box 장치 연결 | Microsoft Docs
description: 장치 개발자 SensorTile.box 장치를 Azure IoT Central 응용 프로그램에 연결 하는 방법에 알아봅니다.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472249"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에 SensorTile.box 장치 연결

이 문서에서는 설명 장치 개발자 SensorTile.box 장치를 Microsoft Azure IoT Central 응용 프로그램에 연결 하는 방법입니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료 하려면 다음 리소스가 필요 합니다.

* SensorTile.box 장치입니다. 자세한 내용은 [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html)합니다.
* ST BLE 센서 앱이 Android 장치에서 설치를 할 수 있습니다 [여기에서 다운로드](https://play.google.com/store/apps/details?id=com.st.bluems)합니다. 자세한 정보: [ST BLE 센서](https://www.st.com/stblesensor)
* 만든 Azure IoT Central 응용 프로그램을 **DevKits** 응용 프로그램 템플릿. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
* 추가 합니다 **SensorTile.box** 방문 하 여 IoT Central 응용 프로그램에 장치 템플릿을 **장치 템플릿** 페이지를 클릭 하 **+ 새로 만들기**를 선택 합니다 **SensorTile.box** 템플릿.

### <a name="get-your-device-connection-details"></a>장치 연결 정보 가져오기

Azure IoT Central 응용 프로그램에서 실제 장치를 추가 합니다 **SensorTile.box** 장치 템플릿 및 장치 연결 세부 정보를 기록 합니다. **ID 범위**하십시오 **장치 ID**, 및 **기본 키**:

1. Device Explorer 장치를 추가 합니다. 선택 **+ 새로 만들기 > 실제** 실제 장치를 추가 합니다.

    * 소문자 입력 **장치 ID**, 제안 된 사용할지 **장치 ID**합니다.
    * 입력 한 **장치 이름**, 제안 된 이름을 사용 하거나

    ![장치 추가](media/howto-connect-sensortile/real-device.png)

1. 장치 연결 세부 정보를 가져오려면 **범위 ID**, **장치 ID**, 및 **Primary key**를 선택 **Connect** 장치 페이지에서.

    ![연결 세부 정보](media/howto-connect-sensortile/connect-device.png)

1. 연결 세부 정보를 기록해 둡니다. 일시적으로 연결이 끊긴 인터넷에서 다음 단계에 DevKit 장치를 준비 합니다.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>모바일 응용 프로그램을 사용 하 여 SensorTile.box 설정

이 섹션에서는 장치에서 응용 프로그램 펌웨어를 푸시하는 방법을 알아봅니다. 다음 있습니다 에너지 BLE (Bluetooth Low) 연결을 사용 하 여 ST BLE 센서 모바일 앱을 통해 IoT central 장치 데이터를 보내는 방법.

1. ST BLE 센서 앱 및 키를 눌러 엽니다는 **새 앱 만들기** 단추입니다.

    ![앱 만들기](media/howto-connect-sensortile/create-app.png)

1. 선택 된 **지표** 응용 프로그램입니다.
1. 업로드 단추를 누릅니다.

    ![지표 업로드](media/howto-connect-sensortile/barometer-upload.png)

1. 프로그램 SensorTile.box 연관 play 단추를 누릅니다.
1. 프로세스를 완료 하는 경우는 SensorTile.box 온도, 압력 및 습도 BLE 통해 스트리밍합니다.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>SensorTile.box를 클라우드에 연결

이 섹션에서는 모바일 응용 프로그램에 SensorTile.box를 연결 하 여 모바일 응용 프로그램을 클라우드에 연결 하는 방법을 알아봅니다.

1. 왼쪽된 메뉴를 사용 하 여 선택 합니다 **클라우드 로깅** 단추입니다.

    ![클라우드 로깅](media/howto-connect-sensortile/cloud-logging.png)

1. 선택 **Azure IoT Central** 클라우드 공급자로 합니다.
1. 장치 ID 및 앞에서 언급 된 범위 ID를 삽입 합니다.

    ![자격 증명](media/howto-connect-sensortile/credentials.png)

1. 선택 된 **응용 프로그램 키** 라디오 단추입니다.
1. 클릭 **Connect** 업로드 하려는 원격 분석 데이터를 선택 합니다.
1. 몇 초 후 데이터는 IoT Central 응용 프로그램 대시보드에 나타납니다.

## <a name="sensortilebox-device-template-details"></a>SensorTile.box 장치 템플릿 세부 정보

다음 특성을 사용 하 여 SensorTile.box 장치 템플릿에서 만든 응용 프로그램:

### <a name="telemetry"></a>원격 분석

| 필드 이름     | Units  | 최소 | 최대 | 소수 자릿수 |
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

제안 된 다음 단계에 알아보려면는 이제는 SensorTile.box Azure IoT Central 응용 프로그램에 연결 하는 방법을 배웠으므로 [사용자 지정 장치 템플릿을 설정 하는 방법을](howto-set-up-template.md) 사용자 고유의 IoT 장치에 대 한 합니다.
