---
title: IoT DevKit에서 클라우드로 -- IoT DevKit AZ3166을 원격 모니터링 IoT 솔루션 가속기에 연결 | Microsoft Docs
description: 이 자습서에서는 모니터링 및 시각화를 위해 IoT DevKit AZ3166에서 센서의 상태를 원격 모니터링 IoT 솔루션 가속기로 보내는 방법을 알아봅니다.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 32742b2a680370f443051e2d86f90d94e8632850
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720585"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>MXChip IoT DevKit AZ3166을 IoT 원격 모니터링 솔루션 가속기에 연결

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

IoT DevKit에서 샘플 앱을 실행하여 센서 데이터를 솔루션 가속기에 보내는 방법에 대해 알아봅니다.

[MXChip IoT DevKit](https://aka.ms/iot-devkit)은 풍부한 주변 장치 및 센서가 포함된 올인원 Arduino 호환 보드입니다. 이는 Visual Studio Code에서 [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench)를 사용하여 개발할 수 있습니다. 그리고 Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션의 프로토타입을 안내하기 위해 확장 중인 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)가 제공됩니다.

## <a name="what-you-need"></a>필요한 항목

[시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)를 살펴본 후에 **다음 섹션만 수행합니다**.

* 하드웨어 준비
* Wi-Fi 구성
* DevKit를 사용하여 시작
* 개발 환경 준비

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>VS Code에서 원격 모니터링 샘플 열기

1. IoT DevKit가 컴퓨터에 연결되어 있지 **않은지** 확인합니다. VS Code를 시작하고 DevKit를 컴퓨터에 연결합니다.

2. `F1` 키를 클릭하여 명령 팔레트를 연 다음, **IoT Workbench: 예제**를 입력하고 선택합니다. 보드로 **IoT DevKit**를 선택합니다.

3. **원격 모니터링**을 찾아 **예제 열기**를 클릭합니다. 프로젝트 폴더가 있는 새 VS Code 창이 열립니다.
  ![IoT Workbench에서 원격 모니터링 예제 선택](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>IoT Hub 장치 연결 문자열 구성

1. IoT DevKit를 **구성 모드**로 전환합니다. 이렇게 하려면 다음을 수행합니다.
   * **A** 단추를 누르고 있습니다.
   * **다시 설정** 단추를 눌렀다가 놓습니다.

2. 화면에 DevKit ID와 '구성'이 표시됩니다.
   
  ![IoT DevKit 구성 모드](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. `F1` 키를 클릭하여 명령 팔레트를 연 다음, **IoT Workbench: 장치 > 장치 설정 구성**을 입력하고 선택합니다.

4. 방금 복사한 연결 문자열을 붙여넣으려면 `Enter` 키를 클릭하여 구성합니다.

## <a name="build-and-upload-the-device-code"></a>장치 코드 빌드 및 업로드

1. `F1` 키를 클릭하여 명령 팔레트를 연 다음, **IoT Workbench: 장치 > 장치 업로드**를 입력하고 선택합니다.
  ![IoT Workbench: 장치 -> 업로드](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. 그러면 VS Code에서 DevKit에 코드를 컴파일하고 업로드하기 시작합니다.
  ![IoT Workbench: 장치 -> 업로드됨](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

DevKit는 다시 부팅하고 코드를 실행하기 시작합니다.

## <a name="test-the-project"></a>프로젝트 테스트

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>원격 모니터링 솔루션에 보낸 원격 분석 보기

샘플 앱이 실행되면 DevKit에서 Wi-Fi를 통해 센서 데이터를 원격 모니터링 솔루션에 보냅니다. 결과를 보려면 다음 단계를 수행합니다.

1. 솔루션 대시보드로 이동하여 **장치**를 클릭합니다.

2. 오른쪽 탭에서 장치 이름을 클릭하면 DevKit의 센서 상태를 실시간으로 볼 수 있습니다.
  ![Azure IoT Suite의 센서 데이터](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>C2D 메시지 보내기

원격 모니터링 솔루션을 사용하면 장치에서 원격 메서드를 호출할 수 있습니다. 예제 코드에서는 센서가 선택되면 **메서드** 섹션에서 볼 수 있는 세 개의 메서드를 게시합니다.

![IoT DevKit 메서드](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

"LedColor" 메서드를 사용하여 DevKit LED 중 하나의 색을 변경해 보겠습니다.

1. 장치 목록에서 장치 이름을 선택하고, **작업**을 클릭합니다.

  ![작업 만들기](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. 아래와 같이 [작업]을 구성하고 **적용**을 클릭합니다.
  * 선택 작업: **메서드 실행**
  * 메서드 이름: **LedColor**
  * 작업 이름: **ChangeLedColor**
  
  ![작업 설정](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

몇 초 후에 DevKit에서 RGB LED(아래의 A 단추)의 색이 변경됩니다.

![IoT DevKit 빨간색 LED](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>리소스 정리

자습서를 계속 진행하려는 경우 배포된 원격 모니터링 솔루션 가속기를 그대로 둡니다.

솔루션 가속기가 더 이상 필요하지 않은 경우 해당 가속기를 선택한 다음, [솔루션 삭제]를 클릭하여 [프로비전된 솔루션] 페이지에서 삭제합니다.

![솔루션 삭제](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 지원 채널을 사용하여 문의해 주세요.

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

DevKit 장치를 Azure IoT 원격 모니터션 솔루션 가속기에 연결하고 센서 데이터를 시각화하는 방법을 알아보았습니다. 다음 제안 단계는 다음과 같습니다.

* [Azure IoT 솔루션 가속기 개요](https://docs.microsoft.com/azure/iot-suite/)
* [UI 사용자 지정](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [Azure IoT Central 응용 프로그램에 IoT DevKit 연결](../iot-central/howto-connect-devkit.md)