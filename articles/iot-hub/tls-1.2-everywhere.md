---
title: IoT Hub 및 DPS (장치 프로 비전 서비스)에서 사용 중단 TLS 1.0 및 1.1 | Microsoft Docs
description: IoT Hub 및 DPS에서 TLS 1.0 및 1.1 및 지원 되는 암호의 사용 중단에 대 한 지침입니다.
author: rezas
ms.author: reza
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f1ee3156e5639ae47d5bb323cf992586580668f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485911"
---
# <a name="tls-10-and-11-deprecation-in-iot-hub-and-device-provisioning-service"></a>IoT Hub 및 장치 프로 비전 서비스에서 TLS 1.0 및 1.1 사용 중단

최상의 암호화를 제공 하기 위해 IoT Hub 및 DPS (장치 프로 비전 서비스)는 IoT 장치 및 서비스에 대 한 선택의 암호화 메커니즘으로 TLS (Transport Layer Security) 1.2로 이동 됩니다. 따라서 TLS 1.0 및 TLS 1.1 뿐만 아니라 권장 되지 않는 여러 레거시 암호화에 대 한 레거시 지원은 **2020 년 7 월 1 일부 터**더 이상 사용 되지 않습니다.


## <a name="impact"></a>영향
고객의 특정 환경 및 구성을 기반으로 하는 TLS 1.0 및 1.1 및 권장 되지 않는 레거시 암호의 사용 중단은 IoT 장치 및 IoT Hub 또는 DPS와 통신 하는 서비스에 대 한 변경 일 수 있습니다. 경우에 따라 이러한 변경과 호환 되지 않는 장치 및 서비스는 앞서 언급 한 휴가 날짜 이후 IoT Hub 또는 DPS에 연결할 수 없습니다.


## <a name="supported-ciphers"></a>지원 되는 암호화

TLS 핸드셰이크 중에는 다음 암호화만 허용 됩니다.

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>고객 의견

TLS 1.2 적용이 업계 전반에서 최상의 암호화 선택이며 계획된 대로 구현될 예정이지만, Microsoft는 TLS 1.2를 채택하는 특정 배포 및 관련 어려움에 대해 고객의 의견을 듣고자 합니다. 이러한 목적을 위해 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)에 주석을 보낼 수 있습니다.