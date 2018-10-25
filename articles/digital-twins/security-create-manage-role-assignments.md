---
title: Azure Digital Twins 장치 연결 및 인증 이해 | Microsoft Docs
description: Azure Digital Twins를 사용하여 장치 연결 및 인증
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: adfb4c369ea1b324da8562a5b0b245ebdecff602
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323920"
---
# <a name="create-and-manage-role-assignments"></a>역할 할당 만들기 및 관리

Azure Digital Twins는 [RBAC](./security-role-based-access-control.md)(역할 기반 액세스 제어)를 사용하여 리소스 액세스를 관리합니다.

각 역할 할당에는 다음이 포함됩니다.

* **개체 식별자**(Azure Active Directory ID, 서비스 사용자 개체 ID 또는 도메인 이름).
* **개체 식별자 유형**.
* **역할 정의 ID**.
* **공간 경로**.
* (대부분의 경우) Azure Active Directory **테넌트 ID**.

## <a name="role-definition-identifiers"></a>역할 정의 식별자

아래 표는 시스템/역할 API를 쿼리하여 얻을 수 있는 항목을 보여줍니다.

| **역할** | **식별자** |
| --- | --- |
| 공간 관리자 | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| 사용자 관리자| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| 장치 관리자 | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| 키 관리자 | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| 토큰 관리자 | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| 사용자 | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| 지원 전문가 | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| 장치 설치 관리자 | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| GatewayDevice | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>지원되는 ObjectIdTypes

지원되는 `ObjectIdTypes`:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>역할 할당 만들기

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **Name** | **필수** | **형식** | **설명** |
| --- | --- | --- | --- |
| roleId| yes |string | 역할 정의 식별자입니다. 역할 정의 및 식별자는 시스템 API를 쿼리하여 찾을 수 있습니다. |
| objectId | yes |string | 연결된 형식에 따라 포맷해야 하는 역할 할당의 개체 ID입니다. `DomainName` ObjectIdType의 경우 ObjectId가 `“@”` 문자로 시작해야 합니다. |
| objectIdType | yes |string | 역할 할당의 형식입니다. 이 표의 행 중 하나여야 합니다. |
| tenantId | 다름 | string |테넌트 식별자입니다. `DeviceId` 및 `TenantId` ObjectIdTypes에 허용되지 않습니다. `UserId` 및 `ServicePrincipalId` ObjectIdTypes에는 필수입니다. DomainName ObjectIdType에는 선택 사항입니다. |
| path* | yes | string |`Space` 개체의 전체 액세스 경로입니다. 예: `/{Guid}/{Guid}` 식별자에 전체 그래프에 대한 역할 할당이 필요한 경우 `"/"`를 지정하세요(루트를 지정). 그러나 되도록 사용하지 않는 것이 좋으며 **항상 최소 권한 원칙을 따라야 합니다**. |

## <a name="sample-configuration"></a>샘플 구성

사용자가 테넌트 공간의 층에 대한 관리자 액세스가 필요합니다.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

장치 및 센서를 모의 실험하는 테스트 시나리오를 실행하는 응용 프로그램:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

도메인의 모든 사용자 파트는 자신과 관련된 개체를 포함하여 공백, 센서 및 사용자에 대한 읽기 액세스를 받게 됩니다.

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

역할 할당을 가져오려면:

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Name** | **위치** | **필수** |    **형식** |  **설명** |
| --- | --- | --- | --- | --- |
| path | path | True | 문자열 | 공간의 전체 경로 |

역할 할당을 삭제하려면:

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Name** | **위치** | **필수** | **형식** | **설명** |
| --- | --- | --- | --- | --- |
| ID | path | True | 문자열 |   역할 할당 ID |

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 보안에 대한 자세한 내용은 [API 인증](./security-authenticating-apis.md)합을 참조하세요.
