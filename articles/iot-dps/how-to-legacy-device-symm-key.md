---
title: 대칭 키를 사용 하 여 레거시 장치 프로 비전-Azure IoT Hub 장치 프로 비전 서비스
description: 장치 프로 비전 서비스 (DPS) 인스턴스로 대칭 키를 사용 하 여 레거시 장치를 프로 비전 하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: c9beda9c271c755c9ea61498b24a9e40bde35a7e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975111"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>대칭 키를 사용 하 여 레거시 장치를 프로 비전 하는 방법

많은 레거시 장치에서 흔히 발생 하는 문제는 단일 정보로 구성 된 id가 있는 경우가 많습니다. 이 id 정보는 일반적으로 MAC 주소 또는 일련 번호입니다. 레거시 장치에는 장치를 안전 하 게 식별 하는 데 사용할 수 있는 인증서, TPM 또는 기타 보안 기능이 없을 수 있습니다. IoT hub에 대 한 장치 프로 비전 서비스는 대칭 키 증명을 포함 합니다. 대칭 키 증명을 사용 하 여 MAC 주소 또는 일련 번호와 같은 정보를 기준으로 장치를 식별할 수 있습니다.

[HSM (하드웨어 보안 모듈](concepts-security.md#hardware-security-module) ) 및 인증서를 쉽게 설치할 수 있는 경우 장치를 식별 하 고 프로 비전 하는 데 더 나은 방법이 될 수 있습니다. 이 방법을 사용 하면 모든 장치에 배포 된 코드의 업데이트를 무시할 수 있으며 장치 이미지에 비밀 키가 포함 되어 있지 않습니다.

이 문서에서는 HSM 또는 인증서가 실행 가능한 옵션인 것으로 가정 합니다. 그러나 장치 프로 비전 서비스를 사용 하 여 이러한 장치를 프로 비전 하는 장치 코드를 업데이트 하는 몇 가지 방법이 있다고 가정 합니다. 

또한이 문서에서는 마스터 그룹 키 또는 파생 된 장치 키에 대 한 무단 액세스를 방지 하기 위해 보안 환경에서 장치 업데이트가 이루어지는 것으로 가정 합니다.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-munkaállomást használó példát a [Több bérlős regisztráció](how-to-provision-multitenant.md) című cikkben talál.

> [!NOTE]
> 이 문서에 사용 된 샘플은 C로 작성 되었습니다. 사용할 수 있는 [ C# 장치 프로 비전 대칭 키 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) 도 있습니다. 이 샘플을 사용 하려면 [azure-iot-csharp-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) 리포지토리를 다운로드 하거나 복제 하 고 샘플 코드의 인라인 지침을 따르세요. 이 문서의 지침에 따라 포털을 사용 하 여 대칭 키 등록 그룹을 만들고이 샘플을 실행 하는 데 필요한 ID 범위 및 등록 그룹 기본 및 보조 키를 찾을 수 있습니다. 샘플을 사용 하 여 개별 등록를 만들 수도 있습니다.

## <a name="overview"></a>Áttekintés

해당 장치를 식별 하는 정보를 기반으로 각 장치에 대해 고유한 등록 ID가 정의 됩니다. 예를 들어, MAC 주소 또는 일련 번호입니다.

[대칭 키 증명](concepts-symmetric-key-attestation.md) 을 사용 하는 등록 그룹은 장치 프로 비전 서비스를 사용 하 여 생성 됩니다. 등록 그룹에는 그룹 마스터 키가 포함 됩니다. 각 고유 등록 ID를 해시 하 여 각 장치에 고유한 장치 키를 생성 하는 데 해당 마스터 키가 사용 됩니다. 장치는 해당 파생 된 장치 키를 고유한 등록 ID로 사용 하 여 장치 프로 비전 서비스를 증명 하 고 IoT hub에 할당 합니다.

이 문서에서 설명 하는 장치 코드는 [빠른 시작: 대칭 키를 사용 하 여 시뮬레이션 된 장치 프로 비전](quick-create-simulated-device-symm-key.md)과 동일한 패턴을 따릅니다. 이 코드는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 샘플을 사용 하 여 장치를 시뮬레이션 합니다. 시뮬레이션 된 장치는 빠른 시작에서 설명한 대로 개별 등록 대신 등록 그룹을 증명 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Azure Portal 빠른 시작 [을 사용 하 여 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md) 완료
* ' 워크 로드 [를 C++사용한 데스크톱 개발 '](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 작업을 사용 하는 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 이상
* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Ebben a szakaszban előkészítjük az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) build készítésére szolgáló fejlesztőkörnyezetet. 

SDK에는 시뮬레이션 된 장치에 대 한 샘플 코드가 포함 되어 있습니다. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

1. [Cmake 빌드 시스템](https://cmake.org/download/)을 다운로드 합니다.

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az Azure IoT C SDK GitHub-adattárat:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Ez a művelet várhatóan több percig is eltarthat.


3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
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

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 장치 프로 비전 서비스 인스턴스를 엽니다.

2. **등록 관리** 탭을 선택 하 고 페이지 위쪽에서 **등록 그룹 추가** 단추를 클릭 합니다. 

3. **등록 그룹 추가**에서 다음 정보를 입력 하 고 **저장** 단추를 클릭 합니다.

   - **그룹 이름**: **mylegacydevices**을 입력 합니다.

   - **증명 형식**: **대칭 키**를 선택 합니다.

   - **Kulcsok automatikus létrehozása**: Jelölje be ezt a jelölőnégyzetet.

   - **허브에 장치를 할당 하는 방법 선택**: 특정 허브에 할당할 수 있도록 **정적 구성을** 선택 합니다.

   - **이 그룹을 할당할 수 있는 IoT Hub 선택**: 허브 중 하나를 선택 합니다.

     ![대칭 키 증명에 대 한 등록 그룹 추가](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Ha mentette a regisztrációt, a rendszer létrehozza az **Elsődleges kulcsot** és a **Másodlagos kulcsot**, majd hozzáadja őket a regisztrációs bejegyzéshez. 대칭 키 등록 그룹은 *등록 그룹* 탭의 *그룹 이름* 열 아래에 **mylegacydevices** 로 표시 됩니다. 

    Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**. 이 키는 마스터 그룹 키입니다.


## <a name="choose-a-unique-registration-id-for-the-device"></a>장치에 대 한 고유 등록 ID를 선택 합니다.

각 장치를 식별 하려면 고유한 등록 ID를 정의 해야 합니다. 장치에서 MAC 주소, 일련 번호 또는 모든 고유 정보를 사용할 수 있습니다. 

이 예제에서는 등록 ID에 대해 다음 문자열을 형성 하는 MAC 주소와 일련 번호를 조합 하 여 사용 합니다.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

장치에 대 한 고유한 등록 ID를 만듭니다. 유효한 문자는 소문자 영숫자 및 대시 ('-')입니다.


## <a name="derive-a-device-key"></a>장치 키 파생 

장치 키를 생성 하려면 그룹 마스터 키를 사용 하 여 장치에 대 한 고유 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) 을 계산 하 고 그 결과를 Base64 형식으로 변환 합니다.

장치 코드에 그룹 마스터 키를 포함 하지 마세요.


#### <a name="linux-workstations"></a>Linux 워크스테이션

Linux 워크스테이션을 사용 하는 경우 다음 예제와 같이 openssl를 사용 하 여 파생 된 장치 키를 생성할 수 있습니다.

**키** 의 값을 앞에서 적어둔 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 등록 ID로 바꿉니다.

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

Windows 기반 워크스테이션을 사용 하는 경우 다음 예제와 같이 PowerShell을 사용 하 여 파생 된 장치 키를 생성할 수 있습니다.

**키** 의 값을 앞에서 적어둔 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 등록 ID로 바꿉니다.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


장치는 프로 비전 하는 동안 등록 그룹으로 대칭 키 증명을 수행 하기 위해 고유한 등록 ID로 파생 된 장치 키를 사용 합니다.



## <a name="create-a-device-image-to-provision"></a>프로 비전 할 장치 이미지 만들기

이 섹션에서는 이전에 설정한 Azure IoT C SDK에 있는 **prov\_dev\_client\_sample** 이라는 프로 비전 샘플을 업데이트 합니다. 

이 샘플 코드는 장치 프로 비전 서비스 인스턴스로 프로 비전 요청을 보내는 장치 부팅 시퀀스를 시뮬레이션 합니다. 부팅 시퀀스를 통해 장치를 인식 하 고 등록 그룹에 구성한 IoT hub에 할당 합니다.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio에서 CMake이를 실행 하 여 생성 된 **azure_iot_sdks .sln** 솔루션 파일을 엽니다. A megoldásfájlnak a következő helyen kell lennie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg róla, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, mint az alább is látható:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. **Prov\_dev\_client\_sample. c** 에서 `prov_dev_set_symmetric_key_info()`에 대 한 호출을 찾습니다.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    함수 호출의 주석 처리를 제거 하 고 자리 표시자 값 (꺾쇠 괄호 포함)을 장치의 고유한 등록 ID와 사용자가 생성 한 파생 된 장치 키로 바꿉니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Mentse a fájlt.

7. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

8. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

    Az alábbi példakimeneten látható, hogy a szimulált eszköz sikeresen elindul és csatlakozik a regisztrációs szolgáltatáspéldányhoz, hogy az hozzárendelhesse egy IoT-központhoz:

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

9. 포털에서 시뮬레이션 된 장치가 할당 된 IoT hub로 이동 하 고 **Iot 장치** 탭을 클릭 합니다. 허브에 시뮬레이션 된를 성공적으로 프로 비전 하면 장치 ID가 **IoT 장치** 블레이드에 표시 되 고 *상태가* **사용**으로 표시 됩니다. Lehet, hogy rá kell kattintania fent a **Frissítés** gombra. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>보안 문제

이것은 파생 된 장치 키를 이미지의 일부로 포함 하 고 있으므로 권장 되는 보안 모범 사례가 아닙니다. 이것은 보안과 사용 편의성이 절충 하는 한 가지 이유입니다. 





## <a name="next-steps"></a>Következő lépések

* 다시 프로 비전에 대 한 자세한 내용은 [IoT Hub Device 다시 프로 비전 개념](concepts-device-reprovision.md) 을 참조 하세요. 
* [빠른 시작: 대칭 키를 사용 하 여 시뮬레이트된 장치 프로 비전](quick-create-simulated-device-symm-key.md)
* 프로 비전 해제에 대 한 자세한 내용은 [이전에 자동 프로 비전 된 장치의 프로 비전](how-to-unprovision-devices.md) 을 해제 하는 방법 











