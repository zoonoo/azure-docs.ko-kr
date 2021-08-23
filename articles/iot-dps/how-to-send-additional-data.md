---
title: 디바이스와 Azure Device Provisioning Service 간에 페이로드를 전송하는 방법
description: 이 문서에서는 디바이스 및 DPS(Device Provisioning Service) 간에 페이로드를 전송하는 방법을 설명합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: ff8157a938c3b754a35c5a588ac5590be2432971
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566932"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>디바이스와 DPS 간에 페이로드를 전송하는 방법
경우에 따라 DPS는 올바른 IoT Hub에 적절히 프로비저닝하기 위해 디바이스의 더 많은 데이터를 필요로 하며 디바이스는 해당 데이터를 제공해야 합니다. 그 반대의 경우 DPS는 클라이언트 쪽 논리를 용이하게 하기 위해 디바이스에 데이터를 반환할 수 있습니다. 

## <a name="when-to-use-it"></a>사용하는 경우
이 기능은 [사용자 지정 할당](./how-to-use-custom-allocation-policies.md)에 대한 개선 기능으로 사용할 수 있습니다. 예를 들어, 사용자 개입 없이 디바이스 모델을 기준으로 디바이스를 할당하려고 합니다. 이 경우에는 [사용자 지정 할당](./how-to-use-custom-allocation-policies.md)을 사용합니다. [디바이스 등록 호출](/rest/api/iot-dps/device/runtime-registration/register-device)의 일부로 모델 정보를 보고하도록 디바이스를 구성할 수 있습니다. DPS는 사용자 지정 할당 Webhook에 디바이스의 페이로드를 전달합니다. 또한 함수는 디바이스 모델 정보를 받을 때 이 디바이스가 이동하는 IoT Hub를 결정할 수 있습니다. 마찬가지로 Webhook에서 디바이스에 일부 데이터를 반환하려는 경우 데이터를 다시 Webhook 응답의 문자열로 전달합니다.  

## <a name="device-sends-data-payload-to-dps"></a>디바이스에서 DPS로 데이터 페이로드 전송
디바이스에서 DPS에 [레지스터 디바이스 호출](/rest/api/iot-dps/device/runtime-registration/register-device)을 전송하는 경우 본문의 다른 필드를 사용하도록 레지스터 호출을 향상시킬 수 있습니다. 본문은 다음과 같습니다. 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS가 디바이스에 데이터 반환
사용자 지정 할당 정책 Webhook에서 일부 데이터를 디바이스로 반환하려는 경우 데이터를 다시 Webhook 응답의 문자열로 전달합니다. 변경 내용은 아래 페이로드 섹션에 나와 있습니다. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK 지원
이 기능은 C, C#, JAVA 및 Node.js [클라이언트 SDK](./index.yml)에서 사용할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* Azure IoT Hub 및 Azure IoT Hub Device Provisioning Service에서 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)를 사용하여 개발
