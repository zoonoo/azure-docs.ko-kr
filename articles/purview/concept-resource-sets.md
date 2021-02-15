---
title: 리소스 집합 이해
description: 이 문서에서는 리소스 집합 및 Azure 부서의 범위를 만드는 방법에 대해 설명 합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 39a1400bfe642672ba276686851d4f0edebbeb00
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387855"
---
# <a name="understanding-resource-sets"></a>리소스 집합 이해

이 문서에서는 Azure 부서의 범위가 리소스 집합을 사용 하 여 데이터 자산을 논리적 리소스에 매핑하는 방법을 이해 하는 데 도움을 줍니다.
## <a name="background-info"></a>배경 정보

규모에 맞게 데이터 처리 시스템은 일반적으로 디스크에 단일 테이블을 여러 파일로 저장 합니다. 이 개념은 리소스 집합을 사용 하 여 Azure 부서의 범위에 표시 됩니다. 리소스 집합은 저장소의 자산을 많이 나타내는 카탈로그의 단일 개체입니다.

예를 들어 Spark 클러스터가 Azure DataL Lake Storage (ADLS) Gen2 데이터 원본에 데이터 프레임를 유지 했다고 가정 합니다. Spark에서 테이블은 단일 논리 리소스 처럼 보이지만, 디스크에는 총 데이터 프레임의 내용에 대 한 파티션을 나타내는 수천 개의 Parquet 파일이 있습니다. IoT 데이터와 웹 로그 데이터는 동일한 챌린지를 가집니다. 로그 파일을 여러 번 출력 하는 센서가 있다고 가정해 봅니다. 해당 단일 센서에서 수백 개의 로그 파일을 포함할 때까지 시간이 오래 걸리지 않습니다.

Azure 부서의 범위는 리소스 집합을 사용 하 여 다 수의 데이터 자산을 단일 논리적 리소스로 매핑하는 과제를 해결 합니다.

## <a name="how-azure-purview-detects-resource-sets"></a>Azure 부서의 범위에서 리소스 집합을 검색 하는 방법

Azure 부서의 범위는 Azure Blob Storage, ADLS Gen1 및 ADLS Gen2에서 리소스 집합 검색을 지원 합니다.

Azure 부서의 범위는 검색할 때 리소스 집합을 자동으로 검색 합니다. 이 기능은 검색을 통해 수집 하는 모든 데이터를 확인 하 고 정의 된 패턴 집합과 비교 합니다.

