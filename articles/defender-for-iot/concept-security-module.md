---
title: Defender-마이크로 에이전트 및 장치 쌍
description: Defender-마이크로 에이전트 쌍의 개념과 IoT 용 Defender에서이를 사용 하는 방법에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 552da329b90b102a13ef53158ec81be87684c1fc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493189"
---
# <a name="defender-iot-micro-agent"></a>Defender-마이크로 에이전트

이 문서에서는 Defender for IoT에서 장치 쌍 및 모듈을 사용 하는 방법을 설명 합니다.

## <a name="device-twins"></a>디바이스 쌍

Azure에 구축된 IoT 솔루션의 경우 디바이스 쌍은 디바이스 관리와 프로세스 자동화 모두에서 중요한 역할을 수행합니다.

Defender for IoT는 기존 IoT 디바이스 관리 플랫폼과 완벽하게 통합되어 디바이스 보안 상태를 관리하고 기존 디바이스 제어 기능을 활용할 수 있도록 지원합니다. 통합은 IoT Hub 쌍 메커니즘을 사용하여 구현됩니다.

Azure IoT Hub에서 [장치](../iot-hub/iot-hub-devguide-device-twins.md) 쌍의 개념에 대해 자세히 알아보세요.

## <a name="defender-iot-micro-agent-twins"></a>Defender-마이크로 에이전트 쌍

IoT 용 defender는 서비스의 각 장치에 대 한 Defender IoT-마이크로 에이전트 쌍을 유지 관리 합니다.
Defender-IoT-마이크로 에이전트 쌍은 솔루션의 각 특정 장치에 대 한 장치 보안과 관련 된 모든 정보를 포함 합니다.
장치 보안 속성은 안전 하 게 통신 하 고 더 적은 리소스를 필요로 하는 업데이트 및 유지 관리를 가능 하 게 하기 위해 전용 Defender-IoT-마이크로 에이전트 쌍에서 유지 관리 됩니다.

쌍을 만들고 사용자 지정 하 고 구성 하는 방법에 대해 알아보려면 [Defender-IoT-마이크로 에이전트 쌍 만들기](quickstart-create-security-twin.md) 및 [보안 에이전트 구성](how-to-agent-configuration.md) 을 참조 하세요. IoT Hub에서 모듈 쌍의 개념에 대해 자세히 알아보려면 [모듈 쌍 이해](../iot-hub/iot-hub-devguide-module-twins.md) 를 참조 하세요.

## <a name="see-also"></a>참고 항목

- [IoT 용 Defender 개요](overview.md)
- [보안 에이전트 배포](how-to-deploy-agent.md)
- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md)