---
title: "Azure Machine Learning 데이터 준비에서 사용 가능한 지원되는 데이터 원본 | Microsoft Docs"
description: "이 문서에서는 Azure ML 데이터 준비에서 사용 가능한 지원되는 데이터 원본의 전체 목록을 제공합니다"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0a77b31e07f118155094fab4d3393ffdb37c9c6f
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="supported-data-sources-for-this-release"></a>이 릴리스에 대해 지원되는 데이터 원본 
다음 문서에서는 데이터 준비에서 현재 지원되는 데이터 원본 목록을 간단하게 설명합니다.

이 릴리스에 대해 지원되는 데이터 원본은 아래에 나와 있습니다.

## <a name="types"></a>형식 
### <a name="directory-vs-file"></a>디렉터리 및 파일
파일/디렉터리 - 단일 파일을 선택하여 데이터 준비에서 읽고 파일 유형을 구문 분석하여 다음 화면에 있는 파일 연결에 대한 기본 매개 변수를 확인합니다. 디렉터리 또는 디렉터리 내의 파일 집합을 선택합니다(파일 선택기로 다중 선택 가능). 두 방식의 결과로 서로 추가된 파일(필요한 경우 헤더 제거)을 사용하여 단일 데이터 흐름으로 파일을 읽습니다.

지원되는 파일 형식은 다음과 같습니다.
- 구분된 파일(csv, tsv, txt 등) 
- 고정 폭
- 일반 텍스트
- JSON 파일

### <a name="csv-file"></a>CSV 파일
저장소에서 쉼표로 구분된 값 파일을 읽습니다.

#### <a name="options"></a>옵션
- 구분 기호
- 주석
- 헤더
- 소수점 기호
- 파일 인코딩
- 줄 건너뛰기

### <a name="tsv-file"></a>TSV 파일
저장소에서 탭으로 구분된 값 파일을 읽습니다.

#### <a name="options"></a>옵션
- 주석
- 헤더
- 파일 인코딩
- 줄 건너뛰기

### <a name="excel-xlsxlsx"></a>Excel(.xls/xlsx)
Excel 파일에서 시트 이름 또는 번호를 지정하여 한 번에 하나의 시트를 읽습니다.

#### <a name="options"></a>옵션
- 시트 이름/번호
- 헤더
- 줄 건너뛰기

### <a name="json-file"></a>JSON 파일
저장소에서 JSON 파일을 읽습니다. "일반" 읽기 파일입니다.

#### <a name="options"></a>옵션
- 없음

### <a name="parquet"></a>Parquet
Parquet 데이터 집합(단일 파일 또는 폴더)을 읽습니다.

Parquet 형식은 저장소에서 다양한 형태가 될 수 있습니다. 작은 데이터 집합의 경우 단일 '.parquet' 파일이 사용되기도 하며 다양한 python 라이브러리는 단일 '.parquet' 파일에 대한 읽기/쓰기를 지원합니다. 잠시 동안 AMLWB는 로컬 '대화형'을 사용하는 동안 Parquet을 읽는 경우 PyArrow python 라이브러리에 따라 다릅니다. Parquet 데이터 집합뿐만 아니라 단일 '.parquet' 파일(큰 데이터 집합의 일부가 아니고 일반적으로 작성된 경우)도 지원합니다. Parquet 데이터 집합은 각각 큰 데이터 집합의 작은 파티션을 나타내는 둘 이상의 '.parquet' 파일의 컬렉션입니다. 데이터 집합은 일반적으로 폴더에 포함되고 Spark 및 Hive와 같이 공통 플랫폼의 기본 parquet 출력 형식입니다.

>[!NOTE]
>여러 개의 '.parquet' 파일이 있는 폴더의 Parquet 데이터를 읽는 경우 읽을 디렉터리를 선택하고 'Parquet 데이터 집합' 옵션을 선택하는 것이 가장 안전합니다. 이렇게 하면 PyArrow가 개별 파일이 아닌 전체 폴더를 읽을 수 있고 더 복잡한 방식으로 저장된 디스크의 Parquet(예: 폴더 파티션)을 읽을 수 있습니다.

확장 실행은 Spark의 Parquet 읽기 기능을 사용하며 로컬 대화형과 유사하게 폴더 및 단일 파일을 지원합니다.

#### <a name="options"></a>옵션
- Parquet 데이터 집합
  - 이 옵션을 통해 AMLWB가 지정된 디렉터리를 확장하여 해당 파일을 개별적으로 읽을지(선택 안 함 모드) 또는 디렉터리를 전체 데이터 집합으로 처리하여 PyArrow가 파일을 해석하는 최상의 방법을 찾을지(선택 모드)를 결정합니다.


## <a name="locations"></a>위치
### <a name="local"></a>Local
로컬 하드 드라이브 또는 매핑된 네트워크 저장소 위치

### <a name="azure-blob"></a>Azure BLOB
Azure Storage(BLOB), Azure 구독 필요


