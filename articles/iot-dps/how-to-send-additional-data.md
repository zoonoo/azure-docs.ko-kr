---
title: 장치와 Azure 장치 프로비저닝 서비스 간에 페이로드를 전송하는 방법
description: 이 문서에서는 장치와 장치 프로비저닝 서비스(DPS) 간에 페이로드를 전송하는 방법을 설명합니다.
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246690"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>장치와 DPS 간에 페이로드를 전송하는 방법
DPS는 올바른 IoT Hub에 적절하게 프로비전하기 위해 장치에서 더 많은 데이터가 필요하며 해당 데이터를 장치에서 제공해야 하는 경우가 있습니다. 그 반대의 경우도 마찬가지입니다. 

## <a name="when-to-use-it"></a>사용하는 시기
이 기능은 [사용자 지정 할당에](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)대한 향상된 기능으로 사용할 수 있습니다. 예를 들어 사람의 개입 없이 장치 모델을 기반으로 장치를 할당하려고 합니다. 이 경우 [사용자 지정 할당을](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)사용합니다. [레지스터 장치 호출의](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)일부로 모델 정보를 보고하도록 장치를 구성할 수 있습니다. DPS는 장치의 페이로드를 사용자 지정 할당 웹후크에 전달합니다. 또한 함수는 이 장치가 장치 모델 정보를 수신할 때 어떤 IoT Hub로 이동할지 결정할 수 있습니다. 마찬가지로 웹후크가 일부 데이터를 장치에 반환하려는 경우 웹후크 응답의 문자열로 데이터를 다시 전달합니다.  

## <a name="device-sends-data-payload-to-dps"></a>장치가 DPS로 데이터 페이로드를 전송합니다.
장치가 DPS에 [레지스터 장치 호출을](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) 보내는 경우 레지스터 호출을 향상시켜 본문의 다른 필드를 사용할 수 있습니다. 본체는 다음과 같습니다. 
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

## <a name="dps-returns-data-to-the-device"></a>DPS가 장치에 데이터를 반환합니다.
사용자 지정 할당 정책 webhook 장치에 일부 데이터를 반환 하려는 경우 웹 후크 응답에서 문자열로 데이터를 다시 전달 합니다. 변경 사항은 아래 페이로드 섹션에 있습니다. 
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
이 기능은 C, C#, JAVA 및 Node.js [클라이언트 SDK에서](https://docs.microsoft.com/azure/iot-dps/)사용할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* Azure IoT Hub 및 Azure IoT Hub Device Provisioning Service에서 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)를 사용하여 개발
