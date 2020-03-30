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
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272605"
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

|필드|데이터 형식|설명|
|----|----|----|
|**경고표시이름**|String|경고의 표시 이름입니다.|
|**AlertType**|String|경고 유형입니다. 동일한 형식의 경고는 동일한 값을 가져야 합니다. 이 필드는 경고 인스턴스가 아닌 경고 유형을 나타내는 키문자열입니다. 동일한 검색 논리/분석의 모든 경고 인스턴스는 경고 유형에 대해 동일한 값을 가져야 합니다.|
|**손상된 엔터티**|String|이 경고와 가장 관련이 있는 리소스의 표시 이름입니다.|
|**설명**|String|경고에 대한 설명입니다.|
|**엔드타임유트**|DateTime|경고에 포함된 마지막 이벤트 또는 활동의 시간입니다.  필드는 UTC 표준 시간대 정보를 포함하여 ISO8601 형식을 준수하는 문자열이어야 합니다.|
|**Entities**|이에이넘너어블 (아이엔티티)|경고와 관련된 엔터티 목록입니다. 이 목록에는 다양한 유형의 엔터티가 혼합된 엔터티가 포함될 수 있습니다. 엔터티 형식은 엔터티 섹션에 정의된 형식 중 어느 한 개일 수 있습니다. 아래 목록에 없는 엔터티도 보낼 수 있지만 처리될 것이라는 보장은 없습니다(경고는 새로운 유형의 엔터티에 대한 유효성 검사에 실패하지 않음).|
|**확장 속성**|사전(문자열, 문자열)|공급자는 (선택적으로) 여기에 사용자 지정 필드를 포함할 수 있습니다.|
|**의도**|열거형|경고 뒤에 있는 킬 체인 관련 의도입니다. 지원되는 값 목록 및 Azure Security Center에서 지원되는 킬 체인 의도에 대한 설명은 [의도를](../articles/security-center/alerts-reference.md#intentions)참조하십시오.<br/>이 필드에는 여러 값(쉼표로 구분)이 있을 수 있습니다.|
|**이스인시내**|Bool|이 필드는 경고가 인시던트(여러 경고의 복합 그룹화)인지 단일 경고인지를 결정합니다. 필드의 기본값은 'false'입니다(단일 경고임을 의미).|
|**ProcessingEndTime**|DateTime|경고를 보유한 원래 제품의 최종 사용자가 경고에 액세스할 수 있었던 시간입니다.|
|**ProductName**|String|이 경고를 게시한 제품의 이름(Azure 보안 센터, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS 등).|
|**수정 단계**|목록<String>|경고를 수정하기 위해 취할 수동 작업 항목입니다.|
|**리소스 식별자**|목록(리소스 식별자)|올바른 제품 노출 그룹(테넌트, 작업 공간, 구독 등)으로 경고를 지시하는 데 사용할 수 있는 이 경고에 대한 리소스 식별자입니다. 경고당 서로 다른 유형의 식별자가 여러 개 있을 수 있습니다.|
|**심각도**|열거형|공급자가 보고한 경고의 심각도입니다. 가능한 값: 정보, 낮음, 중간 및 높음.|
|**스타트타임유트**|DateTime|경고에 포함된 첫 번째 이벤트 또는 활동의 시간입니다. 필드는 UTC 표준 시간대 정보를 포함하여 ISO8601 형식을 준수하는 문자열이어야 합니다.|
|**상태**|열거형|경고의 수명 주기 상태입니다.<br/>지원되는 상태는 새로, 해결됨, 해제됨, 알 수 없음입니다.<br/>지원되는 옵션 이외의 값을 지정하는 경고에는 '알 수 없음' 상태가 할당됩니다.<br/>값을 지정하지 않는 경고에는 '새로'라는 상태가 할당됩니다.|
|**시스템 경고ID**|String|경고 식별자입니다.|
|**TimeGenerated**|DateTime|경고 공급자가 경고를 생성한 시간입니다. 내부 경고 공급자가 보고하지 않은 경우 제품은 제품 처리를 위해 받은 시간을 할당하도록 선택할 수 있습니다.  필드는 UTC 표준 시간대 정보를 포함하여 ISO8601 형식을 준수하는 문자열이어야 합니다.|
|**공급업체 이름**|String|경고를 발생 시키는 공급 업체의 이름입니다.|
|||
