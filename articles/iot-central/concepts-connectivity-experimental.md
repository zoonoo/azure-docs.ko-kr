---
title: Azure IoT Central에서 디바이스 연결 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 디바이스 연결과 관련된 주요 개념을 소개합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 5d90980172b49071601edeebbf28907def862e4e
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55825060"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central의 디바이스 연결 | Microsoft Docs

이 문서에서는 Microsoft Azure IoT Central의 디바이스 연결과 관련된 주요 개념을 소개합니다.

Azure IoT Central은 [Azure IoT Hub DPS(Device Provisioning service)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)를 사용하여 IoT Central이 대규모로 디바이스의 온보딩 및 연결을 지원할 수 있도록 설정합니다.

-   이제 고객은 먼저 디바이스를 IoT Central에 등록할 필요 없이 디바이스 자격 증명을 생성하고 오프라인에서 디바이스를 구성할 수 있음
-   IoT Central은 업계에서 권장하는 X509 인증 기반 연결을 통해 디바이스 연결을 지원하면서 계속해서 SAS(공유 액세스 서명) 연결을 지원하고 개선함
-   IoT Central 고객은 자신의 고유 디바이스 ID를 통해 디바이스를 IoT Central에 등록할 수 있고, 기존 백 오피스 시스템과 간단한 통합을 사용하도록 설정함
-   IoT Central에 디바이스를 연결하기 위한 하나의 일관된 방법이 있음 

>[!NOTE]
>IoT Central은 모든 디바이스 등록 및 연결에 대해 Azure IoT DPS(Device Provisioning Service)를 사용합니다. [DPS에 대한 자세한 정보](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)를 참조하세요.

