---
title: Azure Monitor에서 azure Active Directory 로그인 로그 스키마 | Microsoft Docs
description: 로그 스키마를 사용 하 여 Azure Monitor에서 Azure AD 로그인에 설명
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ac6c56dca100ea9836158f46881c4eb12213e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60285188"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Azure Monitor에서 Azure AD 로그인 로그 스키마를 해석 합니다.

이 문서에서는 Azure Monitor에서 Azure AD(Azure Active Directory) 로그인 로그 스키마에 대해 설명합니다. 로그인 관련 정보는 대부분 `records` 개체의 *Properties* 특성에서 제공됩니다.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>필드 설명

| 필드 이름 | 설명 |
|------------|-------------|
| Time | UTC 형식의 날짜 및 시간입니다. |
| ResourceId | 이 값이 매핑 해제되며 이 필드를 안전하게 무시할 수 있습니다.  |
| OperationName | 로그인의 경우 이 값은 항상 *로그인 활동*입니다. |
| OperationVersion | 클라이언트에서 요청한 REST API 버전입니다. |
| Category | 로그인의 경우 이 값은 항상 *SignIn*입니다. | 
| TenantId | 로그와 연결된 테넌트 GUID입니다. |
| ResultType | 로그인 작업의 결과는 *성공* 또는 *실패*일 수 있습니다. | 
| ResultSignature | 로그인 작업에 대한 오류 코드(있는 경우)를 포함합니다. |
| ResultDescription | 로그인 작업에 대한 오류 설명을 제공합니다. |
| DurationMs |  이 값이 매핑 해제되며 이 필드를 안전하게 무시할 수 있습니다.|
| callerIpAddress | 요청한 클라이언트의 IP 주소입니다. | 
| CorrelationId | 클라이언트에서 전달한 선택적 GUID입니다. 이 값은 클라이언트 쪽 작업을 서버 쪽 작업과 상관 관계를 지정하는 데 도움이 될 수 있으며, 서비스에 걸쳐 있는 로그를 추적하는 경우에 유용합니다. |
| ID | 요청할 때 제공된 토큰의 ID입니다. 사용자 계정, 시스템 계정 또는 서비스 사용자일 수 있습니다. |
| Level | 메시지의 형식을 제공합니다. 감사의 경우 항상 *Informational*입니다. |
| Location | 로그인 활동의 위치를 제공합니다. |
| properties | 로그인과 연결된 모든 속성을 나열합니다. 자세한 내용은 [Microsoft Graph API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)를 참조하세요. 이 스키마는 읽기 쉽도록 하기 위해 로그인 리소스와 동일한 특성 이름을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](reference-azure-monitor-audit-log-schema.md)
* [Azure 진단 로그에 대해 자세히 알아보기](../../azure-monitor/platform/diagnostic-logs-overview.md)