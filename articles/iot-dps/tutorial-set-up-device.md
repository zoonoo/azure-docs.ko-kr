---
title: "Azure IoT Hub Device Provisioning Service를 위한 장치 설정 | Microsoft Docs"
description: "장치 제조 프로세스 중 IoT Hub Device Provisioning Service를 통해 프로비전할 장치 설정"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 835a54f147b9ea543df21e7dfeb226ac42aceda3
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service를 사용하여 장치 프로비전

이전 자습서에서 장치를 IoT Hub에 자동으로 프로비전하도록 Azure IoT Hub Device Provisioning Service를 설정하는 방법을 배웠습니다. 이 자습서에서는 사용자가 [HSM(하드웨어 보안 모듈)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security)을 기반으로 장치에 대한 Device Provisioning Service를 구성하고, 장치가 처음으로 부팅될 때 Device Provisioning Service에 연결할 수 있도록 제조 프로세스에서 장치를 구성하기 위한 지침을 제공합니다. 이 자습서에서는 다음과 같은 프로세스를 설명합니다.

> [!div class="checklist"]
> * 하드웨어 보안 모듈 선택
> * 선택한 HSM에 대한 장치 프로비전 클라이언트 SDK 빌드
> * 보안 아티팩트 추출
> * 장치에서 Device Provisioning Service 구성 설정

## <a name="prerequisites"></a>필수 조건

계속하기 전에 자습서 [장치 프로비전을 위한 클라우드 설정](./tutorial-set-up-cloud.md)에 언급된 지침을 사용하여 Device Provisioning Service 인스턴스 및 IoT Hub를 만듭니다.


## <a name="select-a-hardware-security-module"></a>하드웨어 보안 모듈 선택

[Device Provisioning Service 클라이언트 SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client)는 하드웨어 보안 모듈(또는 HSM)의 두 형식에 대한 지원을 제공합니다. 