사용 사례를 기반으로 IoT Central에 디바이스를 연결하기 위한 지침을 따릅니다.
1. [단일 디바이스를 신속하게 연결(공유 액세스 서명 사용)](#connect-a-single-device)
1. [SAS(공유 액세스 서명)를 사용하여 대규모로 디바이스 연결](#connect-devices-at-scale-using-shared-access-signatures)
1. [X509 인증을 사용하여 대규모로 디바이스 연결](#connect-devices-using-x509-certificates)**프로덕션 워크로드에 권장됨**
1. [먼저 디바이스를 등록하지 않고 연결](#connect-without-first-registering-devices) 


>[!NOTE]
>디바이스가 **global.azure-devices-provisioning.net**을 연결하고 프로비전할 수 있는 글로벌 엔트포인트입니다.

## <a name="connect-a-single-device"></a>단일 디바이스 연결
SAS를 사용하여 IoT Central에 단일 디바이스 연결하기는 간단하며 몇 단계만 사용합니다. 
1. Device Explorer에서 **실제 디바이스**를 추가하고 **+새로 만들기 &gt; 실제 디바이스**를 클릭하여 실제 디바이스를 추가합니다.
    * 디바이스 ID**<span style="color:Red">(소문자여야 함)</span>** 를 입력하거나 제안된 디바이스 ID를 사용합니다.
    * 디바이스 이름을 입력하거나 제안된 이름을 사용합니다.   
    ![디바이스 추가](media/concepts-connectivity/add-device.png)
1. 디바이스 페이지에서 **연결**을 클릭하여 추가 디바이스의 **범위 ID, 디바이스 ID 및 기본 키** 같은 연결 정보를 가져옵니다.
    * **[범위 ID](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)** 는 IoT Central 앱 단위이며, 앱 내에서 고유한 디바이스 ID를 확인하는 데 사용된 DPS에서 생성됩니다.
    * **디바이스 ID**는 앱당 고유한 디바이스 ID이며, 디바이스는 등록 호출의 일부로 디바이스 ID를 전송해야 합니다.   
    * **기본 키**는 이 특정 디바이스용 IoT Central에서 생성된 SAS 토큰입니다. 
    ![연결 세부 정보](media/concepts-connectivity/device-connect.png)
1. 디바이스 코드에서 이러한 연결 세부 정보**디바이스 ID, 디바이스 이름 및 디바이스 기본 키**를 사용하여 디바이스를 프로비전 및 연결하고 즉시 데이터 흐름이 끝까지 표시되기 시작합니다. MxChip 디바이스를 사용하는 경우 [여기의 단계별 지침](howto-connect-devkit-experimental.md#add-a-real-device)을 따라 **DevKit 디바이스 준비** 섹션부터 시작합니다.   

    다음은 사용하려는 다른 언어에 대한 참조입니다.

    *   **C 언어:** C를 사용하는 경우 [이 C 샘플 디바이스 클라이언트](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)를 따라 샘플 디바이스를 연결합니다. 샘플에서 다음 설정을 사용합니다.   

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    *   **Node.js:**  Node.js를 사용하려는 경우 [여기의 단계별 지침을 사용하여](tutorial-add-device-experimental.md#prepare-the-client-code) **클라이언트 코드 준비** 섹션부터 시작합니다.



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>공유 액세스 서명을 사용하여 대규모로 디바이스 연결

SAS를 사용하여 IoT Central에서 대규모로 디바이스를 연결하려면 두 가지 관련 단계가 있습니다. 
1. **디바이스 등록**: CSV 파일을 통해 IoT Central로 디바이스를 가져와 디바이스를 등록하고, 디바이스를 연결하는 데 사용할 디바이스 연결 세부 정보를 사용하여 디바이스를 내보냅니다.
1. **디바이스 설정**: 프로비전 서비스가 켜져 있는 경우 연결 정보/IoT Central 앱 할당을 가져오기 위해 프로비전 서비스를 호출할 수 있도록 연결 세부 정보(**범위 ID, 디바이스 ID 및 기본 키**)를 사용하여 디바이스를 프로그래밍합니다.

>[!NOTE]
>먼저 IoT Central에 디바이스를 등록하지 않고도 디바이스를 연결할 수 있는 경우 고급 옵션을 사용할 수도 있습니다. [여기서 자세히 알아봅니다](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

**디바이스 등록**

대량의 장치를 애플리케이션에 연결할 수 있도록 Azure IoT Central은 CSV 파일을 통해 장치 대량 가져오기 기능을 제공합니다. 

CSV 파일 요구 사항: CSV 파일에는 다음 열 및 헤더가 있어야 합니다.
1.  IOTC_DeviceID**<span style="color:Red">(소문자여야 함)</span>**
1.  IOTC_DeviceName(선택 사항)



애플리케이션에 등록하려면 장치를 가져오기
1.  왼쪽 탐색 메뉴에서 **Explorer**를 선택합니다.
1.  왼쪽 패널에서 디바이스를 대량으로 만들 디바이스 템플릿을 선택합니다. 
1.  **가져오기**를 클릭하고, 가져올 디바이스 ID 목록이 들어 있는 CSV 파일을 선택합니다.
CSV 파일에는 다음 열(및 헤더)가 있어야 합니다.
    *   IOTC_DeviceID**<span style="color:Red">(소문자여야 함)</span>**
    *   IOTC_DeviceName(선택 사항)
1.  가져오기가 완료되면 디바이스 그리드에 성공 메시지가 표시됩니다.

디바이스 연결 세부 정보를 가져오기 위해 디바이스를 내보냅니다. [내보내기]는 디바이스 ID, 디바이스 이름 및 디바이스 키를 사용하여 CSV 파일을 만듭니다. 이러한 정보를 사용하여 IoT Central에 디바이스를 연결합니다.
애플리케이션에서 장치를 대량으로 내보내려면 다음을 수행합니다.
1.  왼쪽 탐색 메뉴에서 **Explorer**를 선택합니다.
1.  내보낼 디바이스를 선택한 다음, **내보내기** 동작을 클릭합니다.
1.  내보내기가 완료되면 성공 메시지가 생성된 파일을 다운로드할 수 있는 링크와 함께 표시됩니다.
1.  디스크의 로컬 폴더로 파일을 다운로드하려면 성공 메시지를 클릭합니다.
1.  내보낸 CSV 파일에는 다음과 같은 열 정보가 포함됩니다. **디바이스 ID, 디바이스 이름, 디바이스 주/보조 키 및 기본/보조 인증서 지문**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**디바이스 설정**

디바이스 코드의 이러한 연결 세부 정보(**디바이스 ID(IOTC_DEVICEID), 디바이스 기본 키(IOTC_SASKEY_PRIMARY) 및 범위 ID**)를 사용하여 디바이스를 프로비전하고 연결합니다. 아직 없는 경우 IoT Central 앱(**관리 &gt; 디바이스 연결 &gt; 범위 ID**)에서 **범위 ID**를 가져옵니다.
**MxChip** 디바이스를 사용하여 연결하는 경우 [여기의 단계별 지침](howto-connect-devkit-experimental.md#add-a-real-device)을 따라 **DevKit 디바이스 준비** 섹션부터 시작합니다.   

다음은 사용하려는 다른 언어에 대한 참조입니다.

   *   **C 언어:** C를 사용하는 경우 [이 C 샘플 디바이스 클라이언트](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)를 따라 샘플 디바이스를 연결합니다. 샘플에서 다음 설정을 사용합니다.   
         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```
    * **Node.js:**  Node.js를 사용하려는 경우 [여기의 단계별 지침을 사용하여](tutorial-add-device-experimental.md#prepare-the-client-code) **클라이언트 코드 준비** 섹션부터 시작합니다.


## <a name="connect-devices-using-x509-certificates"></a>X509 인증서를 사용하여 디바이스 연결

X.509 인증서를 증명 메커니즘으로 사용하면 **프로덕션**의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. X.509 인증서는 일반적으로 체인의 각 인증서가 다음으로 높고, 그 다음으로 높은 등등의 우선 순위에 있는 인증서의 개인 키로 서명되는 신뢰의 인증서 체인에 정렬되며, 자체 서명된 루트 인증서에서 종료됩니다. 이렇게 하면 신뢰할 수 있는 루트 CA(인증 기관)에서 생성한 루트 인증서에서 각각의 중간 CA를 거쳐 디바이스에 설치된 최종 엔터티 “리프” 인증서로 위임된 신뢰 체인을 설정합니다. 자세한 내용은 [X.509 CA 인증서를 사용하여 디바이스 인증](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview)을 참조하세요. 

X509 인증서를 사용하여 IoT Central에 디바이스를 연결하려면 다음과 같이 세 가지 주요 단계가 있음 
1. **연결 설정 구성**: 디바이스 인증서를 생성하는 데 사용되는 X509 루트/중간 인증서를 추가/확인하여 IoT Central 앱에서 연결 설정을 구성합니다.  X509 인증서에 대한 연결 설정을 구성하기 위한 두 단계가 있습니다.  

    *   리프 디바이스 인증서를 생성하는 데 사용하는 **X509 루트 또는 중간 인증서 추가**합니다. 관리 &gt; 디바이스 연결 &gt; 인증서로 이동합니다. 
    
        ![연결 설정](media/concepts-connectivity/connection-settings.png)
    *   **인증서 확인:** 인증서 소유권을 확인하면 인증서의 업로더가 인증서의 개인 키를 소유했는지 확인합니다. 인증서를 확인하려면
        *  확인 코드를 생성한 다음, 확인 코드 필드 옆에 있는 단추를 클릭하여 확인 코드를 생성합니다. 
        *  인증서 코드를 사용하여 X.509 확인 인증서를 만든 다음, 인증서를 .cer 파일로 저장합니다. 
        *  서명된 확인 인증서를 업로드하고 확인을 클릭합니다.

        ![연결 설정](media/concepts-connectivity/verify-cert.png)
    *   **보조 인증서:** IoT 솔루션의 수명 주기 동안 인증서를 배포해야 합니다. 인증서를 배포하는 주된 이유 두 가지는 보안 위반 및 인증서 만료입니다. 보조 인증서는 기본 인증서를 업데이트하는 동안 프로비전하려는 디바이스의 가동 중지 시간을 줄이는 데 사용됩니다.

    **테스트 전용** 
    
    다음은 CA 인증서 및 디바이스 인증서를 생성하는 데 사용할 수 있는 몇몇 유틸리티 명령줄 도구입니다.

    * MxChip를 사용하는 경우 CA를 생성하여 IoT Central 앱에 추가하고 인증서를 확인하기 위한 [명령줄 도구](https://aka.ms/iotcentral-docs-dicetool)가 있습니다. 

    *   이 [명령줄 도구](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )를 사용하여
        * 인증서 체인(GitHub 문서의 2단계에 따라)을 만듭니다. 
         인증서를 .cer 파일로 저장하고 IoT Central(기본)에 업로드합니다.   
        * IoT Central 앱에서 확인 코드를 가져오고, 인증서(GitHub 문서의 3단계에 따라)를 생성하고, 업로드하여 확인합니다. 
        * 도구(4단계에 따라)에 대한 매개 변수로 디바이스 ID를 사용하여 리프 인증서를 만듭니다. 인증서를 저장하여 디바이스에서 사용합니다.     

1. **디바이스 등록**: CSV 파일을 통해 디바이스를 IoT Central로 가져와 등록합니다.

1. **디바이스 설정**: 업로드된 루트 인증서를 사용하여 리프 인증서를 생성합니다. **디바이스 ID**를 리프 인증서의 CNAME으로 사용하되 **소문자**여야 합니다. **테스트 전용** 리프/장치 인증서를 생성하기 위한 [명령줄 도구](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )가 있습니다.

    서비스 정보를 프로비전하여 디바이스를 프로그래밍하면 디바이스가 켜질 때 해당 연결 세부 정보 및 IoT Central 앱 할당을 가져올 수 있습니다.    

    **추가 참조** 
    *   [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)에 대한 샘플 구현.  

    *   [C의 샘플 디바이스 클라이언트.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

>[!NOTE]
>디바이스에 대한 리프 인증서를 생성할 때 **디바이스 ID**를 cname으로 사용합니다.

>[!NOTE]
>**디바이스 ID**는 소문자여야 합니다. 
 
## <a name="connect-without-first-registering-devices"></a>먼저 디바이스를 등록하지 않고 연결
IoT Central이 사용하는 주요 시나리오 중 하나는 OEM이 디바이스를 대량으로 제작하고, 자격 증명을 생성하고, 먼저 IoT Central에 등록할 필요 없이 팩터리에서 자격 증명을 구성하는 것입니다. 디바이스가 켜지고 IoT Central에 연결되면 운영자는 디바이스가 IoT Central 앱에 연결되도록 승인합니다.

다음에서는 이 기능을 사용하여 디바이스를 연결하는 흐름을 보여 줍니다.

![연결 설정](media/concepts-connectivity/device-connection-flow.png)


사용자가 선택한 디바이스 인증 체계(X509/SAS)에 따라 단계를 따릅니다.

1. **연결 설정** 
    * **X509 인증서:** [루트/중간 인증서를 추가 및 확인](#connect-devices-using-x509-certificates)하여 다음 단계에서 디바이스 인증서를 생성하는 데 사용합니다.
    * **SAS:** 기본 키(이 키는 이 IoT Central 애플리케이션에 대한 그룹 SAS 키임)를 복사하여 다음 단계에서 디바이스 SAS 키를 생성하는 데 사용합니다. 
![연결 설정 SAS](media/concepts-connectivity/connection-settings-sas.png)

1. **디바이스 자격 증명 생성** 
    *   **인증서 X509:** 이 앱에 추가한 루트/중간 인증서를 사용하여 디바이스에 대한 리프 인증서를 생성합니다. **디바이스 ID**를 리프 인증서의 cname으로 사용하되 **<span style="color:Red">(소문자여야 합니다)</span>**. 테스트용 리프/장치 인증서를 생성하기 위한 [명령줄 도구](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )가 있습니다.
    *   **SAS** 디바이스: 이 [명령줄 도구](https://www.npmjs.com/package/dps-keygen)를 사용하여 SAS 키를 생성할 수 있습니다. 이전 단계의 기본 SAS 키(그룹 SAS 키)를 사용합니다. 디바이스 ID는 **<span style="color:Red">소문자</span>** 여야 합니다.

        아래 지침을 사용하여 디바이스 SAS 키를 생성함           

        ```
        npm i -g dps-keygen
        ```
    
        **사용 현황**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **디바이스 설정** 
    
     **범위 ID, 디바이스 ID, 디바이스 인증서/SAS 키**를 사용하여 디바이스를 깜박거린 다음, 디바이스를 켜고 IoT Central 앱에 연결합니다.

1. **IoT Central에 디바이스 연결:** 디바이스가 켜지면 등록을 위해 DPS/IoT Central에 연결됩니다.

1. **템플릿에 디바이스 연결:** 연결된 디바이스는 **Device Explorer**의 **연결되지 않은 디바이스** 아래에 표시됩니다. 디바이스의 프로비전 상태는 **등록**됩니다. 디바이스를 적절한 디바이스 템플릿에 **연결**하고 디바이스가 IoT Central 앱에 연결되도록 승인합니다. 디바이스가 IoT Central 앱에 대한 연결 세부 정보를 가져온 다음, 연결하여 데이터를 보내기 시작합니다. 이제 디바이스 프로비전이 완료되었으므로 *프로비전 상태*가 **프로비전됨**으로 전환됩니다.

## <a name="device-provisioning-status"></a>디바이스 프로비전 상태
실제 디바이스를 Azure IoT Central에 연결한 경우 일련의 관련 단계가 있음 
1. **등록됨**: 디바이스가 먼저 **등록되었다**는 의미는 디바이스가 IoT Central에서 만들어지고 디바이스에 대한 디바이스 ID를 보유한다는 것입니다.
다음과 같은 경우 디바이스가 등록됩니다.  
    *   새 실제 디바이스가 **Explorer**에 추가될 경우
    *   디바이스 집합이 **Explorer**에서 **가져오기**를 사용하여 추가될 경우
    *   등록되지 않았지만 유효한 자격 증명을 사용하여 연결되고 **연결되지 않은** 디바이스 아래에 표시되는 디바이스의 경우 

    위의 모든 경우에는 *프로비전 상태*가 **등록됨**으로 표시됩니다.

1. **프로비전됨**: 다음 단계는 디바이스가 유효한 자격 증명과 연결될 때 IoT Central이 IoT Hub에서 디바이스를 만들어 프로비전 단계를 완료하는 것입니다. 그런 다음, IoT Central은 데이터에 연결 문자열을 반환하여 데이터에 연결하고 데이터를 보내기 시작합니다. 디바이스의 *프로비전 상태*는 이제 **등록됨**에서 **프로비전됨**으로 전환됩니다.

1.  **차단됨**: 운영자는 디바이스를 차단할 수 있습니다. 디바이스가 차단되면 IoT Central에 데이터를 보낼 수 없고 디바이스를 다시 설정해야 합니다. 차단된 디바이스는 *프로비전 상태*가 **차단됨**으로 표시됩니다. 운영자는 또한 디바이스의 차단을 해제할 수 있습니다. 디바이스 차단이 해제되면 *프로비전 상태*가 이전의 *프로비전 상태*(등록됨 또는 프로비전됨)로 돌아갑니다. 

## <a name="getting-device-connection-string"></a>디바이스 연결 문자열 가져오기
다음 단계를 사용하여 IoT Hub 디바이스 연결 문자열을 Azure IoT Hub로 가져올 수 있음 
1. 디바이스 페이지에서 **범위 ID, 디바이스 ID 및 디바이스 기본 키** 같은 연결 세부 정보를 가져옵니다(디바이스 페이지 &gt; 연결 클릭으로 이동). 

    ![연결 정보](media/concepts-connectivity/device-connect.png)

1. 아래 명령줄 도구를 사용하여 디바이스 연결 문자열을 가져옵니다.
    다음 지침을 사용하여 디바이스 연결 문자열을 가져옵니다.  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **사용 현황**

    연결 문자열을 만들려면 bin/폴더 아래에 있는 이진 파일을 찾습니다.
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    [여기에서 dps-keygen 도구](https://www.npmjs.com/package/dps-keygen)에 대해 자세히 알아봅니다.

## <a name="sdk-support"></a>SDK 지원

Azure 장치 SDK는 장치에서 Azure IoT Central 애플리케이션에 연결하는 코드를 구현하는 가장 쉬운 방법을 제공합니다. 현재 다음과 같은 디바이스 SDK를 사용할 수 있습니다.

- [C용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Python용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Java용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

각 디바이스는 디바이스를 식별하는 고유한 연결 문자열을 사용하여 연결합니다. 디바이스는 등록된 IoT Hub에만 연결할 수 있습니다. Azure IoT Central 응용 프로그램에 실제 디바이스를 만들면 응용 프로그램에서는 사용할 연결 문자열을 생성합니다.

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 기능 및 IoT Hub 연결

IoT Hub와의 모든 디바이스 통신에 다음 IoT Hub 연결 옵션이 사용됩니다.

- [디바이스-클라우드 메시지](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [디바이스 쌍](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

다음 표에는 Azure IoT Central 디바이스 기능이 IoT Hub 기능에 매핑되는 방식이 요약되어 있습니다.

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 측정: 원격 분석 | 디바이스-클라우드 메시징 |
| 디바이스 속성 | 디바이스 쌍 reported 속성 |
| 설정 | 디바이스 쌍 desired 및 reported 속성 |

디바이스 SDK 사용에 대해 자세히 알아보려면 다음 문서 중 하나에서 코드 예제를 참조하세요.

- [Azure IoT Central 애플리케이션에 일반 Node.js 클라이언트 연결](howto-connect-nodejs-experimental.md)
- [Azure IoT Central 애플리케이션에 Raspberry Pi 장치 연결](howto-connect-raspberry-pi-python.md)
- [Azure IoT Central 애플리케이션에 DevDiv 키트 장치 연결](howto-connect-devkit-experimental.md)


## <a name="protocols"></a>프로토콜

디바이스 SDK는 IoT Hub에 연결하기 위한 다음과 같은 네트워크 프로토콜을 지원합니다.

- MQTT
- AMQP
- HTTPS

프로토콜 간 차이점 및 프로토콜 선택 방법에 대한 지침은 [통신 프로토콜 선택](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols)을 참조하세요.

지원되는 프로토콜을 디바이스에서 사용할 수 없는 경우 Azure IoT Edge를 사용하여 프로토콜을 변환하면 됩니다. IoT Edge는 Azure IoT Central 애플리케이션의 에지로 프로세싱을 오프로드하는 에지의 인텔리전스 시나리오를 지원합니다.

## <a name="security"></a>보안

디바이스와 Azure IoT Central 간에 교환되는 모든 데이터는 암호화됩니다. IoT Hub는 디바이스 연결 IoT Hub 엔드포인트 중 하나에 연결하는 디바이스의 모든 요청을 인증합니다. 유선으로 자격 증명을 교환하지 못하도록 방지하기 위해 디바이스에서는 서명된 토큰을 사용하여 인증합니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)를 참조하세요.

> [!NOTE]
> 현재 Azure IoT Central에 연결하는 디바이스는 SAS 토큰을 사용해야 합니다. X.509 인증서는 Azure IoT Central에 연결하는 디바이스에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central의 디바이스 연결에 대해 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

- [DevKit 디바이스 준비 및 연결](howto-connect-devkit-experimental.md)
- [Raspberry Pi 준비 및 연결](howto-connect-raspberry-pi-python.md)
- [Azure IoT Central 애플리케이션에 일반 Node.js 클라이언트 연결](howto-connect-nodejs-experimental.md)
