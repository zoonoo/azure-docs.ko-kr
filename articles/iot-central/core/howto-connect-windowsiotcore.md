---
title: Azure IoT Central 응용 프로그램에 Windows IoT Core 연결 | Microsoft Docs
description: 디바이스 개발자로서 Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스를 연결하는 방법을 알아봅니다.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 452d18908406214bb7e1253363a42d8ba8287d96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454016"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 Windows IoT Core 디바이스 연결

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

이 문서에서는 디바이스 개발자로서 Windows IoT Core 디바이스를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

- **레거시 응용** 프로그램 응용 프로그램 템플릿에서 만든 Azure IoT Central 응용 프로그램입니다. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.

- Windows 10 IoT Core 운영 체제를 실행하는 디바이스. 자세한 내용은 [Windows 10 IoT Core 장치 설정](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)을 참조 하세요.

- [Node.js](https://nodejs.org/) 버전 8.0.0 이상이 설치 된 개발 컴퓨터. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. Node.js는 다양한 운영 체제에 사용할 수 있습니다.

## <a name="add-a-device-template"></a>디바이스 템플릿 추가

Azure IoT Central 응용 프로그램에서 다음 특성을 사용 하 여 새 **Windows IoT Core** 장치 템플릿을 추가 합니다.

- 장치에 대 한 원격 분석 측정: **습도**, **온도**및 **압력**.
- **팬 속도**를 제어 하도록 설정 합니다.
- 장치 속성 **주사위 번호** 및 클라우드 속성 **위치**입니다.

1. 장치 템플릿 ![장치 템플릿에서 **+ 새로 만들기** 를 선택](media/howto-connect-windowsiotcore/adddevicetemplate.png)
   

2. **Windows Iot core** 를 선택 하 고 Windows iot core 장치 템플릿을 만들어 장치 템플릿 추가 ![](media/howto-connect-windowsiotcore/newdevicetemplate.png)

장치 템플릿 구성에 대 한 자세한 내용은 [Windows IoT Core 장치 템플릿 정보](#device-template-details)를 참조 하세요.

## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 응용 프로그램에서 **Device Explorer** 페이지를 사용 하 여 **Windows 10 IoT Core** 장치 템플릿에서 실제 장치를 추가 합니다. 장치 연결 정보 (**범위 ID**, **장치 ID**및 **기본 키**)를 기록해 둡니다.

## <a name="prepare-the-device"></a>장치 준비

장치에서 IoT Central에 연결 하려면 연결 문자열이 필요 합니다.

1. 다음과 같이 `dps-keygen` 명령줄 유틸리티를 사용하여 연결 문자열을 생성합니다.

    [키 생성기 유틸리티](https://github.com/Azure/dps-keygen)를 설치하려면 다음 명령을 실행합니다.

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 연결 문자열을 생성하려면 앞에서 적어 놓은 연결 세부 정보를 사용하여 다음 명령을 실행합니다.

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. 디바이스 코드에 사용할 연결 문자열을 `dps-keygen` 출력에서 복사합니다.

장치 코드에서 연결 문자열에 액세스 하려면 Windows 10 IoT Core 장치에서 `C:\Data\Users\DefaultAccount\Documents\` 폴더의 **iothub** 이라는 파일에 저장 합니다.

데스크톱 컴퓨터에서 장치의 `C:\Data\Users\DefaultAccount\Documents\` 폴더로 **iothub** 파일을 복사 하려면 [Windows 장치 포털](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)을 사용 하면 됩니다.

1. 웹 브라우저를 사용 하 여 장치에서 Windows 장치 포털로 이동 합니다.
1. 장치에서 파일을 찾아보려면 **앱 > 파일 탐색기**를 선택 합니다.
1. **사용자 Folders\Documents**로 이동 합니다. 그런 다음 **iothub** 파일을 업로드 합니다.

    ![연결 문자열 업로드](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>배포 및 실행

장치에서 샘플 응용 프로그램을 배포 하 고 실행 하려면 [Windows 장치 포털](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)을 사용 하면 됩니다.

1. 웹 브라우저를 사용 하 여 장치에서 Windows 장치 포털로 이동 합니다.
1. **Azure IoT Hub 클라이언트** 응용 프로그램을 배포 하 고 실행 하려면 **앱 > 빠른 실행 샘플**을 선택 합니다. 그런 다음 **Azure IoT Hub 클라이언트**를 선택 합니다.
1. 그런 다음 **배포 및 실행**을 선택 합니다.

    ![배포 및 실행](media/howto-connect-windowsiotcore/quick-run.png)

몇 분 후에 IoT Central 응용 프로그램에서 장치에 대 한 원격 분석을 볼 수 있습니다.

[Windows 장치 포털](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) 에는 장치 문제를 해결 하는 데 사용할 수 있는 도구가 포함 되어 있습니다.

- **앱 관리자** 페이지를 사용 하 여 장치에서 실행 되는 앱을 제어할 수 있습니다.
- 장치에 연결 된 모니터가 없는 경우 장치 **설정** 페이지를 사용 하 여 장치에서 스크린샷을 캡처할 수 있습니다. 예:

    ![앱 스크린샷](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>소스 코드 다운로드

클라이언트 응용 프로그램에 대 한 소스 코드를 탐색 하 고 수정 하려면 [Windows-iotcore-샘플 GitHub 리포지토리에서](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients)다운로드할 수 있습니다.

## <a name="device-template-details"></a>장치 템플릿 정보

**샘플 Devkits** 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 **Windows IoT Core** 디바이스가 포함됩니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

| 필드 이름     | 단위  | 최소 | 최대 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>설정

숫자 설정

| 표시 이름 | 필드 이름 | 단위 | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>속성

| 유형            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 디바이스 속성 | 다이 번호   | dieNumber  | number    |
| 텍스트            | 위치     | 위치   | N/A       |

## <a name="next-steps"></a>다음 단계

이제 Windows IoT Core 장치를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 고유한 IoT 장치에 대 한 [사용자 지정 장치 템플릿을 설정](howto-set-up-template.md) 하는 방법을 배우는 것입니다.
