---
title: IoT용 Azure 보안 센터를 사용하여 데이터에 액세스| 마이크로 소프트 문서
description: IoT용 Azure 보안 센터를 사용할 때 보안 경고 및 권장 사항에 액세스하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597188"
---
# <a name="access-your-security-data"></a>보안 데이터에 액세스 

IoT용 Azure 보안 센터는 로그 분석 작업 영역에 보안 경고, 권장 사항 및 원시 보안 데이터를 저장합니다.

## <a name="log-analytics"></a>Log Analytics

사용되는 로그 분석 작업 영역을 구성하려면 다음을 수행하십시오.

1. IoT Hub를 엽니다.
1. **보안** 섹션 아래의 개요 블레이드를 **클릭합니다.**
2. **설정을**클릭하고 로그 분석 작업 영역 구성을 변경합니다.

구성 후 로그 애널리틱스 작업 영역에서 경고 및 권장 사항에 액세스하려면 다음을 수행하십시오.

1. IoT용 Azure 보안 센터에서 경고 또는 권장 사항을 선택합니다. 
2. **추가 조사를**클릭한 다음 을 클릭하여 **이 경고가 있는 장치를 보려면 여기를 클릭하고 DeviceId 열을 봅니다.**

로그 분석에서 데이터 쿼리에 대한 자세한 내용은 [로그 분석의 쿼리로 시작하기](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)를 참조하십시오.

## <a name="security-alerts"></a>보안 경고

보안 경고는 IoT 솔루션용 Azure 보안 센터에 대해 구성된 로그 분석 작업 영역의 _AzureSecurityOfThings.SecurityAlert_ 테이블에 저장됩니다.

보안 경고를 탐색하는 데 도움이 되는 유용한 쿼리가 많이 제공되었습니다.

### <a name="sample-records"></a>샘플 레코드

몇 개의 임의 레코드 선택

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | 이오트허브                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 설명                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 무차별 암호 대입 공격 성공           | 장치에 대한 무차별 암호 대입 공격이 성공했습니다.        |    \"{ "전체 소스 주소": "[ 10.165.12.18:\"],\"\""사용자 이름": "[ ], "DeviceId": "IoT-장치-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 성공적인 로컬 로그인      | 장치에 대한 성공적인 로컬 로그인이 검색되었습니다.     | { "원격 주소": ""?", "원격 포트": """",", "로컬 포트": """,,"로그인 쉘": "/bin/su", "로그인 프로세스 ID": "28207", "사용자 이름": "공격자", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 로컬 로그인 시도 실패  | 장치에 대한 로컬 로그인 시도가 실패한 경우 |  { "원격 주소": ""?", "원격 포트": """",", "로컬 포트": """,", "로그인 쉘": "/bin/su", "로그인 프로세스 ID": "22644", "사용자 이름": "공격자", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>장치 요약

IoT Hub, 장치, 경고 심각도, 경고 유형별로 그룹화하여 지난 주에 감지된 고유한 보안 경고 수를 가져옵니다.

```
// Get the number of distinct security alerts detected in the last week, grouped by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| 이오트허브                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 개수 |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 무차별 암호 대입 공격 성공           | 9   |   
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 장치에서 로컬 로그인 시도 실패  | 242 |    
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 장치에서 성공적인 로컬 로그인      | 31  |
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 암호화 동전 광부                     | 4   |

### <a name="iot-hub-summary"></a>IoT 허브 요약

IoT Hub, 경고 심각도, 경고 유형별로 지난 주에 경고가 있는 여러 개의 고유 장치를 선택합니다.

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| 이오트허브                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 무차별 암호 대입 공격 성공           | 1          |    
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | 중간        | 장치에서 로컬 로그인 시도 실패  | 1          | 
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 장치에서 성공적인 로컬 로그인      | 1          |
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | 중간        | 암호화 동전 광부                     | 1          |

## <a name="security-recommendations"></a>보안 권장 사항

보안 권장 사항은 _AzureSecurityOfThings.Security_ IoT 솔루션용 Azure 보안 센터에 대해 구성된 로그 분석 작업 영역의 추천 테이블에 저장됩니다.

보안 권장 사항을 탐색하는 데 도움이 되는 유용한 쿼리가 많이 제공되었습니다.

### <a name="sample-records"></a>샘플 레코드

몇 개의 임의 레코드 선택

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | 이오트허브 | deviceId | 권장 사항 심각도 | 추천상태 | 추천표시이름 | 설명 | 권장 사항추가데이터 |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간 | Active | 입력 체인에서 허용 방화벽 규칙이 발견되었습니다. | 광범위한 IP 주소 또는 포트에 대한 허용 패턴이 포함된 방화벽의 규칙이 발견되었습니다. | {"규칙":"{{\"소스\"\"\"주소\":\"\"\",\"소스\"\"\"포트\":\"\", 대상\"주소 : , 대상 포트 : 1337 }"} |
| 2019-03-22T10:50:27.237 | /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간 | Active | 입력 체인에서 허용 방화벽 규칙이 발견되었습니다. | 광범위한 IP 주소 또는 포트에 대한 허용 패턴이 포함된 방화벽의 규칙이 발견되었습니다. | {"규칙":"{{\"소스\"\"\"주소\":\"\"\",\"소스\"\"\"포트\":\"\", 대상\"주소 : , 대상 포트 : 1337 }"} |

### <a name="device-summary"></a>장치 요약

IoT Hub, 장치, 권장 사항 심각도 및 유형별로 그룹화된 고유한 활성 보안 권장 사항 수를 가져옵니다.

```
// Get the number of distinct active security recommendations, grouped by by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| 이오트허브                                                                                                       | deviceId      | 권장 사항 심각도 | 개수 |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 2   |    
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 1 |  
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 1  |
| /구독/<subscription_id>/리소스그룹/<resource_group>/공급자/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 4   |


## <a name="next-steps"></a>다음 단계

- IoT에 대한 Azure 보안 센터 [개요](overview.md) 읽기
- IoT [아키텍처를](architecture.md) 위한 Azure 보안 센터에 대해 자세히 알아보기
- [IoT 경고를 위한 Azure 보안 센터](concept-security-alerts.md) 이해 및 탐색
- [IoT 권장 사항에 대한 Azure 보안 센터](concept-recommendations.md) 이해 및 탐색
