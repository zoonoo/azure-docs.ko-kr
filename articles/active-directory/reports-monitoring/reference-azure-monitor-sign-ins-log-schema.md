---
title: Azure Monitor(미리 보기)에서 Azure Active Directory 로그인 로그 스키마 | Microsoft Docs
description: Azure Monitor(미리 보기)에서 사용을 위해 Azure AD 로그인 로그 스키마 설명
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78ce1de5b5b9ff46efcc9e7faed9aa147b53211a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439059"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor-preview"></a>Azure Monitor(미리 보기)에서 Azure AD 로그인 로그 스키마 해석

이 문서에서는 Azure Monitor에서 Azure AD(Azure Active Directory) 로그인 로그 스키마에 대해 설명합니다. 로그인 관련 정보는 대부분 `records` 개체의 *Properties* 특성에서 제공됩니다.

```json
{ 
    "records": [ 
    { 
        "time": "2018-05-16T16:09:58.4634578Z", 
        "resourceId": null, 
        "operationName": "Sign-in activity", 
        "operationVersion": "1.0", 
        "category": "SignIn", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "50140", 
        "resultSignature": "None", 
        "resultDescription": "Other", 
        "durationMs": 0, 
        "callerIpAddress": "167.220.0.158", 
        "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
        "identity": "arvind harinder", 
        "Level": 4, 
        "location": "US", 
        "properties": { 
            "id": "0782c515-08b6-4029-a65c-29d9a3d20800", 
            "createdDateTime": "2018-05-16T16:09:58.4634578+00:00", 
            "userDisplayName": "Arvind Harinder", 
            "userPrincipalName": "ah@wingtiptoysonline.onmicrosoft.com", 
            "userId": "5b9f356d-9592-42fd-9ec4-d70963909534", 
            "appId": "c44b4083-3bb0-49c1-b47d-974e53cbdf3c", 
            "appDisplayName": "Azure Portal", 
            "ipAddress": "167.220.0.158", 
            "status": { 
                "errorCode": 50140, 
                "failureReason": "Other" 
            }, 
            "clientAppUsed": "Browser", 
            "deviceDetail": { 
                "operatingSystem": "Windows 10", 
                "browser": "Chrome 66.0.3359" 
            }, 
            "location": { 
                "city": "Sammamish", 
                "state": "Washington", 
                "countryOrRegion": "US", 
                "geoCoordinates": { 
                    "latitude": 47.66630935668945, 
                    "longitude": -122.09821319580078 
                } 
            }, 
            "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
            "conditionalAccessStatus": 2, 
            "conditionalAccessPolicies": [ 
            { 
                "id": "de7e60eb-ed89-4d73-8205-2227def6b7c9", 
                "displayName": "[billg] SharePoint limited access policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "7412a2d8-cbb1-4f1c-96cf-8410b4b8b37b", 
                "displayName": "[BillG] AIP MFA Policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "727ed8ea-059d-4d8f-aba5-c1dc500e8b06", 
                "displayName": "[billg] mfa for mail", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "6701123a-b4c6-48af-8565-565c8bf7cabc", 
                "displayName": "Medium signin risk block", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "fbafa2da-cf7f-4ec3-83cf-281188e53f76", 
                "displayName": "Require MFA for admins [Ignite talk] ", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "15339054-709d-4e06-a9ec-342bf043ea56", 
                "displayName": "Enhanced proofing for Azure portal [Ignite talk]", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "2ff9436f-bc72-4ce6-b17e-e7e51153146e", 
                "displayName": "[calebb] AIP policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "46ab586b-9447-4847-a889-e60705d96e56", 
                "displayName": "Test policy, OR", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "ceb6e17e-a5d0-4b3a-a150-6c2be2d5b0e9", 
                "displayName": "mm policy with Duo", 
                "enforcedGrantControls": [ 
                    "Require Duo Mfa" 
                ], 
            "enforcedSessionControls": [], 
            "result": 2 
            }, 
            ], 
            "isRisky": false 
            } 
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
| 위치 | 로그인 활동의 위치를 제공합니다. |
| properties | 로그인과 연결된 모든 속성을 나열합니다. 자세한 내용은 [Microsoft Graph API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)를 참조하세요. 이 스키마는 읽기 쉽도록 하기 위해 로그인 리소스와 동일한 특성 이름을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](reference-azure-monitor-audit-log-schema.md)
* [Azure 진단 로그에 대해 자세히 알아보기](../../azure-monitor/platform/diagnostic-logs-overview.md)
