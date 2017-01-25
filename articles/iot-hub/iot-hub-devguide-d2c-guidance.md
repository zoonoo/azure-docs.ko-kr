---
title: "Azure IoT Hub 장치-클라우드 옵션 | Microsoft Docs"
description: "개발자 가이드 - 장치-클라우드 메시지, reported 속성 또는 클라우드-장치 통신을 위한 파일 업로드를 사용하는 경우에 대한 지침입니다."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: e223d0613cd48994315451da87e6b7066585bdb6
ms.openlocfilehash: 8c3479e29b55eacc30842ffdfee23b4a00a13126


---
# <a name="device-to-cloud-communications-guidance"></a>장치-클라우드 통신 지침
장치 앱에서 솔루션 백 엔드로 정보를 전송할 때 IoT Hub은 다음 세 가지 옵션을 공개합니다.

* [장치-클라우드(D2C) 메시지][lnk-d2c] - 시계열 원격 분석 및 경고의 경우
* [reported 속성][lnk-twins] - 장기 실행 워크플로의 사용 가능한 기능, 조건 및 상태와 같은 장치 상태 정보를 보고하는 경우(예: 구성 및 소프트웨어 업데이트)
* [파일 업로드][lnk-fileupload] - 간헐적으로 연결된 장치로 업로드되거나 대역폭을 절약하기 위해 압축된 미디어 파일 및 대형 원격 분석 배치의 경우

다음은 다양한 장치-클라우드 통신 옵션에 대해 자세히 비교하고 있습니다.

|  | D2C 메시지 | reported 속성 | 파일 업로드 |
| ---- | ------- | ---------- | ---- |
| 시나리오 | 원격 분석 시계열 및 경고. 예: 256KB 센서 데이터 배치가 5분마다 전송됩니다. | 사용 가능한 기능 및 조건. 예: 장치 현재 연결 모드(휴대 전화 또는 Wi-Fi). 구성 및 소프트웨어 업데이트와 같이 장기 실행 워크플로 동기화 | 미디어 파일. 대형(일반적으로 압축됨) 원격 분석 배치 |
| 저장 및 검색 | IoT Hub에서 일시적으로 최대 7일 동안 저장합니다. 순차 읽기만 | IoT Hub에서 장치 쌍에 저장합니다. [IoT Hub 쿼리 언어][lnk-query]를 사용하여 검색할 수 있습니다. | 사용자 제공 Azure Storage 계정에 저장됩니다. |
| 크기 | 최대 256KB 메시지 | 최대 reported 속성 크기는 8KB입니다. | Azure Blob Storage에서 지원하는 최대 파일 크기 |
| Frequency(빈도) | 높음. 자세한 내용은 [IoT Hub 제한][lnk-quotas] 참조 | 중간. 자세한 내용은 [IoT Hub 제한][lnk-quotas] 참조 | 낮음. 자세한 내용은 [IoT Hub 제한][lnk-quotas] 참조 |
| 프로토콜 | 모든 프로토콜에서 사용할 수 있습니다. | 현재 MQTT를 사용할 때만 사용할 수 있습니다. | 프로토콜을 사용할 때 사용할 수 있지만 장치에 HTTP가 필요합니다. |

> [!NOTE]
> 응용 프로그램이 원격 분석 시계열 또는 경고로 정보를 보내고 장치 쌍에서 사용할 수 있게 할 수도 있습니다. 이러한 경우 장치 앱은 D2C 메시지를 보내고 속성 변경을 보고하거나 솔루션 백 엔드에서 메시지를 수신할 때 장치 쌍의 태그에 정보를 저장할 수 있습니다. D2C 메시지는 장치 쌍 업데이트보다 훨씬 높은 처리량을 허용하므로 때로는 모든 D2C 메시지에 대해 장치 쌍을 업데이트하지 않는 것이 바람직합니다.
> 
> 

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages



<!--HONumber=Dec16_HO1-->


