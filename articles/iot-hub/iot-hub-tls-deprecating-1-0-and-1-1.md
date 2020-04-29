---
title: IoT Hub의 사용 중단 TLS 1.0 및 1.1 Microsoft Docs
description: IoT Hub에서 TLS 1.0 및 1.1 및 지원 되는 암호화의 사용 중단에 대 한 지침을 설명 합니다.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381307"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>IoT Hub에서 TLS 1.0 및 1.1의 사용 중단

최상의 암호화를 제공 하기 위해 IoT 장치 및 서비스에 대 한 선택의 암호화 메커니즘으로 TLS (Transport Layer Security) 1.2로 이동 IoT Hub. 

## <a name="timeline"></a>타임라인

IoT Hub는 추가 공지가 있을 때까지 TLS 1.0/1.1을 계속 지원 합니다. 그러나 모든 고객은 가능한 한 빨리 TLS 1.2로 마이그레이션하는 것이 좋습니다.

## <a name="supported-ciphers"></a>지원 되는 암호화

TLS 핸드셰이크에서 사용 되는 다양 한 암호의 가용성에 대 한 타임 라인은 다음과 같습니다.

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (현재 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (2020의 하반기에서 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (2020의 하반기에서 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (2020의 하반기에서 지원 됨)

## <a name="customer-feedback"></a>고객 의견

TLS 1.2을 적용 하는 것은 업계 전체의 최고 수준의 암호화 선택 이며 계획 대로 사용 하도록 설정 되지만, microsoft는 특정 배포와 관련 하 여 TLS 1.2을 채택 하는 데 관련 된 문제에 대 한 고객의 의견을 듣고 싶습니다. 이러한 목적을 위해에 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)주석을 보낼 수 있습니다.
