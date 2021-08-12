---
title: Azure IoT Hub 오류 504101 GatewayTimeout 문제 해결
description: 504101 GatewayTimeout 오류 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 5d5962c43a8a3cd97b31ad3fa50bce774e2626cb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060944"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

이 문서에서는 **504101 GatewayTimeout** 오류에 대한 원인과 솔루션을 설명합니다.

## <a name="symptoms"></a>증상

IoT Hub에서 장치에 직접 메서드를 호출하려고 하면 **504101 GatewayTimeout** 오류가 발생하며 요청이 실패합니다.

## <a name="cause"></a>원인

### <a name="cause-1"></a>원인 1

IoT Hub 오류가 발생하여 시간이 초과되기 전에 직접 메서드가 완료되었는지 확인할 수 없습니다.

### <a name="cause-2"></a>원인 2

이전 버전의 Azure IoT C# SDK(<1.19.0)를 사용하는 경우 버그로 인해 장치와 IoT Hub 간의 AMQP 링크가 자동으로 삭제될 수 있습니다.

## <a name="solution"></a>솔루션

### <a name="solution-1"></a>해결 방법 1

다시 시도를 실행합니다.

### <a name="solution-2"></a>해결 방법 2

최신 버전의 Azure IOT C# SDK로 업그레이드합니다.