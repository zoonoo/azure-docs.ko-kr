---
title: IoT Hub 및 DPS (장치 프로 비전 서비스)의 사용 중단 TLS 1.0 및 1.1 | Microsoft Docs
description: IoT Hub 및 DPS에서 TLS 1.0 및 1.1 및 지원 되는 암호의 사용 중단에 대 한 지침입니다.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402797"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>IoT Hub 및 장치 프로 비전 서비스에서 TLS 1.0 및 1.1의 사용 중단

최상의 암호화를 제공 하기 위해 IoT Hub 및 DPS (장치 프로 비전 서비스)는 IoT 장치 및 서비스에 대 한 선택의 암호화 메커니즘으로 TLS (Transport Layer Security) 1.2로 이동 됩니다. 

## <a name="supported-ciphers"></a>지원 되는 암호화

TLS 핸드셰이크에서 사용 되는 다양 한 암호의 가용성에 대 한 타임 라인은 다음과 같습니다.

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (현재 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (2020의 하반기에서 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (2020의 하반기에서 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (2020의 하반기에서 지원 됨)


## <a name="customer-feedback"></a>고객 의견

TLS 1.2을 적용 하는 것은 업계 전체의 최고 수준의 암호화 선택 이며 계획 대로 사용 하도록 설정 되지만, microsoft는 특정 배포와 관련 하 여 TLS 1.2을 채택 하는 데 관련 된 문제에 대 한 고객의 의견을 듣고 싶습니다. 이러한 목적을 위해 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)에 주석을 보낼 수 있습니다.
