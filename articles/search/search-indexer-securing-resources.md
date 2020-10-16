---
title: 보호 된 리소스에 대 한 인덱서 액세스
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 인덱서를 통한 Azure 데이터 액세스를 위한 네트워크 수준 보안 옵션에 대 한 개념적 개요입니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 2fb94faacc2bc7d6c3b1e166e617f3f675594cef
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101259"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Azure 네트워크 보안 기능 (Azure Cognitive Search)을 통해 보호 되는 콘텐츠에 대 한 인덱서 액세스

Azure Cognitive Search 인덱서는 실행 하는 동안 다양 한 Azure 리소스에 대 한 아웃 바운드 호출을 수행할 수 있습니다. 이 문서에서는 IP 방화벽, 개인 끝점 또는 기타 Azure 네트워크 수준 보안 메커니즘으로 보호 되는 콘텐츠에 대 한 인덱서 액세스의 개념에 대해 설명 합니다. 인덱서는 인덱싱을 수행 하는 동안 데이터 원본에 연결 하 고 기술을 통해 캡슐화 된 코드에 연결 하는 두 가지 상황에서 아웃 바운드 호출을 수행 합니다. 인덱서가 일반적인 실행에서 액세스할 수 있는 모든 가능한 리소스 종류의 목록은 아래 표에 나와 있습니다.

| 리소스 | 인덱서 실행 내의 목적 |
| --- | --- |
| Azure Storage (blob, tables, ADLS Gen 2) | 데이터 원본 |
| Azure Storage (blob, 테이블) | 기술력과 (보강 문서 캐싱 및 기술 자료 저장소 저장 예측) |
| Azure Cosmos DB (다양 한 Api) | 데이터 원본 |
| Azure SQL Database | 데이터 원본 |
| Azure 가상 머신의 SQL Server | 데이터 원본 |
| SQL Managed Instance | 데이터 원본 |
| Azure Functions | 사용자 지정 web api 기술에 대 한 호스트 |
| Cognitive Services | 20 개의 무료 문서 제한을 초과 하 여 보강를 청구 하는 데 사용 되는 기술에 연결 됩니다. |

> [!NOTE]
> 기술에 연결 된 인지 서비스 리소스는 검색 인덱스에 대해 수행 되 고 작성 된 강화을 기준으로 청구에 사용 됩니다. Cognitive Services API에 액세스 하는 데 사용 되지 않습니다. 데이터를 전송 중에 강력 하 게 암호화 하 고 미사용 상태로 저장 하지 않는 내부 보안 통신 채널을 통해 Cognitive Services API에 대 한 인덱서의 보강 파이프라인에서 액세스 합니다.

고객은 Azure에서 제공 하는 여러 네트워크 격리 메커니즘을 통해 이러한 리소스를 보호할 수 있습니다. 인식 서비스 리소스를 제외 하 고, 인덱서는 네트워크에 격리 된 경우에도 다른 모든 리소스에 액세스 하는 제한 된 기능을 제공 합니다. 아래 표에 간략하게 설명 되어 있습니다.

| 리소스 | IP 제한 | 프라이빗 엔드포인트 |
| --- | --- | ---- |
| Azure Storage (blob, tables, ADLS Gen 2) | 저장소 계정 및 검색 서비스가 서로 다른 지역에 있는 경우에만 지원 됩니다. | 지원됨 |
| Azure Cosmos DB-SQL API | 지원됨 | 지원됨 |
| Azure Cosmos DB-Cassandra, Mongo 및 Gremlin API | 지원됨 | 지원되지 않음 |
| Azure SQL Database | 지원됨 | 지원됨 |
| Azure 가상 머신의 SQL Server | 지원됨 | N/A |
| SQL Managed Instance | 지원됨 | N/A |
| Azure Functions | 지원됨 | 지원 됨, 특정 계층의 Azure 함수에만 해당 |

