---
title: Azure IoT Hub 장치 프로 비전 서비스에서 배포할지에에 대 한 장치를 프로 비전 하는 방법
description: DPS (장치 프로 비전 서비스) 인스턴스를 사용 하 여 배포할지에 용 장치를 프로 비전 하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5703db90307f679ff4728386dc24647437f9f9ba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974975"
---
# <a name="how-to-provision-for-multitenancy"></a>배포할지에에 프로 비전 하는 방법 

프로 비전 서비스에서 정의 된 할당 정책은 다양 한 할당 시나리오를 지원 합니다. 두 가지 일반적인 시나리오는 다음과 같습니다.

* **지리적 위치/GeoLatency**: 장치가 위치 간에 이동할 때 각 위치에 가장 가까운 IoT hub에 장치를 프로 비전 하 여 네트워크 대기 시간이 향상 됩니다. 이 시나리오에서는 지역에 걸쳐 있는 IoT hub 그룹이 등록에 대해 선택 됩니다. 이러한 등록에 대해 **가장 낮은 대기 시간** 할당 정책이 선택 됩니다. 이 정책을 통해 장치 프로 비전 서비스에서 장치 대기 시간을 평가 하 고 IoT hub 그룹에서 변기 IoT hub를 확인 합니다. 

* **다중 테 넌 트**: iot 솔루션 내에서 사용 되는 장치는 특정 iot Hub 또는 iot hub 그룹에 할당 되어야 할 수 있습니다. 솔루션은 특정 테 넌 트의 모든 장치가 특정 IoT hub 그룹과 통신 하도록 요구할 수 있습니다. 경우에 따라 테 넌 트는 IoT hub를 소유할 수 있으며 장치를 IoT hub에 할당 해야 합니다.

이러한 두 가지 시나리오를 결합 하는 것이 일반적입니다. 예를 들어 다중 테 넌 트 IoT 솔루션은 일반적으로 여러 지역에 분산 된 IoT hub 그룹을 사용 하 여 테 넌 트 장치를 할당 합니다. 이 테 넌 트 장치는 지리적 위치에 따라 대기 시간이 가장 짧은 해당 그룹의 IoT hub에 할당할 수 있습니다.

이 문서에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 의 시뮬레이션 된 장치 샘플을 사용 하 여 여러 지역에 걸쳐 다중 테 넌 트 시나리오에서 장치를 프로 비전 하는 방법을 보여 줍니다. 이 문서에서는 다음 단계를 수행 합니다.

* Azure CLI를 사용 하 여 두 개의 지역 IoT 허브 (**미국 서 부** 및 **미국 동부**)를 만듭니다.
* 다중 테 넌 트 등록 만들기
* Azure CLI를 사용 하 여 동일한 지역 (**미국 서 부** 및 **미국 동부**)에서 장치 역할을 하는 두 개의 지역 Linux vm을 만듭니다.
* 두 Linux Vm에서 Azure IoT C SDK에 대 한 개발 환경 설정
* 장치를 시뮬레이트하여 가장 가까운 지역의 동일한 테 넌 트에 대해 프로 비전 된 것을 확인 합니다.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Azure Portal 빠른 시작 [을 사용 하 여 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md) 완료


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>두 개의 지역 IoT hub 만들기

이 섹션에서는 Azure Cloud Shell를 사용 하 여 테 넌 트에 대 한 **미국 서 부** 및 **미국 동부** 지역에 두 개의 새 지역 IoT hub를 만듭니다.


