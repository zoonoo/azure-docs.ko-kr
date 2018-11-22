---
title: Azure Digital Twins 디바이스 연결 및 인증 이해 | Microsoft Docs
description: Azure Digital Twins를 사용하여 디바이스 연결 및 인증
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: f032e3ebf6a10411057cd6d41df0cad6248f328b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636240"
---
# <a name="create-and-manage-role-assignments"></a>역할 할당 만들기 및 관리

Azure Digital Twins는 [RBAC](./security-role-based-access-control.md)(역할 기반 액세스 제어)를 사용하여 리소스 액세스를 관리합니다.

각 역할 할당에는 다음이 포함됩니다.

* **개체 식별자**: Azure Active Directory ID, 서비스 사용자 개체 ID 또는 도메인 이름
* **개체 식별자 유형**
* **역할 정의 ID**
* **공간 경로**
* **테넌트 ID**: 대부분의 경우 Azure Active Directory 테넌트 ID

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="role-definition-identifiers"></a>역할 정의 식별자

다음 표는 시스템/역할 API를 쿼리하여 얻을 수 있는 항목을 보여줍니다.

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
| 게이트웨이 디바이스 | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

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
HTTP POST YOUR_MANAGEMENT_API_URL/roleassignments
```

| **Name** | **필수** | **형식** | **설명** |
| --- | --- | --- | --- |
| roleId| yes |문자열 | 역할 정의 식별자입니다. 시스템 API를 쿼리하여 역할 정의와 해당 식별자를 찾습니다. |
| objectId | yes |문자열 | 연결된 형식에 따라 포맷해야 하는 역할 할당의 개체 ID입니다. `DomainName` ObjectIdType의 경우 ObjectId가 `“@”` 문자로 시작해야 합니다. |
| objectIdType | yes |문자열 | 역할 할당의 형식입니다. 이 표의 행 중 하나여야 합니다. |
| tenantId | 다름 | 문자열 |테넌트 식별자입니다. `DeviceId` 및 `TenantId` ObjectIdTypes에 허용되지 않습니다. `UserId` 및 `ServicePrincipalId` ObjectIdTypes에는 필수입니다. DomainName ObjectIdType에는 선택 사항입니다. |
| path* | yes | 문자열 |`Space` 개체의 전체 액세스 경로입니다. 예는 `/{Guid}/{Guid}`입니다. 식별자에 전체 그래프에 대한 역할 할당이 필요한 경우 `"/"`를 지정합니다. 이 문자는 루트를 지정하지만 사용하지 않는 것이 좋습니다. 항상 최소 권한 원칙을 준수합니다. |

## <a name="sample-configuration"></a>샘플 구성

이 예제에서는 사용자가 테넌트 공간의 층에 대한 관리자 액세스가 필요합니다.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

이 예제에서는 애플리케이션이 디바이스 및 센서를 모의 실험하는 테스트 시나리오를 실행합니다.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

도메인의 일부인 모든 사용자는 공간, 센서, 사용자에 대한 읽기 액세스 권한을 받습니다. 이 액세스는 해당 관련 개체를 포함합니다.

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

GET을 사용하여 역할 할당을 가져옵니다.

```plaintext
HTTP GET YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| **Name** | **위치** | **필수** |    **형식** |  **설명** |
| --- | --- | --- | --- | --- |
| YOUR_PATH | path | True | 문자열 |    공간의 전체 경로 |

DELETE를 사용하여 역할 할당을 삭제합니다.

```plaintext
HTTP DELETE YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ID
```

| **Name** | **위치** | **필수** | **형식** | **설명** |
| --- | --- | --- | --- | --- |
| YOUR_ROLE_ID | path | True | 문자열 | 역할 할당 ID |

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 보안에 대한 자세한 내용은 [API 인증](./security-authenticating-apis.md)합을 참조하세요.
