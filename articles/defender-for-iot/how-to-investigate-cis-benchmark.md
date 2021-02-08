---
title: CIS 벤치 마크 권장 사항 조사
titleSuffix: Azure Defender for IoT
description: OS 기준 권장 사항을 기반으로 하는 기본 및 고급 조사를 수행 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809836"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>OS 기준 (CIS 벤치 마크 기반) 권장 사항 조사 

OS 기준 권장 사항을 기반으로 하는 기본 및 고급 조사를 수행 합니다.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>기본 OS 기준 보안 권장 사항 조사  

**IoT Hub** 아래에서 IoT 포털 용 Azure Defender로 이동 하 여 OS 기준 권장 사항을 조사할 수 있습니다. 자세한 내용은 [보안 권장 사항을 조사](quickstart-investigate-security-recommendations.md)하는 방법을 참조 하세요.

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>고급 OS 기준 보안 권장 조사  

이 섹션에서는 OS 기준 테스트 결과를 보다 잘 이해 하 고 Azure Log Analytics에서 이벤트를 쿼리 하는 방법을 설명 합니다.  

고급 OS 기준 보안 권장 조사는 log analytics를 사용 하는 경우에만 지원 됩니다. 계속 하기 전에 IoT 용 Defender를 Log Analytics 작업 영역에 연결 합니다. 고급 OS 기준 보안 권장 사항에 대 한 자세한 내용은 [IoT 에이전트 기반 솔루션용 Azure Defender를 구성](how-to-configure-agent-based-solution.md)하는 방법을 참조 하세요.

Log Analytics에서 경고에 대 한 IoT 보안 이벤트를 쿼리하려면:

1. **경고** 페이지로 이동 합니다.

1. **Log Analytics 작업 영역에서 권장 사항 조사를** 선택 합니다.

Log Analytics에서 IoT 보안 이벤트를 쿼리하려면 권장 사항:

1. **권장 사항** 페이지로 이동 합니다.

1. **Log Analytics 작업 영역에서 권장 사항 조사를** 선택 합니다.

1. **권장 사항 세부 정보** 빠른 보기 페이지에서 **작업 시스템 (OS) 기준 규칙 세부 정보 표시** 를 선택 하 여 특정 장치에 대 한 세부 정보를 확인 합니다.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="특정 장치에 대 한 세부 정보를 확인 합니다."::: 

Log Analytics 작업 영역에서 직접 IoT 보안 이벤트를 쿼리하려면:

1. **로그** 페이지로 이동 합니다.

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="왼쪽 창에서 로그를 선택 합니다.":::

1. **경고 조사** 를 선택 하거나 보안 권장 사항 또는 경고에서 **Log Analytics에서 경고 조사** 옵션을 선택 합니다.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>OS 기준 리소스를 조사 하는 데 유용한 쿼리: 

> [!Note]
> `<device-id>`다음 쿼리 각각에서 장치를 제공한 이름으로 바꿔야 합니다. 


### <a name="retrieve-the-latest-information"></a>최신 정보를 검색 합니다.

- **장치 작업 실패**: 다음 쿼리를 실행 하 여 장치에서 실패 한 검사에 대 한 최신 정보를 검색 합니다. 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **특정 장치 오류** -다음 쿼리를 실행 하 여 특정 장치에서 실패 한 검사에 대 한 최신 정보를 검색 합니다.  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **특정 장치 오류** -이 쿼리를 실행 하 여 특정 장치에서 오류가 발생 한 검사에 대 한 최신 정보를 검색 합니다. 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **특정 확인에 실패 한 장치에 대 한 장치 목록 업데이트** -특정 확인에 실패 한 장치에서 업데이트 된 목록을 검색 하려면이 쿼리를 실행 합니다.  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>다음 단계

[보안 권장 사항을 조사](quickstart-investigate-security-recommendations.md)합니다.
 