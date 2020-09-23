---
title: Azure Service Bus - 메시징 단위 자동 업데이트
description: 이 문서에서는 Service Bus 네임 스페이스의 메시징 단위 자동 업데이트를 사용 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 09/15/2020
ms.openlocfilehash: 0a72cc991e768a7bed01762d984cc56238ae0ad0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984648"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Azure Service Bus 네임스페이스의 메시징 단위 자동 업데이트 
자동 크기 조정을 사용하면 애플리케이션의 부하를 처리하기 위해 적절한 양의 리소스가 실행되도록 할 수 있습니다. 그러면 늘어난 부하를 처리하기 위해 리소스를 추가하고 유휴 상태의 리소스를 제거하여 비용을 절약할 수 있습니다. Azure Monitor의 자동 크기 조정 기능에 대해 자세히 알아보려면 [Microsoft Azure의 자동 크기 조정 개요](../azure-monitor/platform/autoscale-overview.md) 를 참조 하세요. 

Service Bus 프리미엄 메시지는 각 고객의 워크로드가 따로 실행되도록 CPU 및 메모리 수준에서 리소스 격리를 제공합니다. 이 리소스 컨테이너를 **메시징 단위**라고 합니다. 메시징 단위에 대 한 자세한 내용은 [Premium messaging Service Bus](service-bus-premium-messaging.md)를 참조 하세요. 

프리미엄 네임 스페이스 Service Bus의 자동 크기 조정 기능을 사용 하 여 [메시징 단위의](service-bus-premium-messaging.md) 최소 및 최대 수를 지정 하 고 규칙 집합에 따라 자동으로 메시징 단위를 추가 하거나 제거할 수 있습니다. 

예를 들어 자동 크기 조정 기능을 사용 하 여 Service Bus 네임 스페이스에 대해 다음과 같은 크기 조정 시나리오를 구현할 수 있습니다. 

- 네임 스페이스의 CPU 사용량이 75%를 초과 하는 경우 Service Bus 네임 스페이스에 대 한 메시징 단위를 늘립니다. 
- 네임 스페이스의 CPU 사용량이 25% 미만이 면 Service Bus 네임 스페이스에 대 한 메시징 단위를 줄입니다. 
- 업무 시간 중에는 업무 시간 외에 더 많은 메시징 단위를 사용 합니다. 

이 문서에서는 Azure Portal에서 Service Bus 네임 스페이스 ( [메시징 단위](service-bus-premium-messaging.md)업데이트)의 크기를 자동으로 조정 하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> 이 문서는 Azure Service Bus의 **프리미엄** 계층에만 적용됩니다. 

## <a name="autoscale-setting-page"></a>자동 크기 조정 설정 페이지
먼저 다음 단계에 따라 Service Bus 네임 스페이스에 대 한 **자동 크기 조정 설정** 페이지로 이동 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 합니다. 
2. 검색 표시줄에 **Service Bus**를 입력 하 고 드롭다운 목록에서 **Service Bus** 을 선택한 다음 **enter**키를 누릅니다. 
1. 네임 스페이스 목록에서 **premium 네임 스페이스** 를 선택 합니다. 
1. **크기 조정** 페이지로 전환 합니다. 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Service Bus 네임 스페이스-눈금 페이지":::

## <a name="manual-scale"></a>수동 크기 조정 
이 설정을 사용 하 여 네임 스페이스에 대 한 고정 된 수의 메시징 단위를 설정할 수 있습니다. 

1. **자동 크기 조정 설정** 페이지에서 **수동 크기 조정** 을 선택 합니다 (아직 선택 하지 않은 경우). 
1. **메시징 단위** 설정의 경우 드롭다운 목록에서 메시징 단위 수를 선택 합니다.
1. 도구 모음에서 **저장** 을 선택 하 여 설정을 저장 합니다. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="수동으로 메시징 단위 크기 조정":::       


## <a name="custom-autoscale---default-condition"></a>사용자 지정 자동 크기 조정-기본 조건
조건을 사용 하 여 메시징 단위의 자동 크기 조정을 구성할 수 있습니다. 이 크기 조정 조건은 다른 크기 조정 조건이 모두 일치 하지 않는 경우에 실행 됩니다. 다음 방법 중 하나로 기본 조건을 설정할 수 있습니다.

- 메트릭 (예: CPU 또는 메모리 사용량)을 기반으로 하는 크기 조정
- 특정 수의 메시징 단위로 크기 조정

