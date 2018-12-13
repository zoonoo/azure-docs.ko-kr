---
title: 원격 모니터링 솔루션 가속기에 IoT DevKit 연결 - Azure | Microsoft Docs
description: 이 방법 가이드에서는 모니터링 및 시각화를 위해 IoT DevKit AZ3166 디바이스의 센서에서 원격 모니터링 솔루션 가속기로 원격 분석을 보내는 방법을 알아봅니다.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 7f67868f6220ab2940aa8ac4d4bf24f82191cc22
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620254"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기에 IoT DevKit 디바이스 연결

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 방법 가이드에서는 IoT DevKit 디바이스에서 샘플 애플리케이션을 실행하는 방법을 보여 줍니다. 샘플 코드는 DevKit 디바이스의 센서에서 솔루션 가속기로 원격 분석을 보냅니다.

[IoT DevKit](https://aka.ms/iot-devkit)는 풍부한 주변 장치 및 센서가 포함된 올인원 Arduino 호환 보드입니다. 이는 Visual Studio Code에서 [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench)를 사용하여 개발할 수 있습니다. [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)에는 IoT 솔루션을 프로토타입하는 데 도움이 되는 샘플 애플리케이션이 포함되어 있습니다.

## <a name="prerequisites"></a>필수 조건

[IoT DevKet 시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)를 따르고 다음 섹션만 완료합니다.

* 하드웨어 준비
* Wi-Fi 구성
* DevKit를 사용하여 시작
* 개발 환경 준비

## <a name="open-the-sample"></a>샘플 열기

VS Code에서 원격 모니터링 샘플을 열려면

1. IoT DevKit가 컴퓨터에 연결되어 있지 않은지 확인합니다. VS Code를 시작하고 DevKit를 컴퓨터에 연결합니다.

1. `F1` 키를 클릭하여 명령 팔레트를 연 다음, **IoT Workbench: 예제**를 입력하고 선택합니다. 보드로 **IoT DevKit**를 선택합니다.

1. **원격 모니터링**을 찾아 **예제 열기**를 클릭합니다. 프로젝트 폴더를 표시하는 새 VS Code 창이 열립니다.

  ![IoT Workbench에서 원격 모니터링 예제 선택](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>디바이스 구성

DevKit 디바이스에서 IoT Hub 디바이스 연결 문자열을 구성하려면

1. IoT DevKit를 **구성 모드**로 전환합니다.

    * **A** 단추를 누르고 있습니다.
    * **다시 설정** 단추를 눌렀다가 놓습니다.

1. 화면에 DevKit ID 및 `Configuration`이 표시됩니다.

    ![IoT DevKit 구성 모드](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. **F1** 키를 눌러 명령 팔레트를 연 다음, **IoT Workbench: 디바이스 > 디바이스 설정 구성**을 입력하고 선택합니다.

1. 이전에 복사한 연결 문자열을 붙여넣고 **Enter** 키를 눌러 디바이스를 구성합니다.

## <a name="build-the-code"></a>코드 빌드

디바이스 코드를 빌드하고 업로드하려면

1. **F1** 키를 눌러 명령 팔레트를 연 다음, **IoT Workbench: 디바이스 > 디바이스 업로드**를 입력하고 선택합니다.

    ![IoT Workbench: 디바이스 -> 업로드](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code가 코드를 컴파일하고 DevKit 디바이스로 업로드합니다.

    ![IoT Workbench: 디바이스 -> 업로드됨](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. DevKit 디바이스가 다시 부팅되고 업로드한 코드를 실행합니다.

## <a name="test-the-sample"></a>샘플 테스트

DevKit 디바이스에 업로드한 샘플 애플리케이션이 작동하는지 확인하려면 다음 단계를 완료합니다.

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>원격 모니터링 솔루션에 보낸 원격 분석 보기

샘플 앱이 실행되면 DevKit 디바이스가 Wi-Fi를 통해 해당 센서 데이터에서 솔루션 가속기로 원격 분석을 보냅니다. 원격 분석을 확인하려면

1. 솔루션 대시보드로 이동하여 **디바이스**를 클릭합니다.

1. DevKit 디바이스의 디바이스 이름을 클릭합니다. 오른쪽 탭에서 DevKit의 원격 분석을 실시간으로 확인할 수 있습니다.

    ![Azure IoT Suite의 센서 데이터](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>DevKit 디바이스 제어

원격 모니터링 솔루션 가속기를 사용하면 디바이스를 원격으로 제어할 수 있습니다. 샘플 코드는 **디바이스** 페이지에서 디바이스를 선택할 때 **메서드** 섹션에서 확인할 수 있는 세 가지 메서드를 구현합니다.

![IoT DevKit 메서드](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

DevKit LED 중 하나의 색을 변경하려면 **LedColor** 메서드를 사용합니다.

1. 디바이스 목록에서 디바이스 이름을 선택하고 **작업**을 클릭합니다.

    ![작업 만들기](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. 다음 값을 사용하여 작업을 구성하고 **적용**을 클릭합니다.

    * 선택 작업: **메서드 실행**
    * 메서드 이름: **LedColor**
    * 작업 이름: **ChangeLedColor**

    ![작업 설정](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. 몇 초 후에 DevKit의 RGB LED 색(단추 A 아래)이 변경됩니다.

    ![IoT DevKit 빨간색 LED](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>리소스 정리

자습서를 계속 진행하려는 경우 배포된 원격 모니터링 솔루션 가속기를 그대로 둡니다.

솔루션 가속기가 더 이상 필요하지 않은 경우 해당 가속기를 선택한 다음, [솔루션 삭제]를 클릭하여 [프로비전된 솔루션] 페이지에서 삭제합니다.

![솔루션 삭제](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나, 다음 지원 채널을 사용하여 문의하세요.

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

지금까지 원격 모니터링 솔루션 가속기에 DevKit 디바이스를 연결하는 방법을 알아보았으며, 다음 몇 가지 제안 단계도 확인하는 것이 좋습니다.

* [Azure IoT 솔루션 가속기 개요](https://docs.microsoft.com/azure/iot-accelerators/)
* [UI 사용자 지정](iot-accelerators-remote-monitoring-customize.md)
* [Azure IoT Central 응용 프로그램에 IoT DevKit 연결](../iot-central/howto-connect-devkit.md)