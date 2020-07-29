---
title: 문제 해결 Azure IoT Hub 오류 409002 LinkCreationConflict
description: 409002 오류를 해결 하는 방법 이해 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758748"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

이 문서에서는 **409002 LinkCreationConflict** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

장치 연결 끊기 또는 클라우드-장치 메시지 오류와 함께 진단 로그에 **LinkCreationConflict 오류 409002** 이 표시 됩니다. 

<!-- When using AMQP? -->

## <a name="cause"></a>원인

일반적으로이 오류는 IoT Hub 클라이언트에 두 개 이상의 연결이 있는 경우에 발생 합니다. 실제로 기존 연결을 사용 하 여 장치에 대 한 새 연결 요청이 도착 하면 IoT Hub이 오류가 발생 한 기존 연결을 닫습니다.

### <a name="cause-1"></a>원인 1

가장 일반적인 경우에는 별도의 문제 (예: [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md))로 인해 장치의 연결이 끊깁니다. 장치는 연결을 즉시 다시 설정 하려고 하지만, IoT Hub 여전히 장치를 연결 된 것으로 간주 합니다. IoT Hub는 이전 연결을 닫고 이 오류를 기록합니다.

### <a name="cause-2"></a>원인 2

장치 측 논리에 결함이 있으면 장치가 이미 열려 있는 경우 연결을 설정 하 게 됩니다.

## <a name="solution"></a>솔루션

이 오류는 대부분 다른 일시적인 문제의 부작용으로 나타나므로 추가로 문제를 해결하려면 로그에서 다른 오류를 검색합니다. 그렇지 않으면, 연결이 끊어질 경우만 새 연결 요청을 발급해야 합니다.