기본 조건의 특정 날짜 또는 날짜 범위에서 자동 크기 조정을 위한 일정은 설정할 수 없습니다. 이 크기 조정 조건은 일정을 가진 다른 크기 조정 조건이 모두 일치 하지 않는 경우에 실행 됩니다. 

### <a name="scale-based-on-a-metric"></a>메트릭을 기준으로 크기 조정
다음 절차에서는 cpu 사용량이 75%를 초과 하는 경우 자동으로 메시징 단위 (규모 확장)를 증가 하는 조건을 추가 하 고 CPU 사용량이 25% 미만이 면 메시징 단위 (규모 축소)를 줄일 수 있는 방법을 보여 줍니다. 증분은 1 ~ 2, 2-4 및 4에서 8 사이에서 수행 됩니다. 마찬가지로, 감소는 8 ~ 4, 4-2 및 2에서 1로 수행 됩니다. 

1. **자동 크기 조정 설정** 페이지에서 **리소스 크기를 조정 하는 방법 선택** 옵션에 대해 **사용자 지정 자동 크기 조정** 을 선택 합니다. 
1. 페이지의 **기본** 섹션에서 기본 조건의 **이름을** 지정 합니다. **연필** 아이콘을 선택 하 여 텍스트를 편집 합니다. 
1. **크기 조정 모드**의 **메트릭을 기준으로 크기 조정** 을 선택 합니다. 
1. **+ 규칙 추가를**선택 합니다. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="기본-메트릭을 기반으로 하는 크기 조정":::    
1. **크기 조정 규칙** 페이지에서 다음 단계를 수행 합니다.
    1. **메트릭 이름** 드롭다운 목록에서 메트릭을 선택 합니다. 이 예제에서는 **CPU**입니다. 
    1. 연산자 및 임계값을 선택 합니다. 이 예제에서는 **메트릭 임계값이 크기 조정 작업을 트리거하기**위해 **75** **보다 큽니다** . 
    1. **작업 섹션에서** **작업** 을 선택 합니다. 이 예제에서는 **증가**하도록 설정 되어 있습니다. 
    1. 그런 다음 **추가** 를 선택 합니다.
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="기본-CPU 사용량이 75% 보다 큰 경우 스케일 아웃":::       

        > [!NOTE]
        > 이 예제에서 전체 CPU 사용량이 75%를 초과 하면 자동 크기 조정 기능에서 네임 스페이스에 대 한 메시징 단위를 늘립니다. 증분은 1 ~ 2, 2-4 및 4에서 8 사이에서 수행 됩니다. 
1. **+ 규칙 추가** 를 다시 선택 하 고 **크기 조정 규칙** 페이지에서 다음 단계를 수행 합니다.
    1. **메트릭 이름** 드롭다운 목록에서 메트릭을 선택 합니다. 이 예제에서는 **CPU**입니다. 
    1. 연산자 및 임계값을 선택 합니다. 이 예제에서는 **크기 조정 작업을 트리거하는 메트릭 임계값**에 대해 및 **25** **보다 작습니다** . 
    1. **작업 섹션에서** **작업** 을 선택 합니다. 이 예에서는 **감소**로 설정 됩니다. 
    1. 그런 다음 **추가** 를 선택 합니다. 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="기본-CPU 사용량이 25% 미만인 경우의 기본 크기 조정":::       

        > [!NOTE]
        > 이 예제에서 전체 CPU 사용량이 25% 미만이 면 자동 크기 조정 기능에서 네임 스페이스에 대 한 메시징 단위를 감소 시킵니다. 감소는 8 ~ 4, 4-2 및 2에서 1로 수행 됩니다. 
1. **최소** 및 **최대** 및 **기본** 메시징 단위 수를 설정 합니다.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="메트릭을 기반으로 하는 기본 규칙":::
1. 도구 모음에서 **저장** 을 선택 하 여 자동 크기 조정 설정을 저장 합니다. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>특정 수의 메시징 단위로 크기 조정
특정 수의 메시징 단위를 사용 하도록 네임 스페이스의 크기를 조정 하는 규칙을 구성 하려면 다음 단계를 수행 합니다. 다른 크기 조정 조건이 모두 일치 하지 않는 경우에도 기본 조건이 적용 됩니다. 

1. **자동 크기 조정 설정** 페이지에서 **리소스 크기를 조정 하는 방법 선택** 옵션에 대해 **사용자 지정 자동 크기 조정** 을 선택 합니다. 
1. 페이지의 **기본** 섹션에서 기본 조건의 **이름을** 지정 합니다. 
1. **크기 조정 모드**의 **특정 메시징 단위로 크기 조정을** 선택 합니다. 
1. **메시징 단위**에서 기본 메시징 단위 수를 선택 합니다. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="기본-특정 메시징 단위로 확장":::       

