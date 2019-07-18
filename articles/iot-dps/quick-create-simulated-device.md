---
title: C를 사용하여 Azure IoT Hub에 시뮬레이션된 TPM 디바이스 프로비전 | Microsoft Docs
description: 이 빠른 시작에서는 개별 등록을 사용합니다. 이 빠른 시작에서는 Azure IoT Hub Device Provisioning Service의 C# 디바이스 SDK를 사용하여 시뮬레이션된 TPM 디바이스를 만들고 프로비전합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ca6914967d855123c70bf746a9d68d2e045e76d9
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65908680"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>빠른 시작: Azure IoT C SDK를 사용하여 시뮬레이션된 TPM 디바이스 프로비전

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

이 빠른 시작에서는 Windows 개발 머신에서 TPM(신뢰할 수 있는 플랫폼 모듈) 디바이스 시뮬레이터를 만들고 실행하는 방법을 알아봅니다. Device Provisioning Service 인스턴스를 사용하여 이 시뮬레이션된 디바이스를 IoT Hub에 연결합니다. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 샘플 코드를 사용하여 Device Provisioning Service 인스턴스에 디바이스를 등록하고 디바이스에 대한 부팅 시퀀스를 시뮬레이션할 수 있습니다.

자동 프로비전 프로세스에 익숙하지 않은 경우 [자동 프로비전 개념](concepts-auto-provisioning.md)을 검토하세요. 이 빠른 시작을 계속하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다. 

