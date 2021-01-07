---
title: 빠른 시작 - Node.js에서 대칭 키를 사용하여 Azure IoT Hub에 디바이스 프로비저닝
description: 이 빠른 시작에서는 DPS(Device Provisioning Service)에서 Node.js용 Azure IoT SDK를 사용하여 대칭 키 디바이스를 IoT 허브에 프로비저닝합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 78005ba46952bcf05b19c7627feecb1ec30ac651
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92429262"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-nodejs"></a>빠른 시작: Node.js를 사용하여 대칭 키 디바이스 프로비저닝

이 빠른 시작에서는 Node.js를 사용하여 Windows 개발 컴퓨터를 디바이스로 IoT 허브에 프로비저닝하는 방법에 대해 알아봅니다. 이 디바이스는 IoT 허브에 할당하기 위해 대칭 키와 개별 등록을 사용하여 DPS(Device Provisioning Service) 인스턴스를 인증합니다. [Node.js용 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node.git)의 샘플 코드를 사용하여 디바이스를 프로비저닝합니다. 

이 문서에서는 개별 등록을 통한 프로비저닝을 보여 주지만 등록 그룹을 사용할 수도 있습니다. 등록 그룹을 사용할 때는 몇 가지 차이점이 있습니다. 예를 들어 디바이스에 대한 고유한 등록 ID가 있는 파생된 디바이스 키를 사용해야 합니다. [대칭 키를 사용하여 디바이스 프로비저닝](how-to-legacy-device-symm-key.md)에서는 등록 그룹 예제를 제공합니다. 등록 그룹에 대한 자세한 내용은 [대칭 키 증명에 대한 그룹 등록](concepts-symmetric-key-attestation.md#group-enrollments)을 참조하세요.

자동 프로비저닝 프로세스에 익숙하지 않은 경우 [프로비저닝](about-iot-dps.md#provisioning-process) 개요를 검토하세요. 

이 빠른 시작을 계속하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다. 이 빠른 시작에서는 Device Provisioning Service 인스턴스를 이미 만들었어야 합니다.

이 문서는 Windows 기반 워크스테이션에 적용됩니다. 그러나 Linux에서 절차를 수행할 수 있습니다. Linux 예제는 [다중 테넌트에 대한 프로비저닝](how-to-provision-multitenant.md)을 참조하세요.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>사전 요구 사항

- [프로비저닝](about-iot-dps.md#provisioning-process) 개념에 대해 잘 알아야 합니다.
- [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md) 완료
- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Node.js v4.0 이상](https://nodejs.org)
- [Git](https://git-scm.com/download/)


## <a name="create-a-device-enrollment"></a>디바이스 등록 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하여 왼쪽 메뉴에서 **모든 리소스** 단추를 선택하고, DPS(Device Provisioning Service) 인스턴스를 엽니다.

2. **등록 관리** 탭을 선택한 다음, 위쪽에서 **개별 등록 추가** 단추를 선택합니다. 

3. **등록 추가** 패널에서 다음 정보를 입력하고 **저장** 단추를 누릅니다.

   - **메커니즘:** ID 증명 *메커니즘* 으로 **대칭 키** 를 선택합니다.

   - **키 자동 생성** : 이 상자를 선택합니다.

   - **등록 ID** : 등록을 식별하는 등록 ID를 입력합니다. 소문자 영숫자 및 대시('-') 문자만을 사용합니다. 예를 들어 **symm-key-nodejs-device-01** 입니다.

   - **IoT Hub 디바이스 ID:** 디바이스 식별자를 입력합니다. 예를 들어 **nodejs-device-01** 입니다.

     ![포털에서 대칭 키 증명에 대한 개별 등록 추가](./media/quick-create-device-symmetric-key-node/create-individual-enrollment-node.png)

4. 등록을 저장하면 **기본 키** 및 **보조 키** 가 생성되고 등록 항목에 추가됩니다. 대칭 키 디바이스 등록이 *개별 등록* 탭의 *등록 ID* 열 아래에 **symm-key-nodejs-device-01** 로 표시됩니다. 

5. 등록을 열고 생성된 **기본 키** 의 값을 복사합니다. 디바이스 프로비저닝 샘플 코드와 함께 사용하기 위해 환경 변수를 추가할 때 이 키 값과 **등록 ID** 를 사용합니다.



## <a name="prepare-the-nodejs-environment"></a>Node.js 환경 준비 

1. Git CMD 또는 Git Bash 명령줄 환경을 엽니다. 다음 명령을 사용하여 [Node.js용 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node.git) GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```


<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>디바이스 프로비저닝 코드 준비

이 섹션에서는 대칭 키 디바이스를 프로비저닝하기 위해 디바이스 프로비저닝 샘플 코드에 대한 매개 변수로 사용되는 다음 4개의 환경 변수를 추가합니다. 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

프로비저닝 코드는 디바이스를 인증하기 위해 이러한 변수에 따라 DPS 인스턴스에 연결됩니다. 그러면 디바이스는 개별 등록 구성에 따라 DPS 인스턴스에 이미 연결된 IoT 허브에 할당됩니다. 프로비저닝되면 샘플 코드는 일부 테스트 원격 분석을 IoT 허브로 보냅니다.

1. [Azure Portal](https://portal.azure.com)의 디바이스 프로비저닝 서비스 메뉴에서 **개요** 를 선택하고 _서비스 엔드포인트_ 및 _ID 범위_ 를 복사합니다. `PROVISIONING_HOST` 및 `PROVISIONING_IDSCOPE` 환경 변수에 대해 이러한 값을 사용합니다.

    ![서비스 정보](./media/quick-create-device-symmetric-key-node/extract-dps-endpoints.png)

2. Node.js 명령을 실행하기 위한 명령 프롬프트를 열고, 다음 *provisioning/device/samples* 디렉터리로 이동합니다.

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

3. *provisioning/device/samples* 폴더에서 *register_symkey.js* 를 열고, 코드를 검토합니다. 

    샘플 코드에서는 사용자 지정 페이로드를 설정합니다.

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    이 빠른 시작에는 이 코드가 필요하지 않습니다. 사용자 지정 할당 함수를 사용하여 디바이스를 IoT Hub에 할당하려는 경우 이 코드는 사용자 지정 페이로드를 설정하는 방법에 대한 예제입니다. 자세한 내용은 [자습서: 사용자 지정 할당 정책 사용](tutorial-custom-allocation-policies.md)을 참조하세요.

    `provisioningClient.register()` 메서드에서 디바이스 등록을 시도합니다.

    디바이스를 등록하기 위해 샘플 코드를 변경할 필요가 없습니다.

4. 명령 프롬프트에서 이전 섹션의 개별 등록에서 복사한 프로비저닝 호스트, ID 범위, 등록 ID 및 기본 대칭 키에 대한 환경 변수를 추가합니다.  

    다음 명령은 명령 구문을 표시하는 예제입니다. 올바른 값을 사용해야 합니다.

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-nodejs-device-01
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```


4. 다음 명령을 사용하여 샘플 코드를 빌드하고 실행합니다.

    ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

5. 예상 출력은 개별 등록 설정에 따라 디바이스가 할당된 연결된 IoT 허브를 보여 주는 다음과 비슷합니다. "Hello World" 문자열이 테스트 메시지로 허브에 보내집니다.

    ```output
    D:\Docs\test\azure-iot-sdk-node\provisioning\device\samples>node register_symkey.js
    registration succeeded
    assigned hub=docs-test-iot-hub.azure-devices.net
    deviceId=nodejs-device-01
    payload=undefined
    Client connected
    send status: MessageEnqueued    
    ```
    
6. Azure Portal에서 프로비저닝 서비스와 연결된 IoT 허브로 이동하여 **IoT 디바이스** 블레이드를 엽니다. 허브에 대칭 키 디바이스를 성공적으로 프로비저닝한 후 디바이스 ID는 **사용** 으로 *상태* 와 함께 표시됩니다. 디바이스 샘플 코드를 실행하기 전에 블레이드가 이미 열려 있으면 위쪽의 **새로 고침** 단추를 눌러야 할 수도 있습니다. 

    ![디바이스가 IoT Hub에 등록됨](./media/quick-create-device-symmetric-key-node/hub-registration-node.png) 

> [!NOTE]
> 디바이스에 대한 등록 항목의 기본값으로부터 *초기 디바이스 쌍 상태* 를 변경한 경우, 허브에서 원하는 쌍 상태를 가져와서 그에 맞게 작동할 수 있습니다. 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.
>


## <a name="clean-up-resources"></a>리소스 정리

디바이스 클라이언트 샘플을 계속해서 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, Device Provisioning Service를 선택합니다. 서비스에 대한 **등록 관리** 를 연 다음, **개별 등록** 탭을 선택합니다. 이 빠른 시작에 등록한 디바이스의 *등록 ID* 옆에 있는 확인란을 선택하고, 창 위쪽에 있는 **삭제** 단추를 누릅니다. 
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 사용자의 IoT 허브를 선택합니다. 허브에 대한 **IoT 디바이스** 를 열고 이 빠른 시작에 등록한 디바이스의 *디바이스 ID* 옆에 있는 확인란을 선택한 다음, 창 위쪽에 있는 **삭제** 단추를 누릅니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT Hub Device Provisioning Service를 사용하여 Windows 기반 대칭 키 디바이스를 IoT 허브에 프로비저닝했습니다. Node.js를 사용하여 X.509 인증서 디바이스를 프로비저닝하는 방법을 알아보려면 아래의 X.509 디바이스에 대한 빠른 시작으로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [Azure 빠른 시작 - DPS 및 Node.js를 사용하여 X.509 디바이스 프로비저닝](quick-create-simulated-device-x509-node.md)
