---
title: Defender IoT 마이크로 에이전트 문제 해결(미리 보기)
description: 예기치 않은 오류 또는 설명할 수 없는 오류를 처리하는 방법을 알아봅니다.
ms.date: 1/24/2021
ms.topic: reference
ms.openlocfilehash: 51550a4d3e5042fed7cadc4eac10a0074e954f19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782455"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Defender IoT 마이크로 에이전트 문제 해결(미리 보기)

예기치 않은 오류나 설명이 없는 오류가 발생한 경우 다음 문제 해결 방법을 사용하여 문제를 해결해 보세요. 또한 필요에 따라 Azure Defender for IoT 제품 팀에 연락하여 도움을 받을 수 있습니다.   

## <a name="service-status"></a>서비스 상태 

서비스의 상태를 보려면 다음을 수행합니다. 

1. 다음 명령을 실행합니다.

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. 서비스가 `active`인지 확인하여 서비스가 안정적이고, 프로세스의 작동 시간이 적절한지 확인합니다.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="서비스가 active 상태인지 확인하여 서비스가 안정적이고, 프로세스의 작동 시간이 적절한지 확인합니다.":::

서비스가 `inactive`로 표시되는 경우 다음 명령을 사용하여 서비스를 시작합니다.

```azurecli
systemctl start defender-iot-micro-agent.service 
```

프로세스 작동 시간이 너무 짧으면 서비스가 충돌하는 것을 알 수 있습니다. 이 문제를 해결하려면 로그를 검토해야 합니다.

## <a name="review-logs"></a>로그 검토 

다음 명령을 사용하여 루트 권한으로 Defender IoT 마이크로 에이전트 서비스가 실행 중인지 확인합니다.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Defender for IoT 마이크로 에이전트 서비스가 루트 권한으로 실행되고 있는지 확인합니다.":::

로그를 보려면 다음 명령을 사용합니다.  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

서비스를 다시 시작하려면 다음 명령을 사용합니다. 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>다음 단계

[기능 지원 및 사용 중지](edge-security-module-deprecation.md)를 확인하세요.