Azure IoT Device Provisioning 서비스는 다음과 같은 두 가지 등록을 지원합니다.
- [등록 그룹](concepts-service.md#enrollment-group): 여러 관련 디바이스를 등록하는 데 사용됩니다.
- [개별 등록](concepts-service.md#individual-enrollment): 단일 디바이스를 등록하는 데 사용됩니다.

이 문서에서는 개별 등록을 설명합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* ['C++를 사용한 데스크톱 개발'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드를 사용하도록 설정한 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 이상.
* 최신 버전의 [Git](https://git-scm.com/download/) 설치


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Azure IoT C SDK에 대한 개발 환경 준비

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 및 [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) 디바이스 시뮬레이터 샘플을 빌드하는 데 사용되는 개발 환경을 준비합니다.

1. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    `CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다.

2. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 복제합니다.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    이 작업을 완료하는 데 몇 분 정도가 걸립니다.


3. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>SDK를 빌드하고 TPM 디바이스 시뮬레이터를 실행합니다.

이 섹션에서는 TPM 디바이스 시뮬레이터 샘플 코드를 포함하는 Azure IoT C SDK를 빌드합니다. 이 샘플에서는 SAS(공유 액세스 서명) 토큰 인증을 통해 TPM [증명 메커니즘](concepts-security.md#attestation-mechanism)을 제공합니다.

1. azure-iot-sdk-c git 리포지토리에서 만든 `cmake` 하위 디렉터리에서 다음 명령을 실행하여 샘플을 빌드합니다. 또한 시뮬레이션된 디바이스에 대한 Visual Studio 솔루션도 이 빌드 명령에 의해 생성됩니다.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    `cmake`에서 C++ 컴파일러를 찾지 못하면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다. 

    빌드가 성공되면 마지막 몇몇 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. 복제한 Git 리포지토리의 루트 폴더로 이동하고 아래 표시된 경로를 사용하여 [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) 시뮬레이터를 실행합니다. 이 시뮬레이터는 포트 2321 및 2322에서 소켓을 수신 대기합니다. 이 명령 창을 닫지 마세요. 이 빠른 시작이 끝날 때까지 이 시뮬레이터가 실행되는 상태를 유지해야 합니다. 

   *cmake* 폴더에 있으면 다음 명령을 실행합니다.

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    시뮬레이터의 출력이 표시되지 않습니다. 계속해서 TPM 디바이스의 시뮬레이션을 실행합니다.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>TPM 디바이스의 암호화 키 읽기

이 섹션에서는 포트 2321 및 2322에서 실행하고 수신 대기해 둔 TPM 시뮬레이터의 인증 키 및 등록 ID를 읽을 샘플을 빌드하고 실행합니다. 이러한 값은 Device Provisioning Service 인스턴스를 사용하는 디바이스 등록에 사용됩니다.

1. Visual Studio를 시작하고 `azure_iot_sdks.sln`이라는 새 솔루션 파일을 엽니다. 이 솔루션 파일은 azure-iot-sdk-c git 리포지토리의 루트에서 이전에 만든 `cmake` 폴더에 위치합니다.

2. Visual Studio 메뉴에서 **빌드** > **빌드 솔루션**을 선택하여 솔루션의 모든 프로젝트를 빌드합니다.

3. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Tools** 폴더로 이동합니다. **tpm_device_provision** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 선택합니다. 

4. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작**을 선택하여 솔루션을 실행합니다. 앱은 **_등록 ID_** 및 **_인증 키_** 를 읽고 표시합니다. 이러한 값을 복사합니다. 해당 값은 디바이스 등록을 위해 다음 섹션에서 사용됩니다. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>포털에서 디바이스 등록 항목 만들기

1. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, Device Provisioning Service를 엽니다.

2. **등록 관리** 탭을 선택한 다음, 위쪽에서 **개별 등록 추가** 단추를 클릭합니다. 

3. **등록 추가**에서 다음 정보를 입력하고 **저장** 단추를 클릭합니다.

    - **메커니즘:** ID 증명 *메커니즘*으로 **TPM**을 선택합니다.
    - **인증 키:** *tpm_device_provision* 프로젝트를 실행하여 TPM 디바이스에 생성된 ‘인증 키’를 입력합니다. 
    - **등록 ID:** *tpm_device_provision* 프로젝트를 실행하여 TPM 디바이스에 생성된 ‘등록 ID’를 입력합니다. 
    - **IoT Edge 디바이스:** **사용 안 함**을 선택합니다.
    - **IoT Hub 디바이스 ID:** **test-docs-device**를 입력하여 디바이스에 ID를 제공합니다.

      ![포털에 디바이스 등록 정보 입력](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      등록에 성공하면 디바이스의 *등록 ID*가 *개별 등록* 탭 아래 목록에 나타납니다. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>디바이스에 대한 첫 번째 부팅 시퀀스 시뮬레이션

이 섹션에서는 샘플 코드가 [AMQP(고급 메시지 큐 프로토콜)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol)를 사용하도록 구성하여 디바이스의 부팅 시퀀스를 Device Provisioning Service 인스턴스에 보냅니다. 이 부팅 시퀀스를 사용하면 디바이스를 인식하고 Device Provisioning Service 인스턴스에 연결된 IoT Hub에 할당합니다.

1. Azure Portal에서 Device Provisioning 서비스에 대한 **개요** 탭을 선택하고 **_ID 범위_** 값을 복사합니다.

    ![포털에서 디바이스 프로비저닝 서비스 엔드포인트 정보 추출](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Samples** 폴더로 이동합니다. **prov\_dev\_client\_sample**이라는 샘플 프로젝트를 확장합니다. **원본 파일**을 확장하고, **prov\_dev\_client\_sample.c**를 엽니다.

3. 아래에 표시된 대로 파일의 맨 위쪽 가까이에 있는 각 디바이스 프로토콜에 대한 `#define` 문을 찾습니다. `SAMPLE_AMQP`의 주석 처리를 제거했는지 확인합니다.

    현재 [MQTT 프로토콜은 TPM 개별 등록에 지원되지 않습니다](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. `id_scope` 상수를 찾고, 값을 앞에서 복사한 **ID 범위** 값으로 바꿉니다. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 동일한 파일에서 `main()` 함수에 대한 정의를 찾습니다. 아래와 같이 `hsm_type` 변수가 `SECURE_DEVICE_TYPE_X509` 대신 `SECURE_DEVICE_TYPE_TPM`으로 설정되었는지 확인합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 

7. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작**을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 프롬프트에서 **예**를 클릭하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 프로비전 디바이스 클라이언트 샘플을 성공적으로 부팅하고, IoT Hub 정보를 가져오기 위해 Device Provisioning Service 인스턴스에 연결하고, 등록하는 예제입니다.

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. 시뮬레이션된 디바이스를 프로비전 서비스별로 IoT Hub에 프로비전하면 디바이스 ID가 허브의 **IoT 디바이스**에 표시됩니다. 

    ![디바이스가 IoT Hub에 등록됨](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>리소스 정리

디바이스 클라이언트 샘플을 계속해서 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 디바이스 클라이언트 샘플 출력 창을 닫습니다.
2. 컴퓨터에서 TPM 시뮬레이터 창을 닫습니다.
3. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 사용자의 Device Provisioning Service를 선택합니다. 서비스에 대한 **등록 관리**를 연 다음, **개별 등록** 탭을 클릭합니다. 이 빠른 시작에서 등록한 디바이스의 *등록 ID*를 선택하고, 위쪽의 **삭제** 단추를 클릭합니다. 
4. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. 허브에 대한 **IoT 디바이스**를 열고, 이 빠른 시작에서 등록한 디바이스의 *디바이스 ID*를 선택한 다음, 위쪽의 **삭제** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 시뮬레이션된 TPM 디바이스를 머신에 만들고, IoT Hub Device Provisioning Service를 사용하여 IoT Hub에 이 디바이스를 프로비전했습니다. 프로그래밍 방식으로 TPM 디바이스를 등록하는 방법을 알아보려면 프로그래밍 방식으로 TPM 디바이스를 등록하는 빠른 시작으로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service에 TPM 디바이스 등록](quick-enroll-device-tpm-java.md)

