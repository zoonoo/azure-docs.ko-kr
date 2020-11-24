---
title: Azure에서 지원 되는 FHIR 기능-FHIR 용 Azure API
description: 이 문서에서는 Azure API for FHIR에서 구현 되는 FHIR 사양의 기능을 설명 합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 3aea2322129c383a385168c54001464da5dae276
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95520087"
---
# <a name="features"></a>기능

FHIR 용 azure API는 Azure 용 Microsoft FHIR 서버를 완전히 관리 하는 배포를 제공 합니다. 서버는 [Fhir](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에는 FHIR 서버의 주요 기능이 나열 되어 있습니다.

## <a name="fhir-version"></a>FHIR 버전

지원 되는 최신 버전: `4.0.1`

이전 버전은 현재 다음과 같이 지원 됩니다. `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 의견                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 읽기                           | 예       | 예       | 예       |                                                     |
| vread                          | 예       | 예       | 예       |                                                     |
| update                         | 예       | 예       | 예       |                                                     |
| 낙관적 잠금으로 업데이트 | 예       | 예       | 예       |                                                     |
| update (조건부)           | 예       | 예       | 예       |                                                     |
| 패치나                          | 아니요        | 아니요        | 아니요        |                                                     |
| delete                         | 예       | 예       | 예       |                                                     |
| delete (조건부)           | 아니요        | 아니요        | 아니요        |                                                     |
| history                        | 예       | 예       | 예       |                                                     |
| create                         | 예       | 예       | 예       | POST/PUT 모두 지원                               |
| create (조건부)           | 예       | 예       | 예       | 문제 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | Partial   | 아래 참조                                           |
| 연결 된 검색                 | 아니요        | 예       | 아니요        |                                           |
| 역방향 연결 된 검색         | 아니요        | 아니요        | 아니요        |                                            |
| capabilities                   | 예       | 예       | 예       |                                                     |
| 일괄 처리                          | 예       | 예       | 예       |                                                     |
| 트랜잭션                    | 아니요        | 예       | 아니요        |                                                     |
| 페이징                         | Partial   | Partial   | Partial   | `self` 및 `next` 가 지원 됩니다.                     |
| 매개자                 | 아니요        | 아니요        | 예        |                                                     |

## <a name="search"></a>검색

모든 검색 매개 변수 유형이 지원 됩니다. 

| 검색 매개 변수 유형 | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 의견 |
|-----------------------|-----------|-----------|-----------|---------|
| 숫자                | 예       | 예       | 예       |         |
| Date/DateTime         | 예       | 예       | 예       |         |
| String                | 예       | 예       | 예       |         |
| 토큰                 | 예       | 예       | 예       |         |
| 참조             | 예       | 예       | 예       |         |
| 복합             | 예       | 예       | 예       |         |
| 수량              | 예       | 예       | 예       |         |
| URI                   | 예       | 예       | 예       |         |
| 특수               | 아니요        | 아니요        | 아니요        |         |


| 한정자             | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 주석 |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | 예       | 예       | 예       |         |
|`:exact`               | 예       | 예       | 예       |         |
|`:contains`            | 예       | 예       | 예       |         |
|`:text`                | 예       | 예       | 예       |         |
|`:in` 토큰          | 아니요        | 아니요        | 아니요        |         |
|`:below` 토큰       | 아니요        | 아니요        | 아니요        |         |
|`:above` 토큰       | 아니요        | 아니요        | 아니요        |         |
|`:not-in` 토큰      | 아니요        | 아니요        | 아니요        |         |
|`:[type]` 참조일  | 아니요        | 아니요        | 아니요        |         |
|`:below` uri         | 예       | 예       | 예       |         |
|`:not`                 | 아니요        | 아니요        | 아니요        |         |
|`:above` uri         | 아니요        | 아니요        | 아니요        | 문제 [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| 공통 검색 매개 변수 | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 주석 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | 예       | 예       | 예       |         |
| `_lastUpdated`          | 예       | 예       | 예       |         |
| `_tag`                  | 예       | 예       | 예       |         |
| `_profile`              | 예       | 예       | 예       |         |
| `_security`             | 예       | 예       | 예       |         |
| `_text`                 | 아니요        | 아니요        | 아니요        |         |
| `_content`              | 아니요        | 아니요        | 아니요        |         |
| `_list`                 | 예       | 예       | 예       |         |
| `_has`                  | 아니요        | 아니요        | 아니요        |         |
| `_type`                 | 예       | 예       | 예       |         |
| `_query`                | 아니요        | 아니요        | 아니요        |         |
| `_filter`               | 아니요        | 아니요        | 아니요        |         |

| 검색 결과 매개 변수 | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 의견 |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Partial        | Partial   | Partial        |   `_sort=_lastUpdated`가 지원됨       |
| `_count`                | 예       | 예       | 예       | `_count` 는 100 자로 제한 됩니다. 100 보다 높게 설정 하면 100만 반환 되 고 번들에 경고가 반환 됩니다. |
| `_include`              | 예       | 예       | 예       |포함 된 항목은 100 개로 제한 됩니다. PaaS 및 OSS에 포함 Cosmos DB에 포함 되지 않음: 반복 지원.|
| `_revinclude`           | 예       | 예       | 예       | 포함 된 항목은 100 개로 제한 됩니다. PaaS 및 OSS에 포함 Cosmos DB에 포함 되지 않음: 반복 지원.|
| `_summary`              | Partial   | Partial   | Partial   | `_summary=count`가 지원됨 |
| `_total`                | Partial   | Partial   | Partial   | _total = non _total = 정확성      |
| `_elements`             | 예       | 예       | 예       |         |
| `_contained`            | 아니요        | 아니요        | 아니요        |         |
| `containedType`         | 아니요        | 아니요        | 아니요        |         |
| `_score`                | 아니요        | 아니요        | 아니요        |         |

## <a name="extended-operations"></a>확장 된 작업

RESTful API를 확장 하는 지원 되는 모든 작업입니다.

| 검색 매개 변수 유형 | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 의견 |
|------------------------|-----------|-----------|-----------|---------|
| $export (전체 시스템) | 예       | 예       | 예       |         |
| 환자/$export        | 예       | 예       | 예       |         |
| 그룹/$export          | 예       | 예       | 예       |         |

## <a name="persistence"></a>지속성

Microsoft FHIR 서버에는 플러그형 지 속성 모듈이 있습니다 (참조 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

현재 FHIR 서버 오픈 소스 코드는 [Azure Cosmos DB](../cosmos-db/index-overview.md) 및 [SQL Database](https://azure.microsoft.com/services/sql-database/)에 대 한 구현을 포함 합니다.

Cosmos DB는 전역적으로 분산 된 다중 모델 (SQL API, MongoDB API 등) 데이터베이스입니다. 서로 다른 [일관성 수준을](../cosmos-db/consistency-levels.md)지원 합니다. 기본 배포 템플릿은 일관성을 사용 하 여 FHIR 서버를 구성 `Strong` 하지만 요청 헤더를 사용 하 여 요청 별로 요청에서 일관성 정책을 수정할 수 있습니다 (일반적으로 완화 됨) `x-ms-consistency-level` .

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

FHIR 서버는 액세스 제어를 위해 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 를 사용 합니다. 특히 구성 매개 변수가로 설정 되어 있는 경우 RBAC (역할 기반 액세스 제어)가 적용 되 `FhirServer:Security:Enabled` `true` 고 `/metadata` Fhir 서버에 대 한 모든 요청 (제외)에는 `Authorization` 요청 헤더가로 설정 되어 있어야 합니다 `Bearer <TOKEN>` . 토큰은 클레임에 정의 된 하나 이상의 역할을 포함 해야 합니다 `roles` . 지정 된 리소스에 대해 지정 된 작업을 허용 하는 역할이 토큰에 포함 되어 있으면 요청이 허용 됩니다.

현재 지정 된 역할에 대해 허용 되는 작업은 API에 *전역적* 으로 적용 됩니다.

## <a name="service-limits"></a>서비스 제한

* [**Rus (요청 단위)**](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) -Azure API for FHIR에 대 한 포털에서 최대 1만 RUs를 구성할 수 있습니다. 최소 400 RUs 또는 10 RUs/GB 중 더 큰 값이 필요 합니다. 1만 명 이상의 RUs가 필요한 경우 지원 티켓에 추가 하 여이를 늘릴 수 있습니다. 사용 가능한 최대값은 100만입니다.

* **동시 연결** 및 **인스턴스** -dafault에서 클러스터의 두 인스턴스에 대해 5 개의 동시 연결이 있습니다 (총 10 개의 동시 요청). 더 많은 동시 요청이 필요 하다 고 생각 되는 경우 요구 사항에 대 한 세부 정보가 포함 된 지원 티켓을 엽니다.

* **번들 크기** -각 번들은 500 항목으로 제한 됩니다.

* **데이터 크기** -데이터/문서는 각각 2mb 보다 약간 작아야 합니다.

## <a name="performance-expectations"></a>성능 기대

시스템의 성능은 RUs의 수, 동시 연결 수 및 수행 중인 작업의 유형 (Put, Post 등)에 따라 달라 집니다. 아래에는 구성 된 RUs를 기준으로 예측할 수 있는 몇 가지 일반적인 범위가 나와 있습니다. 일반적으로 성능 확장은 RUs의 증가에 따라 선형적으로 확장 됩니다.

| RUs의 # | 리소스/초 |
|----------|---------------|
| 400      | 5-10          |
| 1,000    | 100-150       |
| 10000   | 225-400       |
| 100,000  | 2500-4000   |

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 용 Azure API에서 지원 되는 FHIR 기능에 대해 읽었습니다. 그런 다음, FHIR 용 Azure API를 배포 합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)
