---
title: IoT Hub 및 DPS (장치 프로 비전 서비스)의 사용 중단 TLS 1.0 및 1.1 | Microsoft Docs
description: IoT Hub 및 DPS에서 TLS 1.0 및 1.1 및 지원 되는 암호의 사용 중단에 대 한 지침입니다.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912157"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>IoT Hub 및 장치 프로 비전 서비스에서 TLS 1.0 및 1.1의 사용 중단

최상의 암호화를 제공 하기 위해 IoT Hub 및 DPS (장치 프로 비전 서비스)는 IoT 장치 및 서비스에 대 한 선택의 암호화 메커니즘으로 TLS (Transport Layer Security) 1.2로 이동 됩니다. 따라서 TLS 1.0 및 TLS 1.1 뿐만 아니라 권장 되지 않는 여러 레거시 암호화에 대 한 레거시 지원은 **2020 년 7 월 1 일부 터**더 이상 사용 되지 않습니다.


## <a name="impact"></a>영향
고객의 특정 환경 및 구성을 기반으로 하는 TLS 1.0 및 1.1 및 권장 되지 않는 레거시 암호의 사용 중단은 IoT 장치 및 IoT Hub 또는 DPS와 통신 하는 서비스에 대 한 변경 일 수 있습니다. 경우에 따라 이러한 변경과 호환 되지 않는 장치 및 서비스는 앞서 언급 한 휴가 날짜 이후 IoT Hub 또는 DPS에 연결할 수 없습니다.


## <a name="supported-ciphers"></a>지원 되는 암호화

TLS 핸드셰이크에서 사용 되는 다양 한 암호의 가용성에 대 한 타임 라인은 다음과 같습니다.

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (현재 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (2020의 하반기에서 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (2020의 하반기에서 지원 됨)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (2020의 하반기에서 지원 됨)


## <a name="customer-feedback"></a>고객 의견

TLS 1.2을 적용 하는 것은 업계 전체의 최고 수준의 암호화 선택 이며 계획 대로 사용 하도록 설정 되지만, microsoft는 특정 배포와 관련 하 여 TLS 1.2을 채택 하는 데 관련 된 문제에 대 한 고객의 의견을 듣고 싶습니다. 이러한 목적을 위해 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)에 주석을 보낼 수 있습니다.
