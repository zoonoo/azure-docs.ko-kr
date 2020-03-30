---
title: Azure IoT Central 애플리케이션에 DevKit 디바이스 연결 | Microsoft Docs
description: 장치 개발자는 IoT 플러그 앤 플레이(미리 보기)를 사용하여 MXChip IoT DevKit 장치를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아봅니다.
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 0a393ae8629f1742002344ee717a6719269a6722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158611"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스 연결

이 문서에서는 MXChip IoT DevKit(DevKit) 장치를 Azure IoT Central 응용 프로그램에 연결하는 방법을 보여 주었습니다. 이 장치는 DevKit 장치에 대해 인증된 IoT 플러그 앤 플레이(미리 보기) 모델을 사용하여 IoT Central에 대한 연결을 구성합니다.

이 방법 문서에서는 다음과 같은 작업을 수행합니다.

- IoT 중앙 애플리케이션에서 연결 세부 정보를 가져옵니다.
- 장치를 준비하고 IoT 중앙 응용 프로그램에 연결합니다.
- IoT Central의 장치에서 원격 분석 및 속성을 봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 리소스가 필요합니다.

- [데브킷 장치](https://aka.ms/iot-devkit-purchase).
- IoT 중앙 응용 프로그램입니다. [IoT 중앙 응용 프로그램 만들기의](./quick-deploy-iot-central.md)단계를 따를 수 있습니다.

## <a name="get-device-connection-details"></a>장치 연결 세부 정보 가져오기

1. Azure IoT 중앙 응용 프로그램에서 장치 템플릿 탭을 선택하고 **+ 새**을 **선택합니다.** 섹션에서 **미리 구성된 장치 템플릿을**사용하여 **MXChip IoT DevKit를**선택합니다.

    ![MXChip IoT 데브킷용 장치 템플릿](media/howto-connect-devkit/device-template.png)

1. **다음 을 선택합니다: 사용자 지정한** 다음 **만들기.**

1. **장치** 탭을 선택합니다. 장치 목록에서 **MXChip IoT DevKit을** 선택하고 **+ 새** 장치를 선택하여 템플릿에서 새 장치를 만듭니다.

    ![새 장치](media/howto-connect-devkit/new-device.png)

1. 팝업 창에서 **장치 ID를** 다음과 `SampleDevKit` 같이 입력하고 `MXChip IoT DevKit - Sample`장치 **이름을** 로 입력합니다. **시뮬레이션** 옵션이 꺼져 있는지 확인합니다. 그런 다음 **을 선택합니다.**

    ![장치 ID 및 이름](media/howto-connect-devkit/device-id-name.png)

1. 만든 장치를 선택한 다음 **연결을**선택합니다. **ID 범위,** **장치 ID**및 **기본 키를**기록합니다. 이 방법 문서의 나중에 이러한 값이 필요합니다.

    ![장치 연결 정보](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>장치 준비

1. GitHub에서 DevKit 장치에 대한 [사전 빌드된 Azure IoT 중앙 플러그 앤 플레이(미리 보기) 펌웨어를](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) 다운로드합니다.

1. USB 케이블을 사용하여 DevKit 디바이스를 개발 컴퓨터에 연결합니다. Windows에서, DevKit 디바이스의 스토리지에 매핑된 드라이브에서 파일 탐색기 창이 열립니다. 예를 들어 드라이브 이름이 **AZ3166 (D:)** 일 수 있습니다.

1. **iotc_devkit.bin** 파일을 드라이브 창으로 끕습니다. 복사가 완료되면 새 펌웨어를 사용하여 디바이스가 다시 부팅됩니다.

    > [!NOTE]
    > **Wi-Fi 없음과**같은 화면 오류가 표시되는 경우 DevKit이 아직 WiFi에 연결되지 않았기 때문입니다.

1. DevKit에서 **버튼 B를**길게 누를 때 **리셋** 버튼을 누르고 놓은 다음 **단추 B를**놓습니다. 이제 장치가 액세스 포인트 모드에 있습니다. 확인하려면 화면에 "IoT DevKit - AP"와 구성 포털 IP 주소가 표시됩니다.

1. 컴퓨터 또는 태블릿에서 장치 화면에 표시된 WiFi 네트워크 이름에 연결합니다. WiFi 네트워크는 **AZ-다음에** MAC 주소로 시작합니다. 이 네트워크에 연결하면 인터넷에 액세스할 수 없습니다. 이 상태는 예상되며 장치를 구성하는 동안 잠시 동안만 이 네트워크에 연결합니다.

1. 웹 브라우저를 열고 [http://192.168.0.1/](http://192.168.0.1/)로 이동합니다. 다음 웹 페이지가 표시됩니다.

    ![구성 UI](media/howto-connect-devkit/config-ui.png)

    웹 페이지에서 다음을 입력합니다.

    - 와이파이 네트워크(SSID)의 이름입니다.
    - WiFi 네트워크 암호입니다.
    - 연결 세부 정보: 이전에 기록한 **장치 ID,** **ID 범위**및 **SAS 기본 키를** 입력합니다.

    > [!NOTE]
    > 현재 IoT DevKit은 2.4GHz Wi-Fi에만 연결할 수 있으며 하드웨어 제한으로 인해 5GHz가 지원되지 않습니다.

1. **장치 구성을**선택합니다. DevKit 장치가 응용 프로그램을 재부팅하고 실행합니다.

    ![재부팅 UI](media/howto-connect-devkit/reboot-ui.png)

    DevKit 화면에는 응용 프로그램이 실행 중이라는 확인이 표시됩니다.

    ![데브킷 실행 중](media/howto-connect-devkit/devkit-running.png)

DevKit먼저 IoT 중앙 응용 프로그램에 새 장치를 등록 한 다음 데이터 전송을 시작합니다.

## <a name="view-the-telemetry"></a>원격 분석 보기

이 단계에서는 Azure IoT Central 응용 프로그램에서 원격 분석을 봅니다.

IoT 중앙 응용 프로그램에서 **장치** 탭을 선택하고 추가한 장치를 선택합니다. **개요** 탭에서 DevKit 장치에서 원격 분석을 볼 수 있습니다.

![IoT 중앙 장치 개요](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>코드 검토

코드를 검토하거나 수정하고 컴파일하려면 코드 [샘플로 이동하십시오.](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)

## <a name="next-steps"></a>다음 단계

이제 DevKit 장치를 Azure IoT Central 응용 프로그램에 연결하는 방법을 배웠으니 다음 단계는 사용자 지정 IoT 장치에 대한 [사용자 지정 장치 템플릿을 설정하는](./howto-set-up-template.md) 방법을 알아보는 것입니다.
