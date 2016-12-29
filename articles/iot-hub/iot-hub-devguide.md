---
title: "IoT Hub에 대한 개발자 가이드 항목 | Microsoft Docs"
description: "Azure IoT Hub 끝점, 보안, ID 레지스트리, 장치 관리 및 메시징을 포함하는 Azure IoT Hub 개발자 가이드"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: e71676834e06b21482196d2a1bd8b4fd7f0d7d32


---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT Hub 개발자 가이드
Azure IoT Hub는 수백만의 장치와 응용 프로그램 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다.

Azure IoT Hub는 다음을 제공합니다.

* 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 보안 통신을 제공합니다.
* 다수의 장치-클라우드 및 클라우드-장치 하이퍼스케일(hyper-scale) 통신 옵션.
* 쿼리가 가능한 장치 단위 상태 정보 및 메타데이터 저장소.
* 가장 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리로 쉽게 장치에 연결합니다.

이 IoT Hub 개발자 가이드는 다음 문서를 포함합니다.

* [IoT Hub를 통해 메시지 보내고 받기][devguide-messaging]에서는 IoT Hub가 노출하는 메시징 기능(장치-클라우드 및 클라우드-장치)을 설명합니다. 이 문서는 메시지 형식, 지원되는 통신 프로토콜 및 사용하는 포트 번호와 같은 항목에 대한 정보도 포함합니다.
* [장치-클라우드 통신 지침][lnk-d2c-guidance]에서는 장치-클라우드 메시지, 장치 쌍의 reported 속성, 파일 업로드 중에 하나를 선택하도록 도와줍니다.
* [클라우드-장치 통신 지침][lnk-c2d-guidance]에서는 직접 메서드, 장치 쌍의 desired 속성, 클라우드-장치 메시지 중에 하나를 선택하도록 도와줍니다.
* [장치에서 파일 업로드][devguide-upload]에서는 장치에서 파일을 업로드하는 방법을 설명합니다. 이 문서는 업로드 프로세스에서 보낼 수 있는 알림과 같은 항목에 대한 정보도 포함합니다.
* [IoT Hub에서 장치 ID 관리][devguide-identities]에서는 각 IoT Hub의 ID 레지스트리에 어떤 정보가 저장되고 이것을 어떻게 액세스하고 수정할 수 있는지 설명합니다.
* [IoT Hub에 대한 액세스 제어][devguide-security]에서는 장치 및 클라우드 구성 요소에 대한 IoT Hub 기능에 액세스 권한을 부여하는 데 사용되는 보안 모델을 설명합니다. 이 문서에서는 토큰 및 X.509 인증서 사용에 대한 정보와 부여할 수 있는 권한 정보를 포함합니다.
* [장치 쌍을 사용하여 상태 및 구성 동기화][devguide-device-twins]에서는 *장치 쌍* 개념과 장치와 해당 장치 쌍을 사용한 장치 동기화와 같이 노출되는 기능을 설명합니다. 이 문서는 장치 쌍에 저장된 데이터에 대한 정보도 포함합니다.
* [장치에 직접 메서드 호출][devguide-directmethods]에서는 직접 메서드의 수명 주기, 백 엔드 앱에서 장치에 대한 메서드를 호출하고 장치에서 직접 메서드를 처리하는 방법에 대한 정보를 설명합니다.
* [여러 장치에서 작업 예약][devguide-jobs]에서는 여러 장치에서 작업을 예약하는 방법을 설명합니다. 이 문서에서는 직접 메서드 실행, 장치 쌍을 사용하여 장치 업데이트와 같은 태스크를 수행하는 작업을 제출하는 방법을 설명합니다. 또한 작업 상태를 쿼리하는 방법도 설명합니다.
* [참조 - IoT Hub 끝점][devguide-endpoints]에서는 각 IoT Hub가 런타임 및 관리 작업에 노출하는 다양한 끝점을 설명합니다. 이 문서에서는 필드 게이트웨이를 사용하여 일부 장치에서 IoT Hub 끝점에 연결할 수 있도록 하는 방법을 설명합니다.
* [참조 - 장치 쌍 및 작업용 IoT Hub 쿼리 언어][devguide-query]에서는 허브에서 장치 쌍 및 작업에 대한 정보를 검색할 수 있는 IoT Hub 쿼리 언어를 설명합니다.
* [참조 - 할당량 및 제한][devguide-quotas]에는 IoT Hub 서비스에 설정된 할당량과 할당량을 초과할 때 표시될 것으로 예상되는 제한 동작이 요약되어 잇습니다.
* [참조 – 가격 책정][devguide-pricing]에서는 IoT Hub의 가격 책정 및 다양한 SKU에 대한 일반적인 정보와 다양한 IoT Hub 기능이 IoT Hub에 의해 메시지로 요금제가 적용되는 방식에 대한 자세한 정보를 제공합니다.
* [참조 - 장치 및 서비스 SDK][devguide-sdks]에는 IoT Hub와 상호 작용하는 장치 및 서비스 응용 프로그램을 개발할 때 사용할 수 있는 Azure IoT SDK가 나열되어 있습니다. 이 문서에는 온라인 API 설명서에 대한 링크가 제공됩니다.
* [참조 - IoT Hub MQTT 지원][devguide-mqtt]에는 IoT Hub가 MQTT 프로토콜을 어떻게 지원하는지에 대한 자세한 정보가 제공됩니다. 이 문서에서는 Azure IoT SDK에 기본 제공되는 MQTT 프로토콜에 대한 지원을 설명하며 MQTT 프로토콜을 직접 사용하는 방법을 제공합니다.
* [용어집][devguide-glossary]은 일반적인 IoT Hub 관련 용어 목록입니다.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md




<!--HONumber=Nov16_HO5-->