1. Azure Cloud Shell를 사용 하 여 [az group create](/cli/azure/group#az-group-create) 명령을 사용 하 여 리소스 그룹을 만듭니다. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

    다음 예에서는 *e미국* 지역에 *contoso-미국-리소스* 그룹 이라는 리소스 그룹을 만듭니다. 이 문서에서 만든 모든 리소스에 대해이 그룹을 사용 하는 것이 좋습니다. 이렇게 하면 작업을 마친 후 더 쉽게 정리 됩니다.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Azure Cloud Shell를 사용 하 여 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 명령을 사용 하 여 **e Us** 지역에 iot hub를 만듭니다. IoT hub는 *contoso-미국-리소스 그룹*에 추가 됩니다.

    다음 예에서는 *에서는 eastus* 위치에 *contoso-동-hub* 라는 IoT hub를 만듭니다. **Contoso-동-허브**대신 고유한 허브 이름을 사용 해야 합니다.

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    이 명령을 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

3. Azure Cloud Shell를 사용 하 여 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 명령을 사용 하 여 **Westus** 지역에 iot hub를 만듭니다. 이 IoT hub는 *contoso-미국-리소스 그룹*에도 추가 됩니다.

    다음 예제에서는 *westus* 위치에 *contoso-동-hub* 라는 IoT hub를 만듭니다. **Contoso-동-허브**대신 고유한 허브 이름을 사용 해야 합니다.

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    이 명령을 완료 하는 데 몇 분 정도 걸릴 수 있습니다.



## <a name="create-the-multitenant-enrollment"></a>다중 테 넌 트 등록 만들기

이 섹션에서는 테 넌 트 장치에 대 한 새 등록 그룹을 만듭니다.  

간단히 하기 위해이 문서에서는 등록에 [대칭 키 증명](concepts-symmetric-key-attestation.md) 을 사용 합니다. 보다 안전한 솔루션을 위해서는 신뢰 체인에 [x.509 인증서 증명](concepts-security.md#x509-certificates) 을 사용 하는 것이 좋습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 장치 프로 비전 서비스 인스턴스를 엽니다.

2. **등록 관리** 탭을 선택 하 고 페이지 위쪽에서 **등록 그룹 추가** 단추를 클릭 합니다. 

3. **등록 그룹 추가**에서 다음 정보를 입력 하 고 **저장** 단추를 클릭 합니다.

    **그룹 이름**: **contoso-us-장치**를 입력 합니다.

    **증명 형식**: **대칭 키**를 선택 합니다.

    **키 자동 생성**:이 확인란은 이미 선택 되어 있어야 합니다.

    **허브에 장치를 할당 하는 방법을 선택**합니다. **대기 시간을 가장 적게**선택 합니다.

    ![대칭 키 증명에 대 한 다중 테 넌 트 등록 그룹 추가](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. **등록 그룹 추가**에서 **새 IoT hub 링크** 를 클릭 하 여 두 지역 허브를 연결 합니다.

    **구독**: 구독이 여러 개 있는 경우 지역 IoT 허브를 만든 구독을 선택 합니다.

    **IoT hub**: 만든 지역 허브 중 하나를 선택 합니다.

    **액세스 정책**: **iothubowner**를 선택 합니다.

    ![프로 비전 서비스와 지역 IoT hub 연결](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. 두 지역 IoT 허브가 연결 되 면 등록 그룹에 대해 선택 하 고 **저장** 을 클릭 하 여 등록을 위한 지역별 iot hub 그룹을 만듭니다.

    ![등록을 위한 지역 허브 그룹 만들기](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. 등록을 저장 한 후 다시 열고 **기본 키**를 적어둡니다. 키를 생성 하려면 먼저 등록을 저장 해야 합니다. 이 키는 나중에 두 시뮬레이션 된 장치에 대 한 고유한 장치 키를 생성 하는 데 사용 됩니다.


## <a name="create-regional-linux-vms"></a>지역 Linux Vm 만들기

이 섹션에서는 두 개의 지역 Linux Vm (가상 머신)을 만듭니다. 이러한 Vm은 각 지역에서 장치 시뮬레이션 샘플을 실행 하 여 두 지역의 테 넌 트 장치에 대 한 장치 프로 비전을 보여 줍니다.

좀 더 쉽게 정리 하기 위해 이러한 Vm은 만든 IoT hub를 포함 하는 동일한 리소스 *그룹에 추가 됩니다.* 그러나 Vm은 별도의 지역 (**미국 서 부** 및 **미국 동부**)에서 실행 됩니다.

1. Azure Cloud Shell에서 다음 명령을 실행 하 여 명령에서 다음 매개 변수를 변경한 후에 **미국 동부** 지역 VM을 만듭니다.

    **--name**: **미국 동부** 지역 장치 VM에 대 한 고유한 이름을 입력 합니다. 

    **--admin-username**: 사용자 고유의 관리자 이름을 사용 합니다.

    **--admin-password**: 사용자 고유의 관리자 암호를 사용 합니다.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    A parancs feldolgozása eltarthat néhány percig. 명령이 완료 되 면 미국 동부 지역 VM의 **publicIpAddress** 값을 기록해 둡니다.

1. 명령에서 다음 매개 변수를 변경 하 고 나면 Azure Cloud Shell에서 명령을 실행 하 여 **미국 서 부** 지역 VM을 만듭니다.

    **--name**: **미국 서 부** 지역 장치 VM에 대 한 고유한 이름을 입력 합니다. 

    **--admin-username**: 사용자 고유의 관리자 이름을 사용 합니다.

    **--admin-password**: 사용자 고유의 관리자 암호를 사용 합니다.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    A parancs feldolgozása eltarthat néhány percig. 명령이 완료 되 면 미국 서 부 지역 VM의 **publicIpAddress** 값을 기록해 둡니다.

1. 두 명령줄 셸을 엽니다. SSH를 사용 하 여 각 셸에서 지역 Vm 중 하나에 연결 합니다. 

    관리자 사용자 이름 및 VM에 대해 기록한 공용 IP 주소를 SSH에 매개 변수로 전달 합니다. 메시지가 표시 되 면 관리자 암호를 입력 합니다.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK 개발 환경 준비

이 섹션에서는 각 VM에서 Azure IoT C SDK를 복제 합니다. SDK에는 각 지역에서 테 넌 트의 장치 프로 비전을 시뮬레이트하는 샘플이 포함 되어 있습니다.


1. 각 VM에 대해 다음 명령을 사용 하 여 **cmake**, **g + +** , **gcc**및 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 를 설치 합니다.

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. 두 Vm에서 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 를 복제 합니다.

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. 두 Vm 모두 리포지토리 내에 새 **cmake** 폴더를 만들고 해당 폴더로 변경 합니다.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. 두 Vm 모두에 대해 개발 클라이언트 플랫폼과 관련 된 SDK 버전을 빌드하는 다음 명령을 실행 합니다. 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>고유한 장치 키 파생

등록 그룹에 대칭 키 증명을 사용 하는 경우 등록 그룹 키를 직접 사용 하지 않습니다. 대신 각 장치에 대해 고유한 파생 키를 만들고 [대칭 키가 있는 등록 그룹](concepts-symmetric-key-attestation.md#group-enrollments)에 언급 합니다.

장치 키를 생성 하려면 그룹 마스터 키를 사용 하 여 장치에 대 한 고유 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) 을 계산 하 고 그 결과를 Base64 형식으로 변환 합니다.

장치 코드에 그룹 마스터 키를 포함 하지 마세요.

Bash 셸 예제를 사용 하 여 **openssl**를 사용 하는 각 장치에 대해 파생 된 장치 키를 만듭니다.

- **키** 에 대 한 값을 이전에 등록을 위해 기록한 **기본 키** 로 바꿉니다.

- **REG_ID** 의 값을 각 장치에 고유한 등록 ID로 바꿉니다. 소문자 영숫자 및 대시 ('-') 문자를 사용 하 여 두 Id를 모두 정의 합니다.

Contoso의 장치 키 생성 예제 *-동부*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

Contoso의 장치 키 생성 예 *-서 부*:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


테 넌 트 장치는 각 테 넌 트 IoT hub에 프로 비전 하는 동안 해당 파생 된 장치 키 및 고유 등록 ID를 사용 하 여 등록 그룹으로 대칭 키 증명을 수행 합니다.




## <a name="simulate-the-devices-from-each-region"></a>각 지역에서 장치 시뮬레이션


이 섹션에서는 두 지역 Vm에 대 한 Azure IoT C SDK에서 프로 비전 샘플을 업데이트 합니다. 

샘플 코드는 장치 프로 비전 서비스 인스턴스로 프로 비전 요청을 보내는 장치 부팅 시퀀스를 시뮬레이션 합니다. 부팅 시퀀스를 사용 하면 장치가 인식 되 고 대기 시간에 따라 가장 가까운 IoT hub에 할당 됩니다.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. 두 Vm에서 편집 하려면 **/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample. c** 를 엽니다.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. `hsm_type` 변수가 등록 그룹 증명 방법과 일치 하도록 아래와 같이 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`으로 설정 되어 있는지 확인 합니다. 

    두 Vm에 있는 파일에 대 한 변경 내용을 저장 합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. 두 Vm에서 **prov\_dev\_client\_sample. c** 에서 `prov_dev_set_symmetric_key_info()`에 대 한 호출을 찾습니다.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    함수 호출의 주석 처리를 제거 하 고 자리 표시자 값 (꺾쇠 괄호 포함)을 각 장치에 대 한 고유한 등록 Id 및 파생 된 장치 키로 바꿉니다. 아래에 표시 된 키는 예를 들어 목적 으로만 사용 됩니다. 이전에 생성 한 키를 사용 합니다.

    미국 동부:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    미국 서 부:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    파일을 저장 합니다.

1. 두 Vm에서 아래에 표시 된 샘플 폴더로 이동 하 여 샘플을 빌드합니다.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. 빌드가 성공적으로 완료 되 면 **prov\_dev\_\_client** 를 실행 하 여 각 지역의 테 넌 트 장치를 시뮬레이션 합니다. 각 장치는 시뮬레이트된 장치 지역에 가장 가까운 테 넌 트 IoT hub에 할당 됩니다.

    시뮬레이션을 실행 합니다.
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    미국 동부 VM의 예제 출력:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    미국 서 부 VM의 예제 출력:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

이 문서에서 만든 리소스를 계속 사용 하려는 경우에는 그대로 둘 수 있습니다. 리소스를 계속 사용 하지 않으려는 경우 다음 단계를 사용 하 여이 문서에서 만든 모든 리소스를 삭제 하 여 불필요 한 요금을 방지 합니다.

여기에서 설명 하는 단계는이 문서에서 **모든 리소스를**만든 것으로 가정 합니다.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

이름으로 리소스 그룹을 삭제 하려면:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. **이름으로 필터링 ...** 텍스트 상자에 리소스를 포함 하는 리소스 그룹의 이름을 **입력 합니다.** 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

- 다시 프로 비전에 대 한 자세한 내용은 [IoT Hub Device 다시 프로 비전 개념](concepts-device-reprovision.md) 을 참조 하세요. 
- 프로 비전 해제에 대 한 자세한 내용은 [이전에 자동 프로 비전 된 장치의 프로 비전](how-to-unprovision-devices.md) 을 해제 하는 방법 