- [TPM(신뢰할 수 있는 플랫폼 모듈)](https://en.wikipedia.org/wiki/Trusted_Platform_Module).
    - TPM은 몇 개의 Linux/Ubuntu 기반 장치뿐만 아니라 대부분의 Windows 기반 장치 플랫폼에 대해 설정된 표준입니다. 장치 제조자로써, 사용자는 장치에서 이러한 OS 중 하나가 실행되는 경우 및 HSM에 대해 설정된 표준을 찾는 경우 이 HSM을 선택할 수 있습니다. TPM 칩을 사용하면 Device Provisioning Service에 개별적으로 각 장치를 등록할 수 있습니다. 개발을 위해 Windows 또는 Linux 개발 컴퓨터에서 TPM 시뮬레이터를 사용할 수 있습니다.

- 하드웨어 보안 모듈 기반 [X.509](https://cryptography.io/en/latest/x509/). 
    - X.509 기반 HSM은 X.509 인증서를 구현하는 RIoT 또는 DICE 칩에서 Microsoft 내 현재 진행되는 작업을 사용하는 상대적으로 최신의 칩입니다. X.509 칩을 사용하면 포털에서 등록을 대량으로 수행할 수 있습니다. 또한 임베디드 OS와 같은 특정 비Windows OS를 지원합니다. 개발 목적을 위해 Device Provisioning Service 클라이언트 SDK는 X.509 장치 시뮬레이터를 지원합니다. 

장치 제조자로써, 사용자는 이러한 형식 중 하나를 기반으로 하는 하드웨어 보안 모듈/칩을 선택해야 합니다. 다른 형식의 HSM은 Device Provisioning Service 클라이언트 SDK에서 현재 지원되지 않습니다.   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>선택한 HSM에 대한 장치 프로비전 클라이언트 SDK 빌드

Device Provisioning Service 클라이언트 SDK는 소프트웨어에서 선택한 보안 메커니즘을 구현하는 데 도움이 됩니다. 다음 단계에는 선택한 HSM 칩에 대한 SDK를 사용하는 방법을 보여 줍니다.

1. [시뮬레이션된 장치를 만드는 빠른 시작](./quick-create-simulated-device.md)을 수행한 경우 SDK를 빌드할 준비가 된 것입니다. 그렇지 않은 경우 [개발 환경 준비](./quick-create-simulated-device.md#setupdevbox) 섹션에서 처음 네 단계를 따릅니다. 이러한 단계는 `cmake` 빌드 도구를 설치하고, Device Provisioning Service 클라이언트 SDK에 대한 GitHub 리포지토리를 복제합니다. 

1. 명령 프롬프트에서 다음 명령 중 하나를 사용하여 장치에 대해 선택한 HSM 형식에 대한 SDK를 빌드합니다.
    - TPM 장치의 경우:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - TPM 시뮬레이터의 경우:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - X.509 장치 및 시뮬레이터의 경우:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

1. SDK는 TPM 및 X.509 HSM에 대한 Windows 또는 Ubuntu 구현을 실행하는 장치에 대한 기본 지원을 제공합니다. 이러한 지원되는 HSM의 경우 아래 [보안 아티팩트 추출](#extractsecurity) 섹션으로 이동합니다. 
 
## <a name="support-custom-tpm-and-x509-devices"></a>사용자 지정 TPM 및 X.509 장치 지원

Device Provisioning System 클라이언트 SDK는 Windows 또는 Ubuntu를 실행하지 않는 모든 TPM 및 X.509 장치에 대한 기본 지원을 제공하지 않습니다. 이러한 장치의 경우 다음 단계에 나와 있는 것처럼 특정 HSM 칩에 대한 사용자 지정 코드를 작성해야 합니다.

### <a name="develop-your-custom-repository"></a>사용자 지정 리포지토리 개발

1. HSM에 액세스하는 라이브러리를 개발합니다. 이 프로젝트에서는 사용할 Device Provisioning SDK에 대한 정적 라이브러리를 생성해야 합니다.
1. 라이브러리는 다음 헤더 파일에 정의된 함수를 구현해야 합니다. a. 사용자 지정 TPM의 경우 [사용자 지정 HSM 문서](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api)에 정의된 함수를 구현합니다.
    나. 사용자 지정 X.509의 경우 [사용자 지정 HSM 문서](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api)에 정의된 함수를 구현합니다. 

### <a name="integrate-with-the-device-provisioning-service-client"></a>Device Provisioning Service 클라이언트 통합

라이브러리를 성공적으로 빌드하면 IoThub C-SDK로 이동하여 라이브러리에 대해 연결할 수 있습니다.

1. 다음 cmake 명령에 사용자 지정 HSM GitHub 리포지토리, 라이브러리 경로 및 해당 이름을 입력합니다.
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
1. Visual Studio에서 SDK를 열고 빌드합니다. 

    - 빌드 프로세스에서는 SDK 라이브러리를 컴파일합니다.
    - SDK는 cmake 명령에 정의된 사용자 지정 HSM에 대한 연결을 시도합니다.

1. `\azure-iot-sdk-c\provisioning_client\samples\prov_dev_client_ll_sample\prov_dev_client_ll_sample.c` 샘플을 실행하여 HSM이 올바르게 구현되었는지 확인합니다.

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>보안 아티팩트 추출

다음 단계는 장치에서 HSM에 대한 보안 아티팩트를 추출하는 것입니다.

1. TPM 장치의 경우 TPM 칩 제조업체로부터 해당 장치에 연결되어 있는 **인증 키**를 찾아야 합니다. 등록 키를 해시하여 사용자 TPM 장치에 대한 고유한 **등록 ID**를 얻을 수 있습니다. 
2. X.509 장치의 경우 장치에 발급된 인증서를 얻어야 합니다. 개별 장치 등록의 경우 최종 엔터티 인증서이며 장치의 그룹 등록의 경우 루트 인증서입니다.

이러한 보안 아티팩트는 장치를 Device Provisioning Service에 등록해야 합니다. 그런 다음 프로비전 서비스는 향후 특정한 시점에 이러한 장치가 부팅 및 연결되도록 대기합니다. 이러한 보안 아티팩트를 사용하여 등록을 만드는 방법에 대한 자세한 내용은 [장치 등록을 관리하는 방법](how-to-manage-enrollments.md)을 참조하세요. 

장치를 처음으로 부팅하는 경우 클라이언트 SDK는 칩과 상호 작용하여 장치에서 보안 아티팩트를 추출하고, Device Provisioning Service에 등록을 확인합니다. 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>장치에서 Device Provisioning Service 구성 설정

장치 제조 프로세스의 마지막 단계에서는 서비스에 장치를 등록하기 위해 Device Provisioning Service 클라이언트 SDK를 사용하는 응용 프로그램을 작성하는 것입니다. 이 SDK는 사용할 응용 프로그램에 대해 다음과 같은 API를 제공합니다.

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

[이 빠른 시작의 장치를 위한 첫 부팅 시퀀스 시뮬레이션 섹션](./quick-create-simulated-device.md#firstbootsequence)에서 설명한 것처럼 변수 `uri` 및 `id_scope`를 사용하기 전에 초기화해야 합니다. Device Provisioning 클라이언트 등록 API `Prov_Device_LL_Create`는 전역 Device Provisioning Service에 연결합니다. *ID 범위*는 서비스에 의해 생성되고 고유성이 보장됩니다. 이는 변경할 수 없으며 등록 ID를 고유하게 식별하는 데 사용됩니다. `iothub_uri`를 사용하면 IoT Hub 클라이언트 등록 API `IoTHubClient_LL_CreateFromDeviceAuth`를 올바른 IoT Hub로 연결할 수 있습니다. 


이러한 API는 장치를 부팅할 때 Device Provisioning Service에 연결하고 등록하는 데 유용합니다. IoT Hub에 대한 정보를 살펴보고 연결하세요. 파일 `provisioning_client/samples/prov_client_ll_sample/prov_client_ll_sample.c`에는 이러한 API를 사용하는 방법이 나와 있습니다. 일반적으로 클라이언트 등록을 위해 다음과 같은 프레임워크를 만들어야 합니다.

```C
static const char* global_uri = "global.azure-devices-provisioning.net";
static const char* id_scope = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    prov_dev_security_init(hsm_type); // initialize your HSM 

    prov_transport = Prov_Device_HTTP_Protocol;
    
    PROV_CLIENT_LL_HANDLE handle = Prov_Device_LL_Create(global_uri, id_scope, prov_transport); // Create your provisioning client

    if (Prov_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
        // The register_callback is called when registration is complete or fails
            Prov_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    Prov_Client_LL_Destroy(handle); // Clean up the Provisioning client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

테스트 서비스 설정을 사용하여 처음에는 시뮬레이션된 장치를 사용하여 Device Provisioning Service 클라이언트 등록 응용 프로그램을 구체화할 수도 있습니다. 응용 프로그램이 테스트 환경에서 작동되면 특정 장치에 대해 빌드하고, 실행 파일을 사용자 장치 이미지에 복사할 수 있습니다. 장치를 아직 시작하지 마십시오. 장치를 시작하기 전에 [Device Provisioning Service에 장치를 등록](./tutorial-provision-device-to-hub.md#enrolldevice)해야 합니다. 이 프로세스를 알아보려면 아래 다음 단계를 참조하세요. 

## <a name="clean-up-resources"></a>리소스 정리

이 시점에서 포털에서 Device Provisioning 및 IoT Hub 서비스를 설정할 수도 있습니다. 장치 프로비전 설정을 중단 및/또는 이러한 서비스 중 하나를 사용하는 것을 미루려면 불필요한 비용이 발생하지 않도록 종료하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 Device Provisioning Service를 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.  
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.  


## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 하드웨어 보안 모듈 선택
> * 선택한 HSM에 대한 장치 프로비전 클라이언트 SDK 빌드
> * 보안 아티팩트 추출
> * 장치에서 Device Provisioning Service 구성 설정

자동 프로비전을 위한 Azure IoT Hub Device Provisioning Service에 등록하여 IoT Hub에 장치를 프로비전하는 방법을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [IoT Hub에 장치 프로비전](tutorial-provision-device-to-hub.md)

