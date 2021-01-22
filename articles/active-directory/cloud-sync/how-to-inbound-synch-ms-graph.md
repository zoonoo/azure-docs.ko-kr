---
title: MS Graph API를 사용 하는 클라우드 동기화에 대 한 인바운드 동기화
description: 이 항목에서는 Graph API를 사용 하 여 인바운드 동기화를 사용 하도록 설정 하는 방법을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3796b3d86f647e38cf2ff018e8c0c903d9a64e41
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682041"
---
# <a name="inbound-synchronization-for-cloud-sync-using-ms-graph-api"></a>MS Graph API를 사용 하는 클라우드 동기화에 대 한 인바운드 동기화

다음 문서에서는 MSGraph Api만 사용 하 여 동기화 프로필을 처음부터 복제 하는 방법을 설명 합니다.  
이 작업을 수행 하는 방법의 구조는 다음 단계로 구성 됩니다.  관련 토폴로지는 다음과 같습니다.

- [기본 설정](#basic-setup)
- [서비스 사용자 만들기](#create-service-principals)
- [동기화 작업 만들기](#create-sync-job)
- [대상 도메인 업데이트](#update-targeted-domain)
- [암호 해시 동기화 사용](#enable-sync-password-hashes-on-configuration-blade)
- [동기화 작업 시작](#start-sync-job)
- [상태 검토](#review-status)

이러한 [Windows PowerShell용 Microsoft Azure Active Directory 모듈](/powershell/module/msonline/) 명령을 사용 하 여 해당 테 넌 트에 대 한 관리 웹 서비스를 호출 하기 위한 필수 구성 요소인 프로덕션 테 넌 트에 대해 동기화를 사용 하도록 설정할 수 있습니다.

## <a name="basic-setup"></a>기본 설정

### <a name="enable-tenant-flags"></a>테 넌 트 플래그 사용

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
이러한 두 명령의 첫 번째 명령에는 Azure Active Directory 자격 증명이 필요 합니다. 이러한 commandlet 테 넌 트를 암시적으로 식별 하 고 동기화에 사용 하도록 설정할 수 있습니다.

## <a name="create-service-principals"></a>서비스 주체 만들기
다음으로 [AD2AAD 응용 프로그램/서비스 주체](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) 를 만들어야 합니다.

이 응용 프로그램 ID 1a4721b3-e57f-4451-ae87-ef078703ec94를 사용 해야 합니다. DisplayName은 AD 도메인 url 이며 (예: contoso.com) 포털에서 사용 되는 경우 다른 이름으로 지정할 수 있습니다.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>동기화 작업 만들기
위 명령의 출력은 만들어진 서비스 주체의 objectId를 반환 합니다. 이 예에서는 objectId가 614ac0e9-a59b-481f-bd8f-79a73d167e1c입니다.  Microsoft Graph를 사용 하 여 해당 서비스 사용자에 게 synchronizationJob를 추가 합니다.  

동기화 작업 만들기에 대 한 설명서는 [여기](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta)에서 찾을 수 있습니다.

위의 ID를 기록 하지 않은 경우 다음 MS Graph 호출을 실행 하 여 서비스 주체를 찾을 수 있습니다. 디렉터리를 사용 해야 합니다. 다음을 호출 하는 데 필요한 모든 권한:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

그런 다음 출력에서 앱 이름을 찾습니다.

다음 두 명령을 실행 하 여 두 개의 작업을 만듭니다. 하나는 사용자/그룹 프로 비전을 위한 것이 고 다른 하나는 암호 해시 동기화를 위한 것입니다. 동일한 요청이 며 템플릿 Id가 서로 다릅니다.


다음 두 요청을 호출 합니다.

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

둘 다 만들려는 경우 두 호출이 필요 합니다.

예제 반환 값 (프로 비전 용):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>대상 도메인 업데이트
이 테 넌 트의 경우 서비스 주체의 개체 식별자와 응용 프로그램 식별자는 다음과 같습니다.

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-c000-000000000000 DisplayName: testApp

이 구성의 대상이 되는 도메인을 업데이트 하 여이 도메인에 대 한 암호를 업데이트 해야 합니다.

사용할 도메인 이름이 온-프레미스 도메인 컨트롤러에 대해 설정한 것과 동일한 url 인지 확인 합니다.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 수행 하려는 작업을 기준으로 아래 값 배열에 다음과 같은 키/값 쌍을 추가 합니다.
 - PHS를 사용 하도록 설정 하 고 테 넌 트 플래그 {key: "AppKey", 값: "{" appKeyScenario ":" AD2AADPasswordHash "}"}를 동기화 합니다.
 
 - Sync 테 넌 트 플래그만 사용 (PHS를 설정 하지 않음) {키: "AppKey", 값: "{" appKeyScenario ":" AD2AADProvisioning "}"}
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

필요한 응답은 ...입니다. HTTP 204/내용 없음

여기서 강조 표시 된 "도메인" 값은 Azure Active Directory 항목을 프로 비전 할 온-프레미스 Active Directory 도메인의 이름입니다.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>구성 블레이드에서 동기화 암호 해시 사용

 이 섹션에서는 특정 구성에 대 한 암호 해시 동기화를 설정 하는 방법을 설명 합니다. 이는 테 넌 트 수준 기능 플래그를 사용 하도록 설정 하는 AppKey 암호와 다릅니다. 단일 도메인/구성에만 해당 됩니다. 종단 간 작업을 수행 하려면 응용 프로그램 키를 PHS로 설정 해야 합니다.

1. 스키마를 가져옵니다 (경고가 매우 큼). 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. 이 CredentialData 특성 매핑을 사용 합니다.
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. 스키마에서 다음 이름을 사용 하 여 다음 개체 매핑을 찾습니다.
 - 사용자 Active Directory 프로 비전
 - 프로 비전 Active Directory inetOrgPersons

 개체 매핑이 synchronizationRules [0]. objectMappings 내에 있습니다 (현재 동기화 규칙이 1 개만 있다고 가정할 수 있음).

4. 단계 (2)에서 CredentialData 매핑을 수행 하 고, 단계 (3)의 개체 매핑에 삽입 합니다.

 개체 매핑은 다음과 같습니다.
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 위의 **Create AD2AADProvisioning And AD2AADPasswordHash jobs** 단계에서 attributeMappings 배열로 매핑을 복사 하 여 붙여넣습니다. 

 이 배열의 요소 순서는 중요 하지 않습니다 (백 엔드는 사용자를 위해 정렬 됨). 이름이 배열에 이미 있는 경우 (예: targetAttributeName CredentialData가 있는 attributeMappings에 항목이 이미 있는 경우)에는이 특성 매핑을 추가 하는 데 주의 해야 합니다. 그렇지 않으면 충돌 오류가 발생 하거나 기존 매핑과 새 매핑이 함께 결합 될 수 있습니다 (일반적으로 원하는 결과가 아님). 백 엔드는 중복 제거 하지 않습니다. 

 사용자와 inetOrgpersons 모두에 대해이 작업을 수행 해야 합니다.

5. 만든 스키마를 저장 합니다. 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 요청 본문에 스키마를 추가 합니다. 

## <a name="start-sync-job"></a>동기화 작업 시작
다음 명령을 통해 작업을 다시 검색할 수 있습니다.

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

작업 검색에 대 한 설명서는 [여기](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta)에서 찾을 수 있습니다. 
 
작업을 시작 하려면 첫 번째 단계에서 만든 서비스 주체의 objectId를 사용 하 고 작업을 만든 요청에서 반환 된 작업 식별자를 사용 하 여이 요청을 실행 합니다.

작업을 시작 하는 방법에 대 한 설명서는 [여기](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta)에서 찾을 수 있습니다. 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

필요한 응답은 ...입니다. HTTP 204/내용 없음

작업을 제어 하는 다른 명령은 [여기](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)에 설명 되어 있습니다.
 
작업을 다시 시작 하려면 다음 중 하나를 사용 합니다.

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>상태 검토
다음을 통해 작업 상태를 검색 합니다.

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

관련 세부 정보에 대 한 반환 개체의 ' status ' 섹션 아래를 확인 합니다.

## <a name="next-steps"></a>다음 단계 

- [Azure AD Connect 클라우드 동기화 란?](what-is-cloud-sync.md)
- [변환](how-to-transformation.md)
- [Azure AD 동기화 API](/graph/api/resources/synchronization-overview?view=graph-rest-beta)