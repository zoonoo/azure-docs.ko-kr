---
title: Azure Monitor(미리 보기)에서 Azure Active Directory 감사 로그 스키마 해석 | Microsoft Docs
description: Azure Monitor(미리 보기)에서 사용을 위해 Azure AD 감사 로그 스키마 설명
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240596"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Azure Monitor(미리 보기)에서 Azure Active Directory 감사 로그 스키마 해석

이 문서에서는 Azure Monitor에서 Azure AD 감사 로그 스키마를 설명합니다. 각 개별 로그 항목은 아래 두 예제에 표시된 것과 같이 JSON Blob으로 서식이 지정된 텍스트로 저장됩니다. 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

| 필드 이름 | 설명 |
|------------|-------------|
| 실시간       | 날짜 및 시간(UTC) |
| operationName | 작업의 이름입니다. |
| operationVersion | 클라이언트에서 요청한 REST API 버전입니다. |
| 카테고리 | 현재 *감사*는 유일하게 지원되는 값입니다. |
| tenantId | 로그와 연결된 테넌트 Guid입니다. |
| resultType | 작업의 결과는 *성공* 또는 *실패*일 수 있습니다. |
| resultSignature |  매핑 해제되며 이 필드를 안전하게 무시할 수 있습니다. | 
| resultDescription | 사용 가능한 경우 결과의 추가 설명입니다. | 
| durationMS |  매핑 해제되며 이 필드를 안전하게 무시할 수 있습니다. |
| callerIpAddress | 요청한 클라이언트의 IP 주소입니다. | 
| CorrelationId | 클라이언트에서 전달한 선택적 Guid입니다. 서버 쪽 작업과 클라이언트 쪽 작업의 상관 관계를 지정하는 데 도움이 될 수 있으며 서비스에 걸친 로그를 추적하는 경우에 유용합니다. |
| ID | 요청을 수행할 때 제공된 토큰의 ID입니다. 사용자 계정, 시스템 계정 또는 서비스 사용자일 수 있습니다. |
| level | 메시지 형식입니다. 감사 로그의 경우 항상 *정보 제공*입니다. |
| location | 데이터 센터의 위치입니다. |
| properties | 감사 로그와 관련된 지원되는 속성을 나열합니다. 자세한 내용은 아래 테이블을 참조하세요. | 


| 속성 이름 | 설명 |
|---------------|-------------|
| AuditEventCategory | 감사 이벤트의 형식입니다. *사용자 관리*, *응용 프로그램 관리* 등일 수 있습니다.|
| ID 형식 | *응용 프로그램* 또는 *사용자* |
| 작업 유형 | *추가*, *업데이트*, *삭제* 또는 *기타*일 수 있습니다. |
| 대상 리소스 종류 | 작업이 수행된 대상 리소스 종류를 지정합니다. *응용 프로그램*, *사용자*, *역할*, *정책*일 수 있습니다. | 
| 대상 리소스 이름 | 대상 리소스의 이름입니다. 예를 들어 응용 프로그램 이름, 역할 이름, 사용자 계정 이름 또는 서비스 사용자 이름일 수 있습니다. |
| additionalTargets | 특정 작업에 대한 추가 속성을 나열합니다. 예를 들어 업데이트 작업의 경우 이전 값과 새 값이 *targetUpdatedProperties* 아래에 나열됩니다. | 

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 로그인 로그 스키마 해석](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Azure 진단 로그에 대해 자세히 알아보기](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [질문과 대답 및 알려진 문제](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)