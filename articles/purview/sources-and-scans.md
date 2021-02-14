---
title: 지원되는 데이터 원본 및 파일 형식
description: 이 문서에서는 부서의 범위에서 지원 되는 데이터 원본 및 파일 형식에 대 한 개념 정보를 제공 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 9a73f9b734d5404d07e05dd37d5ad8571c1aab2e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383894"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Azure 부서의 범위에서 지원 되는 데이터 원본 및 파일 형식

이 문서에서는 부서의 범위에서 지원 되는 데이터 원본, 파일 형식 및 검색 개념에 대해 설명 합니다.

## <a name="supported-data-sources"></a>지원되는 데이터 원본

Azure 부서의 범위는 다음 소스를 지원 합니다.

| 저장소 형식 | 지원 되는 인증 유형 | UX/PowerShell을 통해 검색 설정 |
| ---------- | ------------------- | ------------------------------ |
| 온-프레미스 SQL Server                   | SQL 인증                        | UX                                |
| Azure Synapse Analytics(이전의 SQL DW)            | SQL 인증, 서비스 주체, MSI               | UX                             |
| Azure SQL Database (DB)                  | SQL 인증, 서비스 주체, MSI               | UX |
| Azure SQL Database Managed Instance      | SQL 인증, 서비스 주체, MSI               | UX    |
| Azure Blob Storage                       | 계정 키, 서비스 주체, MSI | UX            |
| Azure Data Explorer                      | 서비스 주체                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | 서비스 사용자, MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | 계정 키, 서비스 주체, MSI            | UX            |
| Azure Cosmos DB                          | 계정 키                                    | UX            |


> [!Note]
> 이제 Azure Data Lake Storage Gen2가 일반 공급됩니다. 오늘부터 사용을 시작하는 것이 좋습니다. 자세한 내용은 [제품 페이지](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)를 참조하세요.

## <a name="file-types-supported-for-scanning"></a>검색에 지원 되는 파일 형식

다음 파일 형식은 검색에 대해 지원 되며, 해당 하는 경우 스키마 추출 및 분류에 대해 지원 됩니다.

- 확장에서 지원 되는 구조화 된 파일 형식: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML
- 확장에서 지원 되는 문서 파일 형식: DOC, .DOCM, .DOCX, DOT, ODP, ODS, ODT, PDF, .POT, PPS, PPSX, PPT, PPTM, .PPTX, XLC, XLS, .XLSB, .XLSM, .XLSX, .XLT
- 부서의 범위은 사용자 지정 파일 확장명 및 사용자 지정 파서도 지원 합니다.

## <a name="sampling-within-a-file"></a>파일 내에서 샘플링

부서의 범위 용어에서
- L1 검색: 파일 이름, 크기 및 정규화 된 이름과 같은 기본 정보 및 메타 데이터를 추출 합니다.
- L2 검색: 구조화 된 파일 유형 및 데이터베이스 테이블에 대 한 스키마를 추출 합니다.
- L3 검색: 해당 하는 경우 스키마를 추출 하 고 샘플링 된 파일에 시스템 및 사용자 지정 분류 규칙을 적용 합니다.

모든 구조적 파일 형식에 대해 부서의 범위 스캐너는 다음과 같은 방식으로 파일을 샘플링 합니다.

- 구조화 된 파일 형식의 경우 각 열에서 128 행을 샘플링 하 고, 하나는 작은 크기의 행을 샘플링 합니다.
- 문서 파일 형식의 경우 각 파일의 20mb를 샘플링 합니다.
    - 문서 파일이 20mb 보다 큰 경우 전체 검색이 적용 되지 않습니다 (분류에 따름). 이 경우 부서의 범위는 파일 이름 및 정규화 된 이름과 같은 기본 메타 데이터만 캡처합니다.

## <a name="resource-set-file-sampling"></a>리소스 집합 파일 샘플링

부서의 범위에서 폴더 또는 파티션 파일 그룹이 시스템 리소스 집합 정책 또는 고객 정의 리소스 집합 정책과 일치 하는 경우 해당 *리소스 집합* 으로 검색 됩니다. 리소스 집합이 검색 되 면 부서의 범위는 포함 된 각 폴더를 샘플링 합니다. 리소스 집합에 대 한 자세한 내용은 [여기](concept-resource-sets.md)를 참조 하세요.

파일 형식에의 한 리소스 집합에 대 한 파일 샘플링:

- 100 파일의 **구분 된 파일 (CSV, psv, SSV, TSV)** -1은 ' 리소스 집합 '으로 간주 되는 폴더 또는 파티션 파일 그룹 내에서 샘플링 (L3 검색) 합니다.
- 18446744073709551615 (long max) 파일의 **Data Lake 파일 형식 (Parquet, Avro, Orc)** -1은 *리소스 집합* 으로 간주 되는 폴더 또는 파티션 파일 그룹 내에서 샘플링 됩니다 (L3 검색).
- 100 파일의 **다른 구조화 된 파일 형식 (JSON, XML, TXT)** -1은 ' 리소스 집합 '으로 간주 되는 파티션 파일의 폴더 또는 그룹 내에서 샘플링 (L3 검색) 합니다.
- **SQL 개체 및 CosmosDB 엔터티** -각 파일은 L3 검색 됩니다.
- **문서 파일 형식** -각 파일은 L3에서 검색 됩니다. 리소스 집합 패턴은 이러한 파일 형식에 적용 되지 않습니다.

## <a name="scan-regions"></a>지역 검사
다음은 부서의 범위 스캐너가 실행 되는 모든 Azure 데이터 원본 (데이터 센터) 영역 목록입니다. Azure 데이터 원본이이 목록 외부 영역에 있는 경우 스캐너가 부서의 범위 인스턴스의 지역에서 실행 됩니다.
 
### <a name="purview-scanner-regions"></a>부서의 범위 스캐너 지역

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- WestEurope
- NorthEurope
- Storage.westcentralus
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>분류

모든 105 시스템 분류 규칙은 구조적 파일 형식에 적용 됩니다. MCE 분류 규칙만 문서 파일 형식에 적용 됩니다 (데이터 검색 기본 regex 패턴, 블 룸 필터 기반 검색에는 적용 되지 않음). 지원 되는 분류에 대 한 자세한 내용은 [Azure 부서의 범위에서 지원 되는 분류](supported-classifications.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [자습서: 시작 키트를 실행 하 고 데이터를 검색 합니다.](tutorial-scan-data.md)
- [Azure 부서의 범위 (미리 보기)에서 데이터 원본 관리](manage-data-sources.md)