예를 들어 URL이 인 데이터 원본을 검색 한다고 가정 합니다 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure 부서의 범위는 경로 세그먼트를 확인 하 고 모든 기본 제공 패턴과 일치 하는지 확인 합니다. Guid, 숫자, 날짜 형식, 지역화 코드 (예: en-us) 등의 기본 제공 패턴을 포함 합니다. 이 경우 숫자 패턴은 *23* 과 일치 합니다. Azure 부서의 범위는이 파일이 이라는 리소스 집합에 포함 되어 있다고 가정 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` 합니다.

또는와 같은 URL의 경우, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` Azure 부서의 범위는 지역화 패턴과 숫자 패턴을 모두 일치 시켜 라는 리소스 집합을 생성 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` 합니다.

Azure 부서의 범위는이 전략을 사용 하 여 다음 리소스를 동일한 리소스 집합에 매핑합니다 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Azure 부서의 범위가 리소스 집합으로 검색 하지 않는 파일 형식

부서의 범위는 Word, Excel 또는 PDF와 같은 대부분의 문서 파일 형식을 리소스 집합으로 분류 하려고 시도 하지 않습니다. 이 예외는 일반적인 분할 된 파일 형식 이므로 CSV 형식입니다.

## <a name="how-azure-purview-scans-resource-sets"></a>Azure 부서의 범위에서 리소스 집합을 검색 하는 방법

Azure 부서의 범위가 리소스 집합의 일부로 간주 되는 리소스를 검색 하면 전체 검색에서 샘플 검색으로 전환 됩니다. 샘플 검색에서는 리소스 집합에 있는 것으로 간주 되는 파일의 하위 집합만 엽니다. 열려 있는 각 파일에 대해 해당 스키마를 사용 하 고 해당 분류자를 실행 합니다. 그런 다음 Azure 부서의 범위는 열려 있는 리소스에서 최신 리소스를 찾고 해당 리소스의 스키마와 해당 리소스의 스키마를 사용 하 여 카탈로그에 있는 전체 리소스 집합에 대 한 항목을 분류 합니다.

## <a name="what-azure-purview-stores-about-resource-sets"></a>리소스 집합에 대 한 Azure 부서의 범위 저장소

Azure 부서의 범위는 단일 스키마 및 분류 외에도 리소스 집합에 대 한 다음 정보를 저장 합니다.

- 자세히 검색 한 최신 파티션 리소스의 데이터입니다.
- 리소스 집합을 구성 하는 파티션 리소스에 대 한 정보를 집계 합니다.
- 검색 된 파티션 리소스의 수를 표시 하는 파티션 수입니다.
- 심층 검색을 수행한 샘플 집합에 있는 고유한 스키마 수를 표시 하는 스키마 수입니다. 이 값은 1 ~ 5 사이의 숫자 이거나 5 보다 큰 값의 경우입니다.
- 하나 이상의 파티션 유형이 리소스 집합에 포함 된 경우 파티션 유형 목록입니다. 예를 들어 IoT 센서는 모두 논리적으로 동일한 리소스 집합에 포함 되지만 XML 및 JSON 파일을 모두 출력할 수 있습니다.

## <a name="built-in-resource-set-patterns"></a>기본 제공 리소스 집합 패턴

Azure 부서의 범위는 다음과 같은 리소스 집합 패턴을 지원 합니다. 이러한 패턴은 디렉터리의 이름 또는 파일 이름의 일부로 표시 될 수 있습니다.
### <a name="regex-based-patterns"></a>Regex 기반 패턴

| 패턴 이름 | 표시 이름 | 설명 |
|--------------|--------------|-------------|
| GUID         | EID       | [RFC 4122](https://tools.ietf.org/html/rfc4122) 에 정의 된 guid (globally unique identifier) |
| 숫자       | 개의          | 하나 이상의 숫자 |
| 날짜/시간 형식 | 연도가 월 동안 개의     | 다양 한 날짜/시간 형식을 지원 하지만 모두는 {Year} [delimiter] {Month} [delimiter] {Day} 또는 일련의 {N} s로 표시 됩니다. |
| 4ByteHex     | 16 진수        | 4 자리 16 진수 숫자입니다. |
| 지역화 | {LOC}        | [BCP 47](https://tools.ietf.org/html/bcp47)에 정의 된 언어 태그,-및 _ 이름이 모두 지원 됩니다 (예: en_ca 및 en-ca). |

### <a name="complex-patterns"></a>복합 패턴

| 패턴 이름 | 표시 이름 | 설명 |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Spark 파티션 파일 식별자 |
| Date (yyyy/mm/dd) InPath  | {Year}/{1}/{day} | 여러 폴더에 걸쳐 있는 년/월/일 패턴 |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Azure 부서의 범위 Catalog에서 리소스 집합을 표시 하는 방법

Azure 부서의 범위는 자산 그룹을 리소스 집합으로 일치 시킬 때 카탈로그에서 표시 이름으로 사용 하기에 가장 유용한 정보를 추출 하려고 시도 합니다. 기본 명명 규칙의 몇 가지 예는 다음과 같습니다. 

### <a name="example-1"></a>예 1

정규화 된 이름: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

표시 이름: "spark 출력 이름"

### <a name="example-2"></a>예제 2

정규화 된 이름: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

표시 이름: "my 분할 된 데이터"

### <a name="example-3"></a>예제 3

정규화 된 이름: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

표시 이름: "data"

## <a name="known-issues-with-resource-sets"></a>리소스 집합의 알려진 문제

대부분의 경우 리소스 집합이 잘 작동 하지만 Azure 부서의 범위에서 다음과 같은 문제가 발생할 수 있습니다.

- 자산을 리소스 집합으로 잘못 표시 합니다.
- 잘못 된 리소스 집합에 자산을 넣습니다.
- 자산이 리소스 집합이 아닌 것으로 잘못 표시

## <a name="next-steps"></a>다음 단계

Azure 부서의 범위를 시작 하려면 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
