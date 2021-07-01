---
title: Azure에서 지원되는 FHIR 기능 - Azure API for FHIR
description: 이 문서에서는 Azure API for FHIR 구현되는 FHIR 사양의 기능을 설명합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 6/16/2021
ms.author: cavoeg
ms.openlocfilehash: 3e2d0c051ff882e43b04b2aea4357e78d79e67dc
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135954"
---
# <a name="features"></a>기능

Azure API for FHIR Azure용 Microsoft FHIR 서버의 완전 관리형 배포를 제공합니다. 서버는 [FHIR](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에서는 FHIR 서버의 주요 기능을 나열합니다.

## <a name="fhir-version"></a>FHIR 버전

지원되는 최신 버전: `4.0.1`

또한 현재 지원되는 이전 버전은 다음과 같습니다. `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 지원됨 - PaaS | 지원됨 - OSS(SQL) | 지원됨 - OSS(Cosmos DB) | 설명                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 읽기                           | 예       | 예       | 예       |                                                     |
| vread                          | 예       | 예       | 예       |                                                     |
| update                         | 예       | 예       | 예       |                                                     |
| 낙관적 잠금으로 업데이트 | 예       | 예       | 예       |                                                     |
| update(조건부)           | 예       | 예       | 예       |                                                     |
| 패치                          | 예        | 예        | 예        |                                                     |
| delete                         | 예       | 예       | 예       |  아래 참고를 참조하세요.                                   |
| delete(조건부)           | 예       | 예        | 예        |                                                     |
| history                        | 예       | 예       | 예       |                                                     |
| create                         | 예       | 예       | 예       | POST/PUT 모두 지원                               |
| create(조건부)           | 예       | 예       | 예       | 문제 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | Partial   | [FHIR 검색 개요를](overview-of-search.md)참조하세요.                           |
| 연결된 검색                 | Partial       | 예       | 부분   | 아래 참고 2를 참조하세요.                                   |
| 역방향 체인 검색         | Partial       | 예       | 부분   | 아래 참고 2를 참조하세요.                                   |
| capabilities                   | 예       | 예       | 예       |                                                     |
| 일괄 처리                          | 예       | 예       | 예       |                                                     |
| 트랜잭션                    | 예        | 예       | 예        |                                                     |
| 페이징                         | Partial   | Partial   | Partial   | `self` 및 `next` 가 지원됩니다.                     |
| 중개자                 | 예        | 예        | 예        |                                                     |

> [!Note]
> FHIR 사양에서 정의한 삭제를 사용하려면 삭제 후 이후 버전이 아닌 특정 리소스 읽기에서 410 HTTP 상태 코드를 반환하고 검색을 통해 리소스를 더 이상 찾을 수 없게 해야 합니다. 또한 Azure API for FHIR 사용하면 리소스를 완전히 삭제(모든 기록 포함)할 수 있습니다. 리소스를 완전히 삭제 하려면 매개 변수 설정을 `hardDelete` true ()로 전달할 수 있습니다 `DELETE {server}/{resource}/{id}?hardDelete=true` . 이 매개 변수를 전달 하지 않거나 `hardDelete` 을 false로 설정 하면 리소스의 기록 버전을 계속 사용할 수 있습니다.


 **참고 2**
* CosmosDB에서 연결 및 역방향 연결 된 FHIR 검색에 대 한 MVP 지원을 추가 합니다. 

  fhir 용 Azure API와 Cosmos에서 지 원하는 오픈 소스 fhir 서버에서 연결 된 검색 및 역방향 연결 검색은 MVP 구현입니다. Cosmos DB에 대 한 연결 된 검색을 수행 하기 위해 구현은 검색 식을 아래로 이동 하 고 하위 쿼리를 발행 하 여 일치 하는 리소스를 확인 합니다. 식의 각 수준에 대해 수행 됩니다. 쿼리가 100 개 보다 많은 결과를 반환 하면 오류가 throw 됩니다. 기본적으로 연결 된 검색은 기능 플래그 뒤에 있습니다. Cosmos DB에서 연결 된 검색을 사용 하려면 헤더를 사용 합니다 `x-ms-enable-chained-search: true` . 자세한 내용은 [PR 1695](https://github.com/microsoft/fhir-server/pull/1695)을 참조 하세요.

## <a name="extended-operations"></a>확장 된 작업

RESTful API를 확장 하는 지원 되는 모든 작업입니다.

| 검색 매개 변수 유형 | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 설명 |
|------------------------|-----------|-----------|-----------|---------|
| $export (전체 시스템) | 예       | 예       | 예       |         |
| 환자/$export        | 예       | 예       | 예       |         |
| 그룹/$export          | 예       | 예       | 예       |         |
| $convert-데이터          | 예       | 예       | 예       |         |
| $validate              | 예       | 예       | 예       |         |
| $member-일치          | 예       | 예       | 예       |         |
| $patient-모든 항목    | 예       | 예       | 예       |         |

## <a name="persistence"></a>지속성

Microsoft FHIR 서버에는 플러그형 지 속성 모듈이 있습니다 (참조 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

현재 fhir 서버 오픈 소스 코드는 [Azure Cosmos DB](../../cosmos-db/index-overview.md) 및 [SQL Database](https://azure.microsoft.com/services/sql-database/)에 대 한 구현을 포함 합니다.

Cosmos DB는 전역적으로 분산 된 다중 모델 (SQL api, MongoDB api 등) 데이터베이스입니다. 서로 다른 [일관성 수준을](../../cosmos-db/consistency-levels.md)지원 합니다. 기본 배포 템플릿은 일관성을 사용 하 여 FHIR 서버를 구성 `Strong` 하지만 요청 헤더를 사용 하 여 요청 별로 요청에서 일관성 정책을 수정할 수 있습니다 (일반적으로 완화 됨) `x-ms-consistency-level` .

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

fhir 서버는 액세스 제어를 위해 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 를 사용 합니다. 특히 구성 매개 변수가로 설정 되어 있는 경우 RBAC (역할 기반 액세스 제어)가 적용 되 `FhirServer:Security:Enabled` `true` 고 `/metadata` Fhir 서버에 대 한 모든 요청 (제외)에는 `Authorization` 요청 헤더가로 설정 되어 있어야 합니다 `Bearer <TOKEN>` . 토큰은 클레임에 정의 된 하나 이상의 역할을 포함 해야 합니다 `roles` . 지정 된 리소스에 대해 지정 된 작업을 허용 하는 역할이 토큰에 포함 되어 있으면 요청이 허용 됩니다.

현재 지정 된 역할에 대해 허용 되는 작업은 API에 *전역적* 으로 적용 됩니다.

## <a name="service-limits"></a>서비스 제한

* [**Rus (요청 단위)**](../../cosmos-db/concepts-limits.md) -Azure API for FHIR에 대 한 포털에서 최대 1만 RUs를 구성할 수 있습니다. 최소 400 RUs 또는 40 RUs/GB 중 더 큰 값이 필요 합니다. 1만 명 이상의 RUs가 필요한 경우 지원 티켓에 추가 하 여이를 늘릴 수 있습니다. 사용 가능한 최대값은 100만입니다.

* **번들 크기** -각 번들은 500 항목으로 제한 됩니다.

* **데이터 크기** -데이터/문서는 각각 2mb 보다 약간 작아야 합니다.

* **구독 제한** -기본적으로 각 구독은 최대 10 개의 FHIR 서버 인스턴스로 제한 됩니다. 구독 당 더 많은 인스턴스가 필요한 경우 지원 티켓을 열고 필요에 대 한 세부 정보를 제공 합니다.

* **동시 연결 및 인스턴스** -기본적으로 클러스터의 두 인스턴스에 대 한 동시 연결 수가 15 개 있습니다 (총 30 개 동시 요청). 더 많은 동시 요청이 필요한 경우 지원 티켓을 열고 필요에 대 한 세부 정보를 제공 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 용 Azure API에서 지원 되는 FHIR 기능에 대해 읽었습니다. 그런 다음, FHIR 용 Azure API를 배포 합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)