> [!NOTE]
> 네트워크 보안 Azure Storage 계정에 대해 위에 나열 된 옵션 외에도, 고객은 Azure Cognitive Search가 신뢰할 수 있는 [Microsoft 서비스](../storage/common/storage-network-security.md#trusted-microsoft-services)라는 사실을 활용할 수 있습니다. 즉, 저장소 계정에서 적절 한 역할 기반 액세스 제어를 사용 하도록 설정한 경우 특정 검색 서비스는 저장소 계정에 대 한 가상 네트워크 또는 IP 제한을 무시 하 고 저장소 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [신뢰할 수 있는 서비스 예외를 사용 하는 인덱서 연결](search-indexer-howto-access-trusted-service-exception.md)을 참조 하세요. 저장소 계정 또는 검색 서비스를 다른 지역으로 이동할 수 없는 경우에는 IP 제한 경로 대신이 옵션을 사용할 수 있습니다.

인덱서에서 사용 해야 하는 보안 액세스 메커니즘을 선택 하는 경우 다음 제약 조건을 고려 합니다.

- 인덱서는 [가상 네트워크 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)에 연결할 수 없습니다. 자격 증명, 개인 끝점, 신뢰할 수 있는 서비스 및 IP 주소 지정을 사용 하는 공용 끝점은 인덱서 연결에 대해 지원 되는 유일한 방법입니다.
- 검색 서비스는 가상 머신에서 기본적으로 실행 되는 특정 가상 네트워크에 프로 비전 할 수 없습니다. 이 기능은 Azure Cognitive Search에서 제공 되지 않습니다.
- 인덱서를 사용 하 여 리소스에 액세스 하는 경우 추가 [개인 링크 요금이](https://azure.microsoft.com/pricing/details/search/) 적용 될 수 있습니다.

## <a name="indexer-execution-environment"></a>인덱서 실행 환경

Azure Cognitive Search 인덱서는 데이터 원본에서 콘텐츠를 효율적으로 추출 하 고, 추출 된 콘텐츠에 강화을 추가 하 고, 선택적으로 검색 인덱스에 결과를 쓰기 전에 프로젝션을 생성할 수 있습니다. 인덱서에 할당 된 책임의 수에 따라 다음 두 환경 중 하나에서 실행할 수 있습니다.

- 특정 검색 서비스에 전용으로 적용 되는 환경입니다. 이러한 환경에서 실행 되는 인덱서는 다른 작업 (예: 고객에서 시작 된 인덱싱 또는 쿼리 작업 쿼리)과 리소스를 공유 합니다. 일반적으로 텍스트 기반 인덱싱 (예: 기술를 사용 하지 않음)을 수행 하는 인덱서만이 환경에서 실행 됩니다.
- 기술력과를 사용 하는 것과 같이 리소스를 많이 사용 하는 인덱서를 호스트 하는 다중 테 넌 트 환경입니다. 이 환경은 계산 집약적 처리를 오프 로드 하는 데 사용 되며 서비스별 리소스는 일상적인 작업에 사용할 수 있습니다. 이 다중 테 넌 트 환경은 고객에 게 추가 비용 없이 Microsoft에서 관리 하 고 보호 합니다.

지정 된 인덱서 실행에 대해 Azure Cognitive Search는 인덱서를 실행할 최상의 환경을 결정 합니다. IP 방화벽을 사용 하 여 Azure 리소스에 대 한 액세스를 제어 하는 경우 실행 환경에 대 한 정보는 둘 모두를 포함 하는 IP 범위를 설정 하는 데 도움이 됩니다.

## <a name="granting-access-to-indexer-ip-ranges"></a>인덱서 IP 범위에 대 한 액세스 권한 부여

인덱서가 액세스 하려는 리소스가 특정 범위의 IP 범위로만 제한 되는 경우 인덱서 요청이 시작 될 수 있는 가능한 IP 범위를 포함 하도록 집합을 확장 해야 합니다. 위에서 설명한 것 처럼 인덱서를 실행 하 고 액세스 요청을 시작할 수 있는 두 가지 환경을 사용할 수 있습니다. 인덱서 액세스가 작동 하려면 **두** 환경의 IP 주소를 추가 해야 합니다.

- 검색 서비스별 개인 환경의 IP 주소를 가져오려면 `nslookup` `ping` 검색 서비스의 FQDN (정규화 된 도메인 이름)을 지정 합니다. 예를 들어 공용 클라우드에서 검색 서비스의 FQDN은 `<service-name>.search.windows.net` 입니다. 이 정보는 Azure Portal에서 사용할 수 있습니다.
- 다중 테 넌 트 환경의 IP 주소는 서비스 태그를 통해 사용할 수 있습니다 `AzureCognitiveSearch` . [Azure 서비스 태그](../virtual-network/service-tags-overview.md) 에는 각 서비스에 대 한 IP 주소의 게시 된 범위가 있습니다 .이는 [검색 API (미리 보기)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 또는 [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 통해 사용할 수 있습니다. 두 경우 모두, IP 범위는 지역별 분할 됩니다. 검색 서비스가 프로 비전 되는 지역에 할당 된 IP 범위만 선택할 수 있습니다.

특정 데이터 원본의 경우에는 IP 범위 목록을 열거 하는 대신 서비스 태그 자체를 직접 사용할 수 있습니다. 검색 서비스의 IP 주소는 명시적으로 사용 해야 합니다. 이러한 데이터 원본은 Azure Storage, Cosmos DB, Azure SQL 등에서 제공 하는 것과 같은 IP 규칙과 달리 기본적으로 서비스 태그 추가를 지 원하는 [네트워크 보안 그룹 규칙](../virtual-network/network-security-groups-overview.md)을 설정 하는 방법으로 액세스를 제한 합니다. `AzureCognitiveSearch`검색 서비스 IP 주소 외에도 서비스 태그를 직접 활용 하는 기능을 지 원하는 데이터 원본은 다음과 같습니다.

- [Azure 가상 컴퓨터의 SQL Server](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL Managed Instances](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

이 연결 옵션에 대 한 자세한 내용은 [IP 방화벽을 통한 인덱서 연결](search-indexer-howto-access-ip-restricted.md)을 참조 하세요.

## <a name="granting-access-via-private-endpoints"></a>전용 끝점을 통해 액세스 권한 부여

인덱서는 [전용 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 리소스에 액세스 하 고, 가상 네트워크를 선택 하거나, 공용 액세스를 사용 하도록 설정 하지 않은 리소스에 액세스할 수 있습니다.
이 기능은 청구 가능한 검색 서비스 에서만 사용할 수 있으며 생성 되는 전용 끝점 수에는 제한이 있습니다. 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md#shared-private-link-resource-limits)을 참조 하세요.

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>1 단계: 보안 리소스에 대 한 개인 끝점 만들기

고객은 보안 리소스 (예: 저장소 계정)에 대 한 개인 끝점 연결을 만들기 위해 **공유 개인 링크 리소스**에서 검색 관리 작업 인 [CREATEORUPDATE API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) 를 호출 해야 합니다. 이 (아웃 바운드) 개인 끝점 연결을 통해 이동 하는 트래픽은 검색 서비스별 "private" 인덱서 실행 환경에 있는 가상 네트워크 에서만 시작 됩니다.

Azure Cognitive Search는이 API의 호출자에 게 보안 리소스에 대 한 개인 끝점 연결 요청을 승인 하기 위한 RBAC 권한이 있는지 확인 합니다. 예를 들어 읽기 전용 권한을 사용 하 여 저장소 계정에 대 한 개인 끝점 연결을 요청 하면이 호출이 거부 됩니다.

### <a name="step-2-approve-the-private-endpoint-connection"></a>2 단계: 개인 끝점 연결 승인

공유 개인 링크 리소스를 만드는 (비동기) 작업이 완료 되 면 "보류 중" 상태로 개인 끝점 연결이 생성 됩니다. 아직 연결을 통해 트래픽이 전달 되지 않습니다.
그러면 고객은 보안 리소스에서이 요청을 찾고 "승인" 해야 합니다. 일반적으로이 작업은 Azure Portal 또는 [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection)를 통해 수행할 수 있습니다.

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>3 단계: "개인" 환경에서 강제로 인덱서 실행

승인 된 개인 끝점은 특정 형식의 네트워크 수준 액세스 제한이 있는 리소스 (예: 특정 가상 네트워크 에서만 액세스 하도록 구성 된 저장소 계정 데이터 원본)로 검색 서비스에서 나가는 호출을 수행할 수 있습니다.
즉, 개인 끝점을 통해 이러한 데이터 소스에 연결할 수 있는 인덱서가 성공 합니다.
개인 끝점이 승인 되지 않은 경우 또는 인덱서가 개인 끝점 연결을 사용 하지 않는 경우 인덱서 실행이에서 종료 됩니다 `transientFailure` .

인덱서가 개인 끝점 연결을 통해 리소스에 액세스할 수 있도록 하려면 인덱서의를로 설정 하 여 `executionEnvironment` `"Private"` 모든 인덱서 실행이 개인 끝점을 활용할 수 있도록 해야 합니다. 개인 끝점은 개인 검색 서비스 특정 환경 내에서 프로 비전 되기 때문입니다.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

이러한 단계는 [개인 끝점을 통한 인덱서 연결](search-indexer-howto-access-private.md)에 자세히 설명 되어 있습니다.
리소스에 대 한 개인 끝점이 승인 되 면 개인 끝점 연결을 통해 액세스를 가져오기 위해 *전용* 으로 설정 된 인덱서가 있습니다.

### <a name="limits"></a>제한

검색 서비스의 성능 및 안정성을 최적화 하기 위해 다음 차원에 대 한 제한 사항이 검색 서비스 계층에 의해 적용 됩니다.

- *전용*으로 설정할 수 있는 인덱서의 종류입니다.
- 만들 수 있는 공유 개인 링크 리소스의 수입니다.
- 공유 개인 링크 리소스를 만들 수 있는 고유한 리소스 종류의 수입니다.

이러한 제한에 대해서는 [서비스 제한](search-limits-quotas-capacity.md)에 설명 되어 있습니다.

## <a name="next-steps"></a>다음 단계

- [IP 방화벽을 통한 인덱서 연결](search-indexer-howto-access-ip-restricted.md)
- [신뢰할 수 있는 서비스 예외를 사용 하 여 인덱서 연결](search-indexer-howto-access-trusted-service-exception.md)
- [개인 끝점에 대 한 인덱서 연결](search-indexer-howto-access-private.md)