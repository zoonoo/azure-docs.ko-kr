---
title: Azure에서 지원되는 FHIR 기능 - Azure API for FHIR
description: 이 문서에서는 Azure API for FHIR 구현되는 FHIR 사양의 기능을 설명합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: b36937b61b5508dfc933ef15b316d1d1da7b7acc
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078637"
---
# <a name="features"></a>기능

Azure API for FHIR Azure용 Microsoft FHIR 서버의 완전 관리형 배포를 제공합니다. 서버는 [FHIR](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에서는 FHIR 서버의 주요 기능을 나열합니다.

## <a name="fhir-version"></a>FHIR 버전

지원되는 최신 버전: `4.0.1`

또한 현재 지원되는 이전 버전은 다음과 같습니다. `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 지원됨 - PaaS | 지원됨 - OSS(SQL) | 지원됨 - OSS(Cosmos DB) | 의견                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 읽기                           | 예       | 예       | 예       |                                                     |
| vread                          | 예       | 예       | 예       |                                                     |
| update                         | 예       | 예       | 예       |                                                     |
| 낙관적 잠금으로 업데이트 | 예       | 예       | 예       |                                                     |
| update(조건부)           | 예       | 예       | 예       |                                                     |
| 패치                          | 예        | 예        | 예        |                                                     |
| delete                         | 예       | 예       | 예       |  아래 참고를 참조하세요.                                   |
| delete(조건부)           | 예        | 예        | 예        |                                                     |
| history                        | 예       | 예       | 예       |                                                     |
| create                         | 예       | 예       | 예       | POST/PUT 모두 지원                               |
| create(조건부)           | 예       | 예       | 예       | 문제 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | Partial   | [FHIR 검색 개요를](overview-of-search.md)참조하세요.                           |
| 연결 된 검색                 | 부분       | 예       | 부분   | 아래의 참고 2를 참조 하세요.                                   |
| 역방향 연결 된 검색         | 부분       | 예       | 부분   | 아래의 참고 2를 참조 하세요.                                   |
| capabilities                   | 예       | 예       | 예       |                                                     |
| 일괄 처리                          | 예       | 예       | 예       |                                                     |
| 트랜잭션                    | 예        | 예       | 예        |                                                     |
| 페이징                         | Partial   | Partial   | Partial   | `self` 및 `next` 가 지원 됩니다.                     |
| 매개자                 | 예        | 예        | 예        |                                                     |

> [!Note]
> FHIR 사양에 정의 된 삭제를 수행 하려면 삭제 후에 다음 버전의 특정 리소스 읽기가 410 HTTP 상태 코드를 반환 하 고 검색을 통해 리소스를 더 이상 찾을 수 없습니다. 또한 Azure API for FHIR을 사용 하 여 리소스의 모든 기록을 포함 하 여 전체를 삭제할 수 있습니다. 리소스를 완전히 삭제 하려면 매개 변수 설정을 `hardDelete` true ()로 전달할 수 있습니다 `DELETE {server}/{resource}/{id}?hardDelete=true` . 이 매개 변수를 전달 하지 않거나 `hardDelete` 을 false로 설정 하면 리소스의 기록 버전을 계속 사용할 수 있습니다.


 **참고 2**
* CosmosDB에서 연결 및 역방향 연결 된 FHIR 검색에 대 한 MVP 지원을 추가 합니다. 

  FHIR 용 Azure API와 Cosmos에서 지 원하는 오픈 소스 FHIR 서버에서 연결 된 검색 및 역방향 연결 검색은 MVP 구현입니다. Cosmos DB에 대 한 연결 된 검색을 수행 하기 위해 구현은 검색 식을 아래로 이동 하 고 하위 쿼리를 발행 하 여 일치 하는 리소스를 확인 합니다. 식의 각 수준에 대해 수행 됩니다. 쿼리가 100 개 보다 많은 결과를 반환 하면 오류가 throw 됩니다. 기본적으로 연결 된 검색은 기능 플래그 뒤에 있습니다. Cosmos DB에서 연결 된 검색을 사용 하려면 헤더를 사용 합니다 `x-ms-enable-chained-search: true` . 자세한 내용은 [PR 1695](https://github.com/microsoft/fhir-server/pull/1695)을 참조 하세요.

## <a name="extended-operations"></a>확장 된 작업

RESTful API를 확장 하는 지원 되는 모든 작업입니다.

| 검색 매개 변수 유형 | 지원됨 - PaaS | 지원됨 - OSS(SQL) | 지원됨 - OSS(Cosmos DB) | 의견 |
|------------------------|-----------|-----------|-----------|---------|
| $export(전체 시스템) | 예       | 예       | 예       |         |
| 환자/$export        | 예       | 예       | 예       |         |
| 그룹/$export          | 예       | 예       | 예       |         |
| $convert-data          | 예       | 예       | 예       |         |
| $validate          | 예       | 예       | 예       |         |

## <a name="persistence"></a>지속성

Microsoft FHIR 서버에는 플러그형 지속성 모듈이 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) 있습니다(참조).

현재 FHIR 서버 오픈 소스 코드에는 [Azure Cosmos DB](../../cosmos-db/index-overview.md) 및 [SQL Database](https://azure.microsoft.com/services/sql-database/)대한 구현이 포함되어 있습니다.

Cosmos DB 전역적으로 분산된 다중 모델(SQL API, MongoDB API 등) 데이터베이스입니다. 다양한 일관성 [수준](../../cosmos-db/consistency-levels.md)을 지원합니다. 기본 배포 템플릿은 일관성을 사용하여 FHIR 서버를 `Strong` 구성하지만 요청 헤더를 사용하여 요청에 따라 일관성 정책을 수정(일반적으로 완화)할 수 `x-ms-consistency-level` 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

FHIR 서버는 액세스 제어에 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 사용합니다. 특히 구성 매개 변수가 로 `FhirServer:Security:Enabled` 설정되고 FHIR 서버에 대한 모든 `true` 요청(제외)에 요청 헤더가 로 설정된 경우 RBAC(역할 기반 액세스 `/metadata` 제어)가 `Authorization` 적용됩니다. `Bearer <TOKEN>` 토큰은 클레임에 정의된 역할을 하나 이상 포함해야 `roles` 합니다. 토큰에 지정된 리소스에 대해 지정된 작업을 허용하는 역할이 포함된 경우 요청이 허용됩니다.

현재 지정된 역할에 허용되는 작업은 API에 *전역적으로* 적용됩니다.

## <a name="service-limits"></a>서비스 제한

* [**요청 단위(RUS)**](../../cosmos-db/concepts-limits.md) - 포털에서 Azure API for FHIR 최대 10,000개까지 구성할 수 있습니다. 최소 400 RUs 또는 40 RUs/GB 중 더 큰 값이 필요 합니다. 1만 명 이상의 RUs가 필요한 경우 지원 티켓에 추가 하 여이를 늘릴 수 있습니다. 사용 가능한 최대값은 100만입니다.

* **번들 크기** -각 번들은 500 항목으로 제한 됩니다.

* **데이터 크기** -데이터/문서는 각각 2mb 보다 약간 작아야 합니다.

* **구독 제한** -기본적으로 각 구독은 최대 10 개의 FHIR 서버 인스턴스로 제한 됩니다. 구독 당 더 많은 인스턴스가 필요한 경우 지원 티켓을 열고 필요에 대 한 세부 정보를 제공 합니다.

* **동시 연결 및 인스턴스** -기본적으로 클러스터의 두 인스턴스에 대 한 동시 연결 수가 15 개 있습니다 (총 30 개 동시 요청). 더 많은 동시 요청이 필요한 경우 지원 티켓을 열고 필요에 대 한 세부 정보를 제공 합니다.

## <a name="performance-expectations"></a>성능 기대

시스템의 성능은 RUs의 수, 동시 연결 수 및 수행 중인 작업의 유형 (Put, Post 등)에 따라 달라 집니다. 아래에는 구성 된 RUs를 기준으로 예측할 수 있는 몇 가지 일반적인 범위가 나와 있습니다. 일반적으로 성능 확장은 RUs의 증가에 따라 선형적으로 확장 됩니다.

| RUs의 # | 리소스/초 |    최대 저장소 (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1,000    | 100-150       |      25  |
| 10000   | 225-400       |      250  |
| 100,000  | 2500-4000   |      2,500  |

참고: Cosmos DB 요구 사항에 따라 저장소의 GB 당 최소 처리량 40 r u/초를 요구 합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure API for FHIR 지원되는 FHIR 기능에 대해 읽어보았습니다. 다음으로 Azure API for FHIR 배포합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)
