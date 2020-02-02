---
title: 문제 해결 Azure IoT Hub 오류 504101 Gtimeout
description: 504101 게이트웨이 시간 초과 오류 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960673"
---
# <a name="504101-gatewaytimeout"></a>504101 게이트웨이 시간 제한

이 문서에서는 **504101 게이트웨이 시간 제한** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

IoT Hub에서 장치에 직접 메서드를 호출 하려고 하면 요청이 실패 하 고 **504101 Gtimeout**오류가 발생 합니다.

## <a name="cause"></a>원인

### <a name="cause-1"></a>원인 1

IoT Hub 오류가 발생 하 여 시간 제한이 초과 되기 전에 직접 메서드가 완료 되었는지 확인할 수 없습니다.

### <a name="cause-2"></a>원인 2

이전 버전의 Azure IoT C# SDK (< 1.19.0)를 사용 하는 경우 버그로 인해 장치와 IoT Hub 간의 amqp 링크를 자동으로 삭제할 수 있습니다.

## <a name="solution"></a>솔루션

### <a name="solution-1"></a>해결 방법 1

다시 시도를 실행 합니다.

### <a name="solution-2"></a>해결 방법 2

최신 버전의 Azure IOT C# SDK로 업그레이드 합니다.