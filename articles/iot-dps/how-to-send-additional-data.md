---
title: 장치와 Azure 장치 프로 비전 서비스 간에 추가 데이터를 전송 하는 방법
description: 이 문서에서는 장치와 DPS 간에 추가 데이터를 전송 하는 방법을 설명 합니다.
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 90402dd6fbe19811b5bb6d5ac0fbdd984b71fd33
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123173"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>장치와 DPS 간에 추가 데이터를 전송 하는 방법
디바이스를 적절한 IoT Hub에 올바르게 프로비저닝하기 위해 DPS에 디바이스의 추가 데이터가 필요하며, 디바이스에서 해당 데이터를 제공해야 하는 경우가 있습니다. 그 반대의 경우 DPS는 클라이언트 쪽 logics를 용이 하 게 하기 위해 장치에 데이터를 반환할 수 있습니다. 

## <a name="when-to-use-it"></a>사용 시기
이 기능은 [사용자 지정 할당](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)에 대 한 향상 된 기능으로 사용할 수 있습니다. 예를 들어, 사용자 개입 없이 장치 모델을 기반으로 장치를 할당 하려고 합니다. 이 경우에는 [사용자 지정 할당](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)을 사용 합니다. 장치를 구성 하 여 [장치 등록 호출](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)의 일부로 모델 정보를 보고할 수 있습니다. DPS는 사용자 지정 할당 webhook에 장치 정보를 전달 합니다. 그리고 함수는 장치 모델 정보를 받을 때이 장치가 이동 하는 IoT Hub를 결정할 수 있습니다. 마찬가지로 웹후크가 데이터를 디바이스로 반환하려는 경우 웹후크 응답의 문자열로 데이터를 다시 전달합니다.  

## <a name="device-sends-data-to-dps"></a>장치에서 DPS로 데이터 전송
장치에서 DPS에 [장치 등록 호출](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) 을 보내는 경우 본문의 다른 필드를 사용 하도록 등록 호출을 향상 시킬 수 있습니다. 본문은 다음과 같습니다. 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS가 장치에 데이터를 반환 합니다.
사용자 지정 할당 정책 webhook에서 일부 데이터를 장치로 반환 하려는 경우 데이터를 다시 웹 후크 응답의 문자열로 전달 합니다. 변경 내용은 아래의 returnData 섹션에 있습니다. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK 지원
이 기능은 C, C#, JAVA 및 Node.js [클라이언트 sdk](https://docs.microsoft.com/azure/iot-dps/)에서 사용할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* Azure IoT Hub 및 Azure IoT Hub Device Provisioning Service에서 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)를 사용하여 개발
