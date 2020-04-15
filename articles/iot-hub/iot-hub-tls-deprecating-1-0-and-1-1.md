---
title: IoT 허브에서 TLS 1.0 및 1.1 을 더 이상 사용되지 않는 | 마이크로 소프트 문서
description: TLS 1.0 및 1.1의 사용 중단에 관한 지침과 IoT Hub에서 지원되는 암호입니다.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381307"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>IoT Hub에서 TLS 1.0 및 1.1 사용 중단

동급 최강의 암호화를 제공하기 위해 IoT Hub는 IoT 장치 및 서비스에 대해 선택한 암호화 메커니즘으로 TLS(전송 계층 보안) 1.2로 이동하고 있습니다. 

## <a name="timeline"></a>타임라인

IoT Hub는 추후 공지가 있을 때까지 TLS 1.0/1.1을 계속 지원합니다. 그러나 모든 고객이 가능한 한 빨리 TLS 1.2로 마이그레이션하는 것이 좋습니다.

## <a name="supported-ciphers"></a>지원되는 암호

TLS 핸드셰이크에 사용되는 다양한 암호의 가용성에 대한 타임라인은 다음과 같습니다.

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256(현재 지원)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384(2020년 하반기 지원)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256(2020년 하반기 지원)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384(2020년 하반기 지원)

## <a name="customer-feedback"></a>고객 의견

TLS 1.2 시행은 업계 전반에 가장 적합한 동급 최고의 암호화 선택이며 계획대로 활성화될 예정이지만, TLS 1.2를 채택하는 데 따른 특정 배포 및 어려움에 대해 고객으로부터 여전히 듣고 싶습니다. 이를 위해 에 대한 의견을 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)보낼 수 있습니다.