## <a name="custom-autoscale---additional-conditions"></a>사용자 지정 자동 크기 조정-추가 조건
이전 섹션에서는 자동 크기 조정 설정에 대 한 기본 조건을 추가 하는 방법을 보여 줍니다. 이 섹션에서는 자동 크기 조정 설정에 조건을 추가 하는 방법을 보여 줍니다. 이러한 추가 기본이 아닌 조건의 경우 특정 요일 또는 날짜 범위를 기준으로 일정을 설정할 수 있습니다. 

### <a name="scale-based-on-a-metric"></a>메트릭을 기준으로 크기 조정
1. **자동 크기 조정 설정** 페이지에서 **리소스 크기를 조정 하는 방법 선택** 옵션에 대해 **사용자 지정 자동 크기 조정** 을 선택 합니다. 
1. **기본** 블록 아래에서 **크기 조정 조건 추가를** 선택 합니다. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="사용자 지정-크기 조정 조건 추가 링크":::    
1. 조건 **이름을** 지정 합니다. 
1. **메트릭 기반 크기 조정** 옵션을 선택 했는지 확인 합니다. 
1. 전체 CPU 사용량이 75%를 초과 하면 규칙을 추가 하 여 메시징 단위를 늘리는 규칙 추가 **를 선택 합니다** . [기본 조건](#custom-autoscale---default-condition) 섹션의 단계를 따릅니다. 
5. **최소** 및 **최대** 및 **기본** 메시징 단위 수를 설정 합니다.
6. 사용자 지정 조건에 대 한 **일정** 을 설정할 수도 있습니다 (기본 조건에는 없음). 조건에 대 한 시작 날짜와 종료 날짜를 지정 하거나 한 주의 특정 일 (월요일, 화요일 등)을 선택할 수 있습니다. 
    1. **시작/종료 날짜 지정**을 선택 하는 경우 적용 되는 조건에 대 한 **표준 시간대**, **시작 날짜 및 시간** , **종료 날짜 및 시간** (다음 이미지에 표시 됨)을 선택 합니다. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="메시징 단위 수에 대 한 최소값, 최대값 및 기본값":::
    1. **특정 일 반복**을 선택 하는 경우 조건을 적용 해야 하는 요일, 표준 시간대, 시작 시간 및 종료 시간을 선택 합니다. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="특정 일 반복":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>특정 수의 메시징 단위로 크기 조정
1. **자동 크기 조정 설정** 페이지에서 **리소스 크기를 조정 하는 방법 선택** 옵션에 대해 **사용자 지정 자동 크기 조정** 을 선택 합니다. 
1. **기본** 블록 아래에서 **크기 조정 조건 추가를** 선택 합니다. 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="사용자 지정-크기 조정 조건 추가 링크":::    
1. 조건 **이름을** 지정 합니다. 
2. **크기 조정 모드**의 **특정 메시징 단위에 맞게 크기 조정 옵션을** 선택 합니다. 
1. 드롭다운 목록에서 **메시징 단위** 수를 선택 합니다. 
6. **일정**에서 조건에 대 한 시작 날짜와 종료 날짜를 지정 하거나, 특정 요일 (월요일, 화요일 등)을 주 및 시간 중에서 선택 합니다. 
    1. **시작/종료 날짜 지정**을 선택 하는 경우 조건이 적용 될 **표준 시간대**, **시작 날짜 및 시간** 및 **종료 날짜와 시간** 을 선택 합니다. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="특정 메시징 단위로 크기 조정-시작 및 종료 날짜":::        
    1. **특정 일 반복**을 선택 하는 경우 조건을 적용 해야 하는 요일, 표준 시간대, 시작 시간 및 종료 시간을 선택 합니다.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="특정 메시징 단위로 확장-특정 일 반복":::

> [!IMPORTANT]
> 자동 크기 조정 설정의 작동 방식, 특히 프로필 또는 조건을 선택 하 고 여러 규칙을 평가 하는 방법에 대해 자세히 알아보려면 [자동 크기 조정 설정 이해](../azure-monitor/platform/autoscale-understanding-settings.md)를 참조 하세요.          

## <a name="next-steps"></a>다음 단계
메시징 단위에 대한 자세한 내용은 [프리미엄 메시징](service-bus-premium-messaging.md)을 참조하세요.

