---
title: 대칭 키를 사용하여 Azure IoT Hub Device Provisioning Service로 레거시 장치를 프로비전하는 방법 | Microsoft Docs
description: 대칭 키를 사용하여 장치 프로비저닝 서비스 인스턴스로 레거시 장치를 프로비전하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 51fea4fa1973fbe92242f1995d892cd5b038a29b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991643"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>대칭 키를 사용하여 레거시 장치를 프로비전하는 방법


많은 레거시 장치의 일반적인 문제는 종종 단일 정보 부분으로 구성된 ID가 있다는 것입니다. 이 ID 정보는 일반적으로 MAC 주소 또는 일련 번호입니다. 레거시 장치에는 장치를 안전하게 식별하는 데 사용할 수 있는 인증서, TPM 또는 다른 보안 기능이 없을 수 있습니다. IoT 허브에 대한 장치 프로비저닝 서비스는 대칭 키 증명을 포함합니다. MAC 주소 또는 일련 번호와 같은 정보 기반 장치를 식별하는 데 대칭 키 증명을 사용할 수 있습니다.

[HSM(하드웨어 보안 모듈)](concepts-security.md#hardware-security-module) 및 인증서를 쉽게 설치할 수 있는 경우 장치를 식별하고 프로비전하는 더 나은 방법이 될 수 있습니다. 해당 방법을 통해 모든 장치에 배포되는 코드를 업데이트하지 않을 수 있으므로 장치 이미지에 포함된 비밀 키가 없습니다.

이 문서에서는 HSM 또는 인증서가 모두 실행 가능한 옵션이 아니라고 가정합니다. 그러나 이러한 장치를 프로비전하는 장치 프로비저닝 서비스를 사용하기 위해 장치 코드를 업데이트하는 몇 가지 방법이 있다고 가정합니다. 

이 문서는 또한 마스터 그룹 키 또는 파생된 장치 키에 대한 무단 액세스를 방지하기 위해 보안 환경에서 장치 업데이트가 수행된다고 가정합니다.

이 문서는 Windows 기반 워크스테이션에 적용됩니다. 그러나 Linux에서 절차를 수행할 수 있습니다. Linux 예제는 [다중 테넌트를 지원하기 위해 장치를 프로비전하는 방법](how-to-provision-multitenant.md)을 참조하세요.


## <a name="overview"></a>개요

해당 장치를 식별하는 정보에 따라 각 장치에 대해 고유한 등록 ID가 정의됩니다. 예를 들어 MAC 주소 또는 일련 번호입니다.

[대칭 키 증명](concepts-symmetric-key-attestation.md)을 사용하는 등록 그룹은 장치 프로비저닝 서비스를 사용하여 만들어집니다. 등록 그룹은 그룹 마스터 키를 포함합니다. 각 장치에 대한 고유한 장치 키를 생성하기 위해 각 고유 등록 ID를 해시하는 데 마스터 키가 사용됩니다. 장치는 해당 고유한 등록 ID로 파생된 장치 키를 사용하여 장치 프로비저닝 서비스로 증명되고 IoT 허브에 할당됩니다.

이 문서에 설명된 장치 코드는 [빠른 시작: 대칭 키를 사용하여 시뮬레이션된 장치 프로비전](quick-create-simulated-device-symm-key.md)과 동일한 패턴을 따릅니다. 코드에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 샘플을 사용하여 장치를 시뮬레이션합니다. 시뮬레이션된 장치는 빠른 시작에서 설명된 것처럼 개별 등록 대신 등록 그룹으로 증명됩니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>필수 조건

* [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md) 빠른 시작을 완료해야 합니다.
* ['C++를 사용한 데스크톱 개발'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드가 활성화된 Visual Studio 2015 또는 [Visual Studio 2017](https://www.visualstudio.com/vs/)
* 최신 버전의 [Git](https://git-scm.com/download/) 설치


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK 개발 환경 준비

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용되는 개발 환경을 준비합니다. 

SDK에는 시뮬레이트된 장치의 샘플 코드가 포함되어 있습니다. 이 시뮬레이트된 장치는 장치의 부팅 시퀀스 중에 프로비저닝을 시도합니다.

1. [CMake 빌드 시스템](https://cmake.org/download/)의 버전 3.11.4를 다운로드합니다. 해당하는 암호화 해시 값을 사용하여 다운로드된 이진 파일을 확인합니다. 다음 예제에서는 Windows PowerShell을 사용하여 x64 MSI 배포의 3.11.4 버전에 대한 암호화 해시를 확인했습니다.

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    3.11.4 버전에 대한 다음 해시 값이 작성 시 CMake 사이트에 나열되었습니다.

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    `CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다.

2. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 Azure IoT C SDK GitHub 리포지토리를 복제합니다.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    이 리포지토리 크기는 현재 약 220MB입니다. 이 작업을 완료하는 데 몇 분 정도가 걸립니다.


3. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. 개발 클라이언트 플랫폼에 관련된 SDK 버전을 빌드하는 다음 명령을 실행합니다. 또한 시뮬레이션된 장치에 대한 Visual Studio 솔루션이 `cmake` 디렉터리에서 생성됩니다. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    `cmake`에서 C++ 컴파일러를 찾지 못하면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다. 

    빌드가 성공되면 마지막 몇몇 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>대칭 키 등록 그룹 만들기

1. [Azure Portal](http://portal.azure.com)에 로그인하고 Device Provisioning Service 인스턴스를 엽니다.

2. **등록 관리** 탭을 선택한 다음, 페이지 위쪽에 있는 **등록 그룹 추가** 단추를 클릭합니다. 

3. **등록 그룹 추가**에서 다음 정보를 입력하고 **저장** 단추를 클릭합니다.

    - **그룹 이름**: **mylegacydevices**를 입력합니다.

    - **증명 형식**: **대칭 키**를 선택합니다.

    - **키 자동 생성**: 이 상자를 선택합니다.

    - **허브에 장치를 할당할 방법 선택**: 특정 허브에 할당할 수 있도록 **정적 구성**을 선택합니다.

    - **이 그룹을 할당할 IoT 허브 선택**: 사용자의 허브 중 하나를 선택합니다.

    ![대칭 키 증명에 대한 등록 그룹 추가](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. 등록을 저장하면 **기본 키** 및 **보조 키**가 생성되고 등록 항목에 추가됩니다. 대칭 키 등록 그룹이 *등록 그룹* 탭의 *그룹 이름* 열 아래에 **mylegacydevices**로 표시됩니다. 

    등록을 열고 생성된 **기본 키**의 값을 복사합니다. 이 키는 마스터 그룹 키입니다.


## <a name="choose-a-unique-registration-id-for-the-device"></a>장치에 대한 고유한 등록 ID를 선택합니다.

각 장치를 식별하기 위한 고유한 등록 ID가 정의되어야 합니다. 장치에서 MAC 주소, 일련 번호 또는 고유 정보를 사용할 수 있습니다. 

이 예제에서는 등록 ID에 대한 다음 문자열을 형성하는 MAC 주소와 일련 번호의 조합을 사용합니다.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

장치에 대한 고유한 등록 ID를 만듭니다. 유효한 문자는 소문자 영숫자 및 대시('-')입니다.


## <a name="derive-a-device-key"></a>장치 키 파생 

장치 키를 생성하려면 그룹 마스터 키를 사용하여 장치에 대한 고유한 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)을 계산하고 결과를 Base64 형식으로 변환합니다.

장치 코드에 그룹 마스터 키는 포함하지 않습니다.


#### <a name="linux-workstations"></a>Linux 워크스테이션

Linux 워크스테이션을 사용하는 경우 openssl을 사용하여 다음 예제에 표시된 대로 파생된 장치 키를 생성할 수 있습니다.

**KEY**의 값을 이전에 적어 둔 **기본 키**로 바꿉니다.

**REG_ID**의 값을 등록 ID로 바꿉니다.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Windows 기반 워크스테이션

Windows 기반 워크스테이션을 사용하는 경우 PowerShell을 사용하여 다음 예제에 표시된 대로 파생된 장치 키를 생성할 수 있습니다.

**KEY**의 값을 이전에 적어 둔 **기본 키**로 바꿉니다.

**REG_ID**의 값을 등록 ID로 바꿉니다.

```PowerShell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```PowerShell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


장치는 고유한 등록 ID로 파생된 장치 키를 사용하여 프로비전하는 동안 등록 그룹과의 대칭 키 증명을 수행합니다.



## <a name="create-a-device-image-to-provision"></a>프로비전할 장치 이미지 만들기

이 섹션에서는 이전에 설정한 Azure IoT C SDK에 있는 **prov\_dev\_client\_sample**이라는 프로비저닝 샘플을 업데이트합니다. 

이 샘플 코드는 프로비저닝 요청을 Device Provisioning Service 인스턴스에 보내는 장치 부팅 시퀀스를 시뮬레이트합니다. 부팅 시퀀스를 통해 장치가 인식되고 등록 그룹에서 구성한 IoT 허브에 할당됩니다.

1. Azure Portal에서 Device Provisioning 서비스에 대한 **개요** 탭을 선택하고 **_ID 범위_** 값을 기록해 둡니다.

    ![포털 블레이드에서 장치 프로비저닝 서비스 엔드포인트 정보 추출](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio에서 이전에 CMake를 실행하여 생성된 **azure_iot_sdks.sln** 솔루션 파일을 엽니다. 솔루션 파일은 다음 위치에 있습니다.

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Samples** 폴더로 이동합니다. **prov\_dev\_client\_sample**이라는 샘플 프로젝트를 확장합니다. **원본 파일**을 확장하고, **prov\_dev\_client\_sample.c**를 엽니다.

4. `id_scope` 상수를 찾고, 값을 앞에서 복사한 **ID 범위** 값으로 바꿉니다. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 동일한 파일에서 `main()` 함수에 대한 정의를 찾습니다. 아래와 같이 `hsm_type` 변수가 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`로 설정되었는지 확인합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 

7. Visual Studio의 *솔루션 탐색기* 창에서 **hsm\_security\_client** 프로젝트로 이동하고 프로젝트를 확장합니다. **원본 파일**을 확장하고 **hsm\_client\_key.c**를 엽니다. 

    `REGISTRATION_NAME` 및 `SYMMETRIC_KEY_VALUE` 상수의 선언을 찾습니다. 파일을 다음과 같이 변경한 후 저장합니다.

    `REGISTRATION_NAME` 상수의 값을 **장치의 고유 등록 ID**로 업데이트합니다.
    
    `SYMMETRIC_KEY_VALUE` 상수의 값을 **파생된 장치 키**로 업데이트합니다.

    ```c
    static const char* const REGISTRATION_NAME = "sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6";
    static const char* const SYMMETRIC_KEY_VALUE = "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=";
    ```

7. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작**을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 프롬프트에서 **예**를 클릭하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 시뮬레이션된 장치를 성공적으로 부팅하고, IoT 허브에 할당할 프로비저닝 서비스 인스턴스에 연결하는 예제입니다.

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

8. 포털에서 시뮬레이션된 장치가 할당된 IoT 허브로 이동하고 **IoT 장치** 탭을 클릭합니다. 시뮬레이션된 장치가 허브에 성공적으로 프로비전되면 *상태*가 **사용**인 장치 ID가 **IoT 장치** 블레이드에 표시됩니다. 위쪽에서 **새로 고침** 단추를 클릭해야 할 수 있습니다. 

    ![장치가 IoT Hub에 등록됨](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>보안에 대한 우려

이렇게 하면 파생된 장치 키를 이미지의 부분으로 포함된 채로 두므로 권장되는 보안 모범 사례가 아닙니다. 이것이 보안과 사용 편의성이 장단점인 이유 중 하나입니다. 





## <a name="next-steps"></a>다음 단계

* 다시 프로비전에 대한 자세한 내용은 [IoT Hub 장치 다시 프로비전 개념](concepts-device-reprovision.md)을 참조하세요. 
* [빠른 시작: 대칭 키를 사용하여 시뮬레이션된 장치 프로비전](quick-create-simulated-device-symm-key.md)
* 프로비전 해제에 대한 자세한 내용은 [이전에 자동 프로비전된 장치를 프로비전 해제하는 방법](how-to-unprovision-devices.md)을 참조하세요. 











