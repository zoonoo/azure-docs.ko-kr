---
title: IoT Hub에서 TLS 1.0 및 1.1 사용 중단 | Microsoft Docs
description: IoT Hub에서 TLS 1.0 및 1.1 사용 중단 및 지원되는 암호화에 대한 지침을 설명합니다.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: fcf7620f53c9bfdb51eb62598f2c8b441574eca6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006083"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>IoT Hub에서 TLS 1.0 및 1.1 사용 중단

최상의 암호화를 제공하기 위해 IoT Hub는 IoT 디바이스 및 서비스에 대한 암호화 메커니즘 선택을 TLS(Transport Layer Security) 1.2로 이동합니다. 

## <a name="timeline"></a>타임라인

IoT Hub는 추가 공지가 있을 때까지 TLS 1.0/1.1을 계속 지원합니다. 그러나 모든 고객은 가능한 한 빨리 TLS 1.2로 마이그레이션할 것을 권장합니다.

## <a name="deprecating-tls-11-ciphers"></a>TLS 1.1 암호 사용 중단

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>TLS 1.0 암호 사용 중단

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-cipher-suites"></a>TLS 1.2 암호 그룹

[IOT HUB TLS 1.2 암호 그룹](iot-hub-tls-support.md#cipher-suites)을 참조 하세요.
 
## <a name="customer-feedback"></a>고객 의견

TLS 1.2 적용은 업계 최고 수준의 암호화 선택이며 계획대로 추진되지만, Microsoft에서는 고객으로부터 특정 배포에 대한 의견과 TLS 1.2 채택 관련 어려움을 듣고자 합니다. 이러한 목적으로 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)에 의견을 보내실 수 있습니다.
