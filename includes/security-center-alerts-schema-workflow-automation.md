---
title: 포함 파일
description: 포함 파일
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401072"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>스키마의 데이터 모델

|필드|데이터 형식|Description|
|----|----|----|
|**AlertDisplayName**|String|경고의 표시 이름입니다.|
|**AlertType**|String|경고의 유형입니다. 동일한 유형의 경고는 동일한 값을 가져야 합니다. 이 필드는 경고 인스턴스가 아닌 경고 유형을 나타내는 키 문자열입니다. 동일한 검색 논리/분석에서 생성되는 경고 인스턴스는 모두 동일한 경고 유형 값을 가져야 합니다.|
|**CompromisedEntity**|String|이 경고와 가장 관련이 높은 리소스의 표시 이름입니다.|
|**설명**|String|경고에 대한 설명입니다.|
|**EndTimeUtc**|DateTime|경고에 포함된 마지막 이벤트 또는 활동의 시간입니다.  이 필드는 UTC 표준 시간대 정보를 포함하여 ISO8601 형식을 준수하는 문자열이어야 합니다.|
|**엔터티**|IEnumerable(IEntity)|경고와 관련된 엔터티의 목록입니다. 이 목록에는 다양한 유형의 엔터티가 혼합되어 있을 수 있습니다. 엔터티 형식은 엔터티 섹션에 정의된 형식 중 하나일 수 있습니다. 아래 목록에 없는 엔터티를 보낼 수도 있지만 이러한 엔터티는 처리될 수 없습니다(경고는 새로운 유형의 엔터티를 사용하여 유효성 검사에 실패하지 않음).|
|**ExtendedProperties**|Dictionary(String,String)|공급자는 여기에 사용자 지정 필드를 포함할 수 있습니다(선택 사항).|
|**의도**|열거형|경고의 체인 종료 관련 의도입니다. 지원되는 값 목록 및 Azure Security Center에서 지원하는 체인 종료 의도에 대한 설명은 [의도](../articles/security-center/alerts-reference.md#intentions)를 참조하세요.<br/>이 필드에는 여러 값(쉼표로 구분)이 있을 수 있습니다.|
|**IsIncident**|Bool|이 필드는 경고가 인시던트(여러 경고의 그룹)인지 또는 단일 경고인지 결정합니다. 필드 기본값은 'false'입니다(단일 경고임을 나타냄).|
|**ProcessingEndTime**|DateTime|경고가 포함된 원래 제품의 최종 사용자가 경고에 액세스할 수 있는 시간입니다.|
|**ProductName**|String|이 경고를 게시한 제품의 이름입니다(Azure Security Center, Azure ATP, Microsoft Defender ATP, MCAS 등).|
|**RemediationSteps**|List<String>|경고를 수정하기 위해 수행하는 수동 작업 항목입니다.|
|**ResourceIdentifiers**|List(리소스 식별자)|이 경고의 리소스 식별자를 사용하여 경고를 올바른 제품 노출 그룹(테넌트, 작업 영역, 구독 등)으로 보낼 수 있습니다. 경고마다 다른 형식의 식별자가 여러 개 있을 수 있습니다.|
|**심각도**|열거형|공급자가 보고한 경고의 심각도입니다. 가능한 값은 정보, 낮음, 중간 및 높음입니다.|
|**StartTimeUtc**|DateTime|경고에 포함된 첫 번째 이벤트 또는 활동의 시간입니다. 이 필드는 UTC 표준 시간대 정보를 포함하여 ISO8601 형식을 준수하는 문자열이어야 합니다.|
|**상태**|열거형|경고의 수명 주기 상태입니다.<br/>지원되는 상태는 신규, 해결됨, 해제됨, 알 수 없음입니다.<br/>지원되는 옵션 이외의 값이 지정된 경고에는 '알 수 없음' 상태가 할당됩니다.<br/>값이 지정되지 않은 경고에는 '신규' 상태가 할당됩니다.|
|**SystemAlertId**|String|경고 식별자입니다.|
|**TimeGenerated**|DateTime|경고 공급자에 의해 경고가 생성된 시간입니다. 내부 경고 공급자가 보고하지 않을 경우 제품이 제품에서 처리를 위해 받은 시간을 할당하도록 선택할 수 있습니다.  이 필드는 UTC 표준 시간대 정보를 포함하여 ISO8601 형식을 준수하는 문자열이어야 합니다.|
|**VendorName**|String|경고를 제공하는 공급업체의 이름입니다.|
|||
