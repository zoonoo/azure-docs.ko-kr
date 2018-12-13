---
title: Azure IoT Central 애플리케이션에 Windows IoT Core 디바이스 연결 | Microsoft Docs
description: 디바이스 개발자로서 Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스를 연결하는 방법을 알아봅니다.
author: miriambrus
ms.author: miriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5be556ca13b9e4f681239998205aac073cc0fb99
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51974758"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 Windows IoT Core 디바이스 연결

이 문서에서는 장치 개발자로서 Windows IoT Core 장치를 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

1. **샘플 Devkits** 응용 프로그램 템플릿으로 만든 Azure IoT Central 응용 프로그램. 자세한 내용은 참조는 [애플리케이션 빠른 시작 만들기](quick-deploy-iot-central.md)를 참조하세요.
2. Windows 10 IoT Core 운영 체제를 실행하는 장치. 이 연습에서는 Raspberry Pi를 사용합니다.


## <a name="sample-devkits-application"></a>**샘플 Devkits** 응용 프로그램

**샘플 Devkits** 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 **Windows IoT Core** 장치가 포함됩니다. 

- 장치에 대한 측정값 **습도**, **온도** 및 **압력**을 포함하는 원격 분석입니다. 
- **팬 속도**를 보여주는 설정입니다.
- 디바이스 속성 **다이 번호** 및 **위치** 클라우드 속성을 포함하는 속성입니다.


장치 템플릿 구성에 대한 자세한 내용은 [Windows IoT Core 장치 템플릿 세부 정보](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details) 참조

## <a name="add-a-real-device"></a>실제 장치 추가

Azure IoT Central 애플리케이션에서 **Windows IoT Core** 디바이스 템플릿으로 실제 디바이스를 추가하고 디바이스 연결 문자열을 기록해 둡니다. 자세한 내용은 [Azure IoT Central 애플리케이션에 실제 디바이스 추가](tutorial-add-device.md)를 참조하세요.

### <a name="prepare-the-windows-iot-core-device"></a>Windows IoT Core 디바이스 준비

Windows IoT Core 디바이스를 설정하려면 [Windows IoT Core 디바이스 설정](https://github.com/Azure/iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device)의 단계별 지침을 따릅니다.

### <a name="add-a-real-device"></a>실제 장치 추가

Azure IoT Central 애플리케이션에서 **Windows IoT Core** 디바이스 템플릿으로 실제 디바이스를 추가하고 디바이스 연결 정보(**범위 ID, 디바이스 ID, 기본 키**)를 기록해 둡니다. 자세한 내용은 [Azure IoT Central 애플리케이션에 실제 디바이스 추가](tutorial-add-device.md)를 참조하세요.

 > [!NOTE]
   > Azure IoT Central은 모든 디바이스 연결에 Azure IoT Hub DPS(Device Provisioning Service)를 사용하도록 전환되었습니다. 다음 지침에 따라 [디바이스 연결 문자열을 가져오고](concepts-connectivity.md#getting-device-connection-string) 자습서의 나머지 부분을 계속합니다.

## <a name="prepare-the-windows-10-iot-core-device"></a>Windows 10 IoT Core 장치 준비

### <a name="what-youll-need"></a>필요한 항목

실제 Windows 10 IoT Core 디바이스를 설정하려면 Windows 10 IoT Core를 실행하는 디바이스가 필요합니다. Windows 10 IoT Core 디바이스를 설정하는 방법은 [여기](https://docs.microsoft.com/en-us/windows/iot-core/tutorials/quickstarter/devicesetup)서 알아볼 수 있습니다.

Azure IoT Central과 통신할 수 있는 클라이언트 응용 프로그램도 필요합니다. Azure SDK를 사용하여 자신만의 고유한 사용자 지정 애플리케이션을 빌드하고 Visual Studio를 사용하여 디바이스에 배포할 수도 있고, [미리 작성된 샘플](https://developer.microsoft.com/en-us/windows/iot/samples)을 다운로드하여 간단하게 디바이스에 배포하고 실행할 수도 있습니다. 

### <a name="deploying-the-sample-client-application"></a>샘플 클라이언트 응용 프로그램 배포

이전 단계의 클라이언트 애플리케이션을 Windows 10 IoT 디바이스에 배포하여 준비하려면:

**사용할 클라이언트 응용 프로그램에 대한 장치에 연결 문자열 저장**
* 데스크톱에서 connection.string.iothub라는 텍스트 파일에 연결 문자열을 저장합니다.
* 텍스트 파일을 디바이스의 문서 폴더(`[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`)에 복사합니다.

복사했으면 아무 브라우저에 http://[device-IP-address]:8080을 입력하여 [Windows 디바이스 포털](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)을 열어야 합니다.

이 포털에서 아래와 같이 다음 작업을 수행합니다.
1. 왼쪽에서 "앱" 노드를 확장합니다.
2. "빠른 실행 샘플"을 클릭합니다.
3. "Azure IoT Hub 클라이언트"를 클릭합니다.
4. "배포 및 실행"을 클릭합니다.

![Windows 디바이스 포털에서 Azure IoT Hub 클라이언트의 Gif](./media/howto-connect-windowsiotcore/iothubapp.gif)

성공하면 디바이스에서 애플리케이션이 시작되고 다음과 같이 표시됩니다.

![Azure IoT Hub 클라이언트 앱의 스크린샷](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

Azure IoT Central에서, Raspberry Pi에서 실행되는 코드가 응용 프로그램과 상호 작용하는 방식을 살펴볼 수 있습니다.

* 실제 디바이스의 **측정값** 페이지에서 원격 분석 데이터를 볼 수 있습니다.
* **속성** 페이지에서, 보고된 다이 번호 속성 값을 볼 수 있습니다.
* **설정** 페이지에서, 전압, 팬 속도 등 Raspberry Pi의 다양한 설정을 변경할 수 있습니다.

## <a name="download-the-source-code"></a>소스 코드 다운로드

클라이언트 응용 프로그램의 소스 코드를 살펴보고 수정하려면 [여기](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients) GitHub에서 소스 코드를 다운로드하면 됩니다. 코드를 수정하려는 경우 [여기](https://github.com/Microsoft/Windows-iotcore-samples)서 사용 중인 데스크톱 운영 체제의 readme 파일에 제공되는 지침을 따라야 합니다.

> [!NOTE]
> 개발 환경에 **git**이 설치되지 않은 경우 [https://git-scm.com/download](https://git-scm.com/download)에서 다운로드할 수 있습니다.

## <a name="windows-iot-core-device-template-details"></a>Windows IoT Core 장치 템플릿 세부 정보

**샘플 Devkits** 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 **Windows IoT Core** 장치가 포함됩니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

| 필드 이름     | Units  | 최소 | 최대 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>설정

숫자 설정

| 표시 이름 | 필드 이름 | Units | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>properties

| type            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 장치 속성 | 다이 번호   | dieNumber  | number    |
| 텍스트            | 위치     | location   | 해당 없음       |
