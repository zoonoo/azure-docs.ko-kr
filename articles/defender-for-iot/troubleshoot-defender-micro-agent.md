---
title: Defender IoT 마이크로 에이전트 문제 해결
titleSuffix: Azure Defender for IoT
description: 예기치 않거나 설명이 없는 오류를 처리 하는 방법을 알아봅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: dade0d0d5dc4d690ea94f20deaf956b1e079bad7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124179"
---
# <a name="defender-iot-micro-agent-troubleshooting"></a>Defender IoT 마이크로 에이전트 문제 해결 

예기치 않은 오류나 설명이 없는 오류가 발생 한 경우 다음 문제 해결 방법을 사용 하 여 문제를 해결 해 보십시오. 또한 필요에 따라 Azure Defender for IoT 제품 팀에 연락 하 여 도움을 받을 수 있습니다.   

## <a name="service-status"></a>서비스 상태 

서비스의 상태를 보려면 다음을 수행 합니다. 

1. 다음 명령을 실행합니다.

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. 서비스가 안정적이 `active` 고 프로세스의 작동 시간이 적절 한지 확인 합니다.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="서비스가 활성 상태이 고 작동 시간이 적절 한지 확인 하 여 안정적인 지 확인 합니다.":::

서비스가로 표시 되는 경우 `inactive` 다음 명령을 사용 하 여 서비스를 시작 합니다.

```azurecli
systemctl start defender-iot-micro-agent.service 
```

프로세스 작동 시간이 너무 짧으면 서비스가 충돌 하는 것을 알 수 있습니다. 이 문제를 해결 하려면 로그를 검토 해야 합니다.

## <a name="review-logs"></a>로그 검토 

다음 명령을 사용 하 여 루트 권한으로 Defender IoT 마이크로 에이전트 서비스가 실행 중인지 확인 합니다.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="IoT 마이크로 에이전트 서비스가 루트 권한으로 실행 되 고 있는지 확인 합니다.":::

로그를 보려면 다음 명령을 사용 합니다.  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

서비스를 다시 시작 하려면 다음 명령을 사용 합니다. 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>다음 단계

[기능 지원 및](edge-security-module-deprecation.md)사용 중지를 확인 하세요.
