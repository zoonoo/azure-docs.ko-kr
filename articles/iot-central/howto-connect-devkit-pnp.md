---
title: Azure IoT Central 애플리케이션에 DevKit 디바이스 연결 | Microsoft Docs
description: 장치 개발자는 IoT 플러그 앤 플레이를 사용 하 여 MXChip IoT DevKit 장치를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 알아봅니다.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: f3cfb892fc116640df5dd30abf857bde6e196224
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880112"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스 연결

이 문서에서는 DevKit (MXChip IoT DevKit) 장치를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 보여 줍니다. 장치는 DevKit 장치에 대해 인증 된 IoT 플러그 앤 플레이 모델을 사용 하 여 IoT Central에 대 한 연결을 구성 합니다.

이 방법 문서에서는 다음을 수행 합니다.

- IoT Central 응용 프로그램에서 연결 정보를 가져옵니다.
- 장치를 준비 하 고 IoT Central 응용 프로그램에 연결 합니다.
- IoT Central의 장치에서 원격 분석 및 속성을 확인 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료 하려면 다음 리소스가 필요 합니다.

1. [Devkit 장치](https://aka.ms/iot-devkit-purchase)입니다.
1. **Preview 응용 프로그램** 템플릿에서 만든 IoT Central 응용 프로그램입니다. [IoT 플러그 앤 플레이 응용 프로그램 만들기](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)의 단계를 따를 수 있습니다.

## <a name="get-device-connection-details"></a>장치 연결 정보 가져오기

Azure IoT Central 응용 프로그램에서 **관리** 탭을 선택 하 고 **장치 연결**을 선택 합니다. **범위 ID** 및 **기본 키**를 적어둡니다.

![장치 그룹 연결 세부 정보](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>장치 준비

1. GitHub에서 DevKit 장치에 대해 [미리 작성 된 최신 Azure IoT Central 플러그 앤 플레이 펌웨어](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) 를 다운로드 합니다.

1. USB 케이블을 사용하여 DevKit 디바이스를 개발 컴퓨터에 연결합니다. Windows에서, DevKit 디바이스의 스토리지에 매핑된 드라이브에서 파일 탐색기 창이 열립니다. 예를 들어 드라이브 이름이 **AZ3166 (D:)** 일 수 있습니다.

1. **Iotc_devkit** 파일을 드라이브 창으로 끌어 놓습니다. 복사가 완료되면 새 펌웨어를 사용하여 디바이스가 다시 부팅됩니다.

    > [!NOTE]
    > **Wi-fi가 없는**경우와 같이 화면에 오류가 표시 되 면 devkit가 아직 WiFi에 연결 되지 않았기 때문입니다.

1. DevKit에서 **단추 b**를 길게 누른 다음 **다시 설정** 단추를 눌렀다가 놓고 **단추 b**를 해제 합니다. 장치는 이제 액세스 포인트 모드입니다. 확인 하려면 화면에 "IoT DevKit-AP"와 구성 포털 IP 주소가 표시 됩니다.

1. 컴퓨터 또는 태블릿에서 장치 화면에 표시 된 WiFi 네트워크 이름에 연결 합니다. WiFi 네트워크는 **AZ-** 다음에 MAC 주소를 사용 하 여 시작 합니다. 이 네트워크에 연결 하면 인터넷에 액세스할 수 없습니다. 이 상태는 정상적인 상태 이며 장치를 구성 하는 동안 잠시 동안만이 네트워크에 연결 합니다.

1. 웹 브라우저를 열고 [http://192.168.0.1/](http://192.168.0.1/)로 이동합니다. 다음 웹 페이지가 표시됩니다.

    ![구성 UI](media/howto-connect-devkit-pnp/config-ui.png)

    웹 페이지에서 다음을 입력 합니다.

    - WiFi 네트워크 (SSID)의 이름입니다.
    - 사용자의 WiFi 네트워크 암호입니다.
    - 연결 세부 정보: 사용자가 선택할 수 있는 **장치 ID** 와 이전에 기록해 둔 **범위 ID** 및 **그룹 SAS 기본 키** 입니다.

    > [!NOTE]
    > 현재 IoT DevKit는 2.4 GHz Wi-fi에만 연결할 수 있으며, 하드웨어 제한으로 인해 5ghz는 지원 되지 않습니다.

1. **장치 구성**을 선택 하면 devkit 장치가 다시 부팅 되 고 응용 프로그램이 실행 됩니다.

    ![다시 부팅 UI](media/howto-connect-devkit-pnp/reboot-ui.png)

    DevKit 화면에 응용 프로그램이 실행 되 고 있음을 확인 하는 메시지가 표시 됩니다.

    ![DevKit 실행 중](media/howto-connect-devkit-pnp/devkit-running.png)

DevKit는 먼저 IoT Central 응용 프로그램에 새 장치를 등록 한 다음 데이터 보내기를 시작 합니다.

## <a name="view-the-telemetry"></a>원격 분석 보기

이 단계에서는 Azure IoT Central 응용 프로그램에서 원격 분석을 봅니다.

IoT Central 응용 프로그램에서 **장치** 탭을 선택 하 고 추가한 장치를 선택 합니다. **개요** 탭에서 devkit 장치의 원격 분석을 볼 수 있습니다.

   ![IoT Central 장치 개요](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>코드 검토

코드를 검토 하거나 수정 하 고 컴파일하려면 [MXChip IoT DevKit 샘플 코드 GitHub 리포지토리](https://github.com/MXCHIP/IoTDevKit/tree/master/pnp)로 이동 합니다.

## <a name="next-steps"></a>다음 단계

이제 DevKit 장치를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 고유한 IoT 장치에 대 한 [사용자 지정 장치 템플릿을 설정](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 하는 방법을 배우는 것입니다.
