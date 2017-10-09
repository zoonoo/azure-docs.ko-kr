---
title: "Azure Machine Learning Data Preparation로 사용 가능한 지원되는 데이터 대상/출력 | Microsoft Docs"
description: "이 문서는 Azure ML 데이터 준비를 사용하여 지원되는 대상/출력의 전체 목록을 제공합니다."
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
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 415ceba02eb3c8da3de5ab3aa6980fbe5bae2db9
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="supported-data-exports-for-this-preview"></a>이 미리 보기에 대해 지원되는 데이터 내보내기 
여러 다른 형식으로 내보내는 것이 가능합니다. 이러한 형식은 데이터 준비의 중간 결과를 Machine Learning 워크플로의 나머지 부분에 통합하기 전에 유지하는 데 사용할 수 있습니다.

## <a name="types"></a>형식 
### <a name="csv-file"></a>CSV 파일 
쉼표로 구분된 값 파일을 저장소에 쓰기

#### <a name="options"></a>옵션
- 줄 끝
- Null 값을 대체합니다.
- 오류값을 대체합니다. 
- 구분 기호


### <a name="parquet"></a>Parquet ###
데이터 집합을 Parquet으로 저장소에 기록합니다.

Parquet은 형식으로써 저장소에서 다양한 형태를 취할 수 있습니다. 보다 작은 데이터 집합인 경우 단일 '.parquet' 파일이 때로 사용되며 '.parquet' 파일에 읽기/쓰기를 다양 한 python 라이브러리가 지원합니다. 잠시 동안 AMLWB는 로컬 '대화형' 사용 중에 Parquet을 작성하기 위해 PyArrow python 라이브러리에 의존합니다. 다시 말해서 단일 파일 parquet이 로컬 대화형 사용 중에 현재 지원되는 유일한 Parquet 출력 형식입니다.

(Apache Spark에서) 규모 확장 실행 중에 AMLWB는 Apache Spark의 Parquet 읽기/쓰기 기능에 의존합니다. Parquet에 대한 Apache Spark의 기본 출력 형식(현재 하나만 지원됨)은 HIVE 데이터 집합과 구조가 비슷합니다. 다시 말해서 각각이 큰 데이터 집합의 더 작은 파티션에 해당하는 '.parquet' 파일이 많이 포함된 폴더를 의미합니다. 

#### <a name="caveats"></a>주의 사항 ####
Parquet은 형식으로써 비교적 신생이라 서로 다른 라이브러리 간에 일부 구현 불일치가 있습니다. 예를 들어, Apache Spark는 Parquet를 작성할 때 열 이름에 들어가는 유효한 문자에 제한을 두는 한편 PyArrow는 제한을 두지 않습니다. ", {} ()\\n\\t = " 집합 내의 어떠한 문자도 열 이름이 될 수 없습니다.

>[!NOTE]
>Apache Spark와의 호환성을 위해, Parquet에 데이터를 쓸 경우, 열 이름에 이들 문자가 들어 있다면 '_'(밑줄)로 대체 됩니다.**

>[!NOTE]
>앱, Python, 또는 Spark를 통해 로컬 및 Parquet에 쓴 규모 학장 데이터 간에 일관성을 유지하려면 Spark 호환성을 확보할 수 있도록 열 이름은 변경됩니다. Spark에서 Parquet 문자를 쓸 때 예상된 열 이름을 보장하려면 쓰기 전에 열에서 잘못된 문자 집합을 제거해야 합니다.



## <a name="locations"></a>위치 
### <a name="local"></a>로컬 
로컬 하드 드라이브 또는 매핑된 네트워크 저장소 위치

### <a name="azure-blob"></a>Azure BLOB 
Azure Storage(BLOB)는 Azure 구독을 필요로 합니다


