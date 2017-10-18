---
title: "Azure 방법 - Azure에서 Device Provisioning Service 클라이언트 SDK와 함께 다른 하드웨어 보안 모델을 사용하는 방법 | Microsoft Docs"
description: "Azure 방법 - Azure에서 Device Provisioning Service 클라이언트 SDK와 함께 다른 하드웨어 보안 모델을 사용하는 방법"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 620d86b62cf43c3e1a5f7f5c724fcf00174f30e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>Device Provisioning Service 클라이언트 SDK에서 다른 HSM을 사용하는 방법
이 단계에서는 실제 장치 및 시뮬레이터를 사용하여 C의 Device Provisioning Service 클라이언트 SDK에서 다른 [HSM(하드웨어 보안 모듈)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)을 사용하는 방법을 보여 줍니다.  프로비전 서비스는 X**.**509 및 TPM(신뢰할 수 있는 플랫폼 모듈)의 두 가지 인증 모드를 지원합니다.

## <a name="prerequisites"></a>필수 조건

[시뮬레이션된 장치 만들기 및 프로비전](./quick-create-simulated-device.md) 가이드의 "개발 환경 준비" 섹션에 따라 개발 환경을 준비합니다.

## <a name="enable-authentication-with-different-hsms"></a>다른 HSM으로 인증 사용

Azure Portal에 인증 모드(X**.**509 또는 TPM)를 등록하려면 먼저 실제 장치 또는 시뮬레이터를 사용하도록 설정해야 합니다.  azure-iot-sdk-c에 대한 루트 폴더로 이동합니다.  선택한 인증 모드에 따라 지정된 명령을 실행합니다.

### <a name="use-x509-with-simulator"></a>시뮬레이터를 통해 X**.**509 사용

프로비전 서비스는 장치 인증을 위한 X**.**509 인증서를 생성하는 DICE(Device Identity Composition Engine) 에뮬레이터와 함께 제공됩니다.  다음 명령을 실행하여 X**.**509 인증을 사용하도록 설정합니다.

```
cmake -Ddps_auth_type=x509 ..
```

DICE가 포함된 하드웨어에 대한 자세한 내용은 [여기](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)에 있습니다.

### <a name="use-x509-with-hardware"></a>하드웨어를 통해 X**.**509 사용

프로비전 서비스는 다른 하드웨어에서 X**.**509를 통해 사용할 수 있습니다.  하드웨어와 SDK 간의 인터페이스는 연결을 설정하는 데 필요합니다.  인터페이스에 대한 정보는 HSM 제조업체에 문의하세요.

### <a name="use-tpm"></a>TPM 사용

프로비전 서비스에서 SAS 토큰을 사용하여 Windows 및 Linux 하드웨어 TPM 칩에 연결할 수 있습니다.  다음 명령을 실행하여 TPM 인증을 사용하도록 설정합니다.

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>시뮬레이터를 통해 TPM 사용

TPM 칩이 있는 장치가 없으면 개발 용도로 Windows OS에서 시뮬레이터를 사용할 수 있습니다.  다음 명령을 실행하여 TPM 인증을 사용하도록 설정하고 TPM 시뮬레이터를 실행합니다.

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>SDK 빌드
먼저 SDK를 빌드한 후에 장치 등록을 만들어야 합니다.

### <a name="linux"></a>Linux
- Linux에서 SDK를 빌드하려면 다음을 수행합니다.
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- 디버그 이진 파일을 빌드하려면 해당 CMake 옵션을 프로젝트 생성 명령에 추가합니다. 예를 들면 다음과 같습니다.
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- SDK를 빌드하는 데 사용할 수 있는 [CMake 구성 옵션](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html)이 많이 있습니다. 예를 들어 CMake 프로젝트 생성 명령에 인수를 추가하여 사용 가능한 프로토콜 스택 중 하나를 해제할 수 있습니다.
    ```
    cmake -Duse_amqp=OFF ..
    ```
- 단위 테스트를 빌드하여 실행할 수도 있습니다.
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Windows에서 SDK를 빌드하려면 다음 단계를 수행하여 프로젝트 파일을 생성합니다.
    - "VS2015용 개발자 명령 프롬프트"를 엽니다.
    - 리포지토리의 루트에서 다음의 CMake 명령을 실행합니다.
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    이 명령은 x86 라이브러리를 빌드합니다. x64 라이브러리로 빌드하려면 cmake 생성기 인수를 다음과 같이 수정합니다. 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- 프로젝트가 성공적으로 생성되면 `cmake` 폴더 아래에 Visual Studio 솔루션 파일(.sln)이 표시됩니다. SDK를 빌드하려면 다음을 수행합니다.
    - Visual Studio에서 **cmake\azure_iot_sdks.sln**을 열고 빌드합니다. **또는**
    - 프로젝트 파일을 생성하는 데 사용된 명령 프롬프트에서 다음 명령을 실행합니다.
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- 디버그 이진 파일을 빌드하려면 해당 MSBuild 인수를 사용합니다. 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- SDK를 빌드하는 데 사용할 수 있는 CMake 구성 옵션이 많이 있습니다. 예를 들어 CMake 프로젝트 생성 명령에 인수를 추가하여 사용 가능한 프로토콜 스택 중 하나를 해제할 수 있습니다.
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- 단위 테스트를 빌드하여 실행할 수도 있습니다.
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>포함할 라이브러리
- 다음 라이브러리는 SDK에 포함되어야 합니다.
    - 프로비전 서비스: dps_http_transport, dps_client, dps_security_client
    - IoTHub 보안: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-dps"></a>DPS에 장치 등록 항목 만들기

### <a name="tpm"></a>TPM
TPM을 사용하는 경우 ["Azure IoT Hub Device Provisioning Service를 사용하여 시뮬레이션된 장치 만들기 및 프로비전"](./quick-create-simulated-device.md)의 지침에 따라 DPS에 장치 등록 항목을 만들고 첫 번째 부팅을 시뮬레이션합니다.

### <a name="x509"></a>X**.**509
1. 프로비전 서비스에 장치를 등록하려면 클라이언트 SDK에서 제공하는 프로비전 도구에 표시된 각 장치에 대한 인증 키와 등록 ID를 기록해 둡니다. 다음 명령을 실행하여 루트 CA 인증서(그룹 등록의 경우) 및 서명자 인증서(개별 등록의 경우)를 출력합니다.
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, DPS 서비스를 엽니다.
   - X**.**509 개별 등록: 프로비전 서비스 요약 블레이드에서 **등록 관리**를 선택합니다. **개별 등록** 탭을 선택하고 맨 위에서 **추가** 단추를 클릭합니다. ID 증명 *메커니즘*으로 **X**.**509**를 선택하고, 블레이드에서 요구하는 대로 서명자 인증서를 업로드합니다. 완료되면 **저장** 단추를 클릭합니다. 
   - X**.**509 그룹 등록: 프로비전 서비스 요약 블레이드에서 **등록 관리**를 선택합니다. **그룹 등록** 탭을 선택하고, 위쪽에 있는 **추가** 단추를 클릭합니다. ID 증명 *메커니즘*으로 **X**.**509**를 선택하고, 그룹 이름과 인증 이름을 입력하고, 블레이드에서 요구하는 대로 루트 CA 인증서를 업로드합니다. 완료되면 **저장** 단추를 클릭합니다. 

## <a name="connecting-to-iot-hub-after-provisioning"></a>프로비전 후 IoT Hub에 연결

장치가 프로비전 서비스를 통해 프로비전되면 다음 API에서 HSM 인증 모드를 사용하여 IoT Hub에 연결합니다. 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
