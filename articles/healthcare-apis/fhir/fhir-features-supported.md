---
title: Azure에서 지원 되는 FHIR 기능-FHIR 용 Azure API
description: 이 문서에서는 Azure API for FHIR에서 구현 되는 FHIR 사양의 기능을 설명 합니다.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: de99d6b5480a8e8262aba9d40826f69862630961
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592990"
---
# <a name="features"></a>기능

FHIR 용 azure API는 Azure 용 Microsoft FHIR 서버를 완전히 관리 하는 배포를 제공 합니다. 서버는 [Fhir](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에는 FHIR 서버의 주요 기능이 나열 되어 있습니다.

## <a name="fhir-version"></a>FHIR 버전

지원 되는 최신 버전: `4.0.1`

이전 버전은 현재 다음과 같이 지원 됩니다. `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 설명                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 읽기                           | 예       | 예       | 예       |                                                     |
| vread                          | 예       | 예       | 예       |                                                     |
| update                         | 예       | 예       | 예       |                                                     |
| 낙관적 잠금으로 업데이트 | 예       | 예       | 예       |                                                     |
| update (조건부)           | 예       | 예       | 예       |                                                     |
| 패치                          | 아니요        | 아니요        | 아니요        |                                                     |
| delete                         | 예       | 예       | 예       |  아래 참고를 참조 하세요.                                   |
| delete (조건부)           | 예       | 예        | 예        |                                                     |
| history                        | 예       | 예       | 예       |                                                     |
| create                         | 예       | 예       | 예       | POST/PUT 모두 지원                               |
| create (조건부)           | 예       | 예       | 예       | 문제 [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Partial   | Partial   | [FHIR 검색 개요를](overview-of-search.md)참조 하세요.                           |
| 연결 된 검색                 | Partial       | 예       | 부분   | 아래의 참고 2를 참조 하세요.                                   |
| 역방향 연결 된 검색         | Partial       | 예       | 부분   | 아래의 참고 2를 참조 하세요.                                   |
| capabilities                   | 예       | 예       | 예       |                                                     |
| 일괄 처리                          | 예       | 예       | 예       |                                                     |
| 트랜잭션                    | 아니요        | 예       | 아니요        |                                                     |
| 페이징                         | Partial   | Partial   | Partial   | `self` 및 `next` 가 지원 됩니다.                     |
| 매개자                 | 아니요        | 아니요        | 아니요        |                                                     |

> [!Note]
> FHIR 사양에 정의 된 삭제를 수행 하려면 삭제 후에 다음 버전의 특정 리소스 읽기가 410 HTTP 상태 코드를 반환 하 고 검색을 통해 리소스를 더 이상 찾을 수 없습니다. 또한 Azure API for FHIR을 사용 하 여 리소스의 모든 기록을 포함 하 여 전체를 삭제할 수 있습니다. 리소스를 완전히 삭제하려면 매개 변수 설정을 true( )로 전달할 수 `hardDelete` `DELETE {server}/{resource}/{id}?hardDelete=true` 있습니다. 이 매개 변수를 전달하지 않거나 false로 설정하면 `hardDelete` 리소스의 기록 버전을 계속 사용할 수 있습니다.


 **참고 2**
* CosmosDB의 Chained 및 Reverse Chained FHIR Search에 대한 MVP 지원을 추가합니다. 

  Cosmos에서 백업하는 Azure API for FHIR 및 오픈 소스 FHIR 서버에서 연결된 검색 및 역방향 체인 검색은 MVP 구현입니다. Cosmos DB 연결된 검색을 수행하기 위해 구현에서는 검색 식을 안내하고 하위 쿼리를 실행하여 일치하는 리소스를 확인합니다. 이 작업은 식의 각 수준에 대해 수행됩니다. 쿼리가 100개 이상의 결과를 반환하는 경우 오류가 throw됩니다. 기본적으로 연결된 검색은 기능 플래그 뒤에 있습니다. Cosmos DB 연결된 검색을 사용하려면 헤더를 `x-ms-enable-chained-search: true` 사용합니다. 자세한 내용은 [PR 1695를 참조하세요.](https://github.com/microsoft/fhir-server/pull/1695)

## <a name="extended-operations"></a>확장 작업

RESTful API를 확장하는 모든 작업이 지원됩니다.

| 검색 매개 변수 유형 | 지원됨 - PaaS | 지원됨 - OSS(SQL) | 지원됨 - OSS(Cosmos DB) | 설명 |
|------------------------|-----------|-----------|-----------|---------|
| $export(전체 시스템) | 예       | 예       | 예       |         |
| 환자/$export        | 예       | 예       | 예       |         |
| 그룹/$export          | 예       | 예       | 예       |         |
| $convert-data          | 예       | 예       | 예       |         |
| $validate              | 예       | 예       | 예       |         |
| $member 일치          | 예       | 예       | 예       |         |
| $patient-everything    | 아니요        | 아니요        | 예       |         |

## <a name="persistence"></a>지속성

Microsoft FHIR 서버에는 플러그형 지속성 모듈이 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) 있습니다(참조).

현재 FHIR 서버 오픈 소스 코드에는 [Azure Cosmos DB](../../cosmos-db/index-overview.md) 및 [SQL Database](https://azure.microsoft.com/services/sql-database/)대한 구현이 포함되어 있습니다.

Cosmos DB 전역적으로 분산된 다중 모델(SQL API, MongoDB API 등) 데이터베이스입니다. 다양한 일관성 [수준](../../cosmos-db/consistency-levels.md)을 지원합니다. 기본 배포 템플릿은 일관성을 사용하여 FHIR 서버를 `Strong` 구성하지만 요청 헤더를 사용하여 요청에 따라 일관성 정책을 수정(일반적으로 완화)할 수 `x-ms-consistency-level` 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

FHIR 서버는 액세스 제어에 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 사용합니다. 특히 구성 매개 변수가 로 `FhirServer:Security:Enabled` 설정되고 FHIR 서버에 대한 모든 `true` 요청(제외)에 요청 헤더가 로 설정된 경우 RBAC(역할 기반 액세스 `/metadata` 제어)가 `Authorization` 적용됩니다. `Bearer <TOKEN>` 토큰은 클레임에 정의된 역할을 하나 이상 포함해야 `roles` 합니다. 토큰에 지정된 리소스에 대해 지정된 작업을 허용하는 역할이 포함된 경우 요청이 허용됩니다.

현재 지정된 역할에 허용되는 작업은 API에 *전역적으로* 적용됩니다.

## <a name="service-limits"></a>서비스 제한

* [**요청 단위(RUS)**](../../cosmos-db/concepts-limits.md) - 포털에서 Azure API for FHIR 최대 10,000개까지 구성할 수 있습니다. 최소 400개 또는 40개 CPU/GB 중 더 큰 값이 필요합니다. 10,000개 이상의 RU가 필요한 경우 지원 티켓을 제출하여 이를 늘릴 수 있습니다. 사용 가능한 최대값은 1,000,000입니다.

* **번들 크기** - 각 번들은 500개 항목으로 제한됩니다.

* **데이터 크기** - 데이터/문서는 각각 2MB 미만이어야 합니다.

* **구독 제한** - 기본적으로 각 구독은 최대 10개의 FHIR 서버 인스턴스로 제한됩니다. 구독당 더 많은 인스턴스가 필요한 경우 지원 티켓을 열고 요구 사항에 대한 세부 정보를 제공합니다.

* **동시 연결 및 인스턴스** - 기본적으로 클러스터의 두 인스턴스에 15개의 동시 연결이 있습니다(총 30개의 동시 요청). 더 많은 동시 요청이 필요한 경우 지원 티켓을 열고 요구 사항에 대한 세부 정보를 제공합니다.

## <a name="performance-expectations"></a>성능 기대

시스템의 성능은 CPU 수, 동시 연결 및 수행 중인 작업 유형(Put, Post 등)에 따라 달라집니다. 다음은 구성된 RUS에 따라 예상할 수 있는 몇 가지 일반적인 범위입니다. 일반적으로 성능은 다음과 같이 증가하면서 선형적으로 확장됩니다.

| RUS 수 | Resources/sec |    최대 스토리지(GB)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1,000    | 100-150       |      25  |
| 10000   | 225-400       |      250  |
| 100,000  | 2,500-4,000   |      2,500  |

참고: Cosmos DB 요구 사항에 따라 스토리지의 GB당 40 RU/s의 최소 처리량 요구 사항이 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure API for FHIR 지원되는 FHIR 기능에 대해 읽어보았습니다. 다음으로 Azure API for FHIR 배포합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)
