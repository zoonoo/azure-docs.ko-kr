---
title: "MXChip IoT DevKit를 사용하여 Azure IoT Hub Device Provisioning Service에 연결하는 방법 | Microsoft Docs"
description: "MXChip IoT DevKit를 사용하여 Azure IoT Hub Device Provisioning Service에 연결하는 방법을 설명합니다."
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>MXChip IoT DevKit를 Azure IoT Hub Device Provisioning Service에 연결

이 문서에서는 Device Provisioning Service를 사용하여 IoT Hub에 자동으로 등록되도록 DevKit를 구성하는 방법에 대해 설명합니다. 이 자습서에서는 다음 방법을 알아봅니다.

* 장치에서 DPS의 전역 엔드포인트 구성
* UDS(고유 장치 비밀)를 사용하여 X.509 인증서 생성
* 개별 장치 등록
* 장치가 등록되어 있는지 확인

[MXChip IoT DevKit](https://aka.ms/iot-devkit)은 풍부한 주변 장치 및 센서가 포함된 올인원 Arduino 호환 보드입니다. [Arduino용 Visual Studio Code 확장](https://aka.ms/arduino)을 사용하여 개발할 수 있습니다. 그리고 Microsoft Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션의 프로토타입을 안내하기 위해 확장 중인 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)가 제공됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 완료하려면 다음이 필요합니다.

* [시작 가이드](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)를 사용하여 DevKit를 준비합니다.
* [펌웨어 업그레이드](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) 자습서를 사용하여 최신 펌웨어(1.3.0 이상)로 업그레이드합니다.
* [자동 프로비전 설정](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision)을 사용하여 Device Provisioning Service 인스턴스가 있는 IoT Hub를 만들고 연결합니다.

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>장치에서 Device Provisioning Service 구성 설정

DevKit에서 만든 Device Provisioning Service 인스턴스에 연결하려면 다음을 수행합니다.

1. Azure Portal에서 Device Provisioning Service에 대한 **개요**를 선택하고, **전역 장치 엔드포인트** 및 **ID 범위** 값을 적어 둡니다.
  ![DPS 전역 엔드포인트 및 ID 범위](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. 컴퓨터에 `git`이 설치되어 있고 명령 창에서 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 최신 버전을 설치하려면 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요.

3. 명령 프롬프트를 엽니다. DPS 샘플 코드용 GitHub 리포지토리를 복제합니다.
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. VS Code를 시작하고, DevKit를 컴퓨터에 연결하고, 복제한 코드가 포함된 폴더를 엽니다.

5. **DevKitDPS.ino**를 열고, `[Global Device Endpoint]` 및 `[ID Scope]`를 찾은 다음, 방금 적어 둔 값으로 바꿉니다.
  ![DPS 엔드포인트](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) **registrationId**는 비워 둘 수 있으며, 응용 프로그램에서 MAC 주소와 펌웨어 버전에 따라 하나를 생성합니다. 이를 사용자 지정하려면 등록 ID에서 최대 128자의 영숫자, 소문자 및 하이픈 조합을 사용해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 장치 등록 관리](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments)를 참조하세요.

6. VS Code(Windows: `Ctrl+P`, macOS: `Cmd+P`)에서 **Quick Open**을 사용하고, **task device-upload**를 입력하여 코드를 DevKit에 빌드하고 업로드합니다.

7. 출력 창에서 작업의 성공 여부를 관찰합니다.

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>STSAFE 보안 칩에 고유 장치 비밀 저장

Device Provisioning Service는 [HSM(하드웨어 보안 모듈)](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)에 따라 장치에 구성할 수 있습니다. DevKit는 [TCG(신뢰할 수 있는 컴퓨팅 그룹)](https://trustedcomputinggroup.org)의 [DICE(Device Identity Composition Engine)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf)를 사용합니다. DevKit의 STSAFE 보안 칩에 저장된 **UDS(고유 장치 비밀)**가 장치 고유의 [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) 인증서를 생성하는 데 사용됩니다. 이 인증서는 나중에 Device Provisioning Service의 등록 프로세스에 사용할 수 있습니다.

일반적인 **UDS(고유 장치 비밀)**는 64자 길이의 문자열입니다. UDS 샘플은 다음과 같습니다.

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

각각의 두 문자는 보안 계산에서 16진수 값으로 사용됩니다. 위의 UDS 샘플은 다음과 같이 해석됩니다. "`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`"

DevKit에 UDS를 저장하려면 다음을 수행합니다.

1. Putty와 같은 도구를 사용하여 직렬 모니터를 엽니다. 자세한 내용은 [구성 모드 사용](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)을 참조하세요.

2. DevKit를 컴퓨터에 연결한 상태에서 A 단추를 누른 채 다시 설정 단추를 눌렀다 놓으면 구성 모드로 들어갑니다. 화면에 DevKit ID 및 **'Configuration'**(구성)이 표시됩니다.

3. 위의 긴 UDS 샘플 문자열을 가져와서 하나 이상의 문자를 `0`에서 `f` 사이의 다른 값으로 변경합니다. 이 문자열을 사용자 고유의 UDS로 사용됩니다.

4. 직렬 모니터 창에서 **set_dps_uds [your_own_uds_value]**를 입력하고 Enter 키를 눌러 저장합니다.
  > [!NOTE]
  > 예를 들어 마지막 두 문자를 `f`로 변경하여 사용자 고유의 UDS를 설정한 경우 **set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff**와 같은 명령을 입력해야 합니다.

5. 직렬 모니터 창을 닫지 않고 DevKit의 다시 설정 단추를 누릅니다.

6. **DevKit MAC 주소** 및 **DevKit 펌웨어 버전** 값을 적어 둡니다.
  ![펌웨어 버전](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>X.509 인증서 생성

### <a name="windows"></a>Windows

1. 파일 탐색기를 열고 복제한 DSP 샘플 코드가 포함된 폴더로 이동합니다. **.build** 폴더가 있으며 **DPS.ino.bin** 및 **DPS.ino.map**을 찾고 복사합니다.
  ![생성된 파일](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Arduino에 대한 `built.path` 구성을 다른 폴더로 변경한 경우 구성한 폴더에서 해당 파일을 찾아야 합니다.

2. 이 두 파일을 **.build** 폴더가 있는 동일한 수준의 **tools** 폴더에 붙여넣습니다.

3. **dps_cert_gen.exe**를 실행하고, 프롬프트에 따라 DevKit에 대한 **UDS**, **MAC 주소** 및 **펌웨어 버전**을 입력하여 X.509 인증서를 생성합니다.
  ![dps-cert-gen.exe 실행](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. 생성이 성공적으로 관찰되면 **.pem** 인증서가 동일한 폴더에 저장됩니다.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Device Provisioning Service에서 장치 등록 항목 만들기

1. Azure Portal에서 프로비전 서비스로 이동합니다. **등록 관리**를 클릭하고 **개별 등록** 탭을 선택합니다. ![개별 등록](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. **추가**를 클릭합니다.

3. **메커니즘**에서 **X.509**를 선택합니다.
  ![인증서 업로드](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. **인증서 .pem 또는 .cer 파일**에 방금 가지고 있는 **.pem** 인증서를 업로드합니다.

5. 나머지 항목은 기본값으로 유지하고 **저장**을 클릭합니다.

## <a name="start-the-devkit"></a>DevKit 시작

1. VS Code를 시작하고 직렬 모니터를 엽니다.

2. DevKit의 **다시 설정** 단추를 누릅니다.

DevKit에서 Device Provisioning Service에 등록하는 것을 시작해야 합니다.

![VS Code 출력](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>DevKit가 IoT Hub에 등록되어 있는지 확인

장치가 부팅되면 다음 작업을 수행해야 합니다.

1. 장치에서 등록 요청을 Device Provisioning Service에 보냅니다.
2. Device Provisioning Service에서 장치가 응답하는 등록 챌린지를 다시 보냅니다.
3. 등록에 성공하면 Device Provisioning Service는 IoT Hub URI, 장치 ID 및 암호화된 키를 장치로 다시 보냅니다.
4. 그러면 장치의 IoT Hub 클라이언트 응용 프로그램이 사용자 허브에 연결됩니다.
5. 허브에 성공적으로 연결되면 IoT Hub의 Device Explorer에 장치가 표시됩니다.
  ![등록된 장치](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>장치 ID 변경

Azure IoT Hub에 등록된 기본 장치 ID는 **AZ3166**입니다. 수정하려면 [여기의 지침](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)을 따릅니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 채널을 통해 문의해주세요.

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

DICE를 사용하여 DevKit에서 장치를 DPS에 안전하게 등록하도록 준비하는 방법을 알아보았으므로 이제 장치를 IoT Hub에 아무런 작업 없이 자동으로 등록할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 장치에서 DPS의 전역 엔드포인트 구성
> * UDS(고유 장치 비밀)를 사용하여 X.509 인증서 생성
> * 개별 장치 등록
> * 장치가 등록되어 있는지 확인

자세히 알아보려면 다른 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [시뮬레이션된 장치 만들기 및 프로비전](./quick-create-simulated-device.md)

