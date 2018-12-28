---
title: '쓰기: Data Prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Data Prep SDK를 사용하여 데이터를 쓰는 방법을 알아봅니다. 데이터 흐름의 모든 지점에서, 지원되는 위치(로컬 파일 시스템, Azure Blob Storage 및 Azure Data Lake Storage)의 파일에 데이터를 쓸 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a6abdaff986df3c457a0118f6e143fe4ff0daf49
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384346"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning 데이터 준비 SDK를 사용하여 데이터 쓰기

이 문서에서는 Azure Machine Learning Data Prep SDK를 사용하여 데이터를 쓰는 다양한 방법을 알아봅니다. 출력 데이터는 데이터 흐름의 아무 지점에서나 쓸 수 있으며, 쓰기는 결과 데이터 흐름에 단계로 추가되어 데이터 흐름이 있을 때마다 실행됩니다. 데이터는 병렬 쓰기가 가능하도록 여러 파티션 파일에 기록됩니다.

파이프라인에 포함되는 쓰기 단계의 수에는 제한이 없으므로 문제 해결 또는 다른 파이프라인에 대한 중간 결과를 얻기 위해 추가 쓰기 단계를 쉽게 추가할 수 있습니다.

쓰기 단계를 실행할 때마다 데이터 흐름에서 데이터를 완전하게 가져옵니다. 예를 들어 쓰기 단계가 3개 있는 데이터 흐름은 데이터 세트의 레코드를 세 번 읽고 처리합니다.

## <a name="supported-data-types-and-location"></a>지원되는 데이터 형식 및 위치

지원되는 파일 형식은 다음과 같습니다.
-   구분 기호로 분리된 파일(CSV, TSV 등)
-   Parquet 파일

[Azure Machine Learning 데이터 준비 Python SDK](https://aka.ms/data-prep-sdk)를 사용하여 데이터를 쓸 수 있는 위치는 다음과 같습니다.
+ 로컬 파일 시스템
+ Azure Blob Storage
+ Azure Data Lake 저장소

## <a name="spark-considerations"></a>Spark 고려 사항

Spark에서 데이터 흐름을 실행하는 경우 빈 폴더에 써야 합니다. 기존 폴더에 쓰기를 실행하려고 시도하면 오류가 발생합니다. 대상 폴더가 비어 있거나 실행마다 다른 대상 위치를 사용해야 합니다. 그렇지 않으면 쓰기가 실패합니다.

## <a name="monitoring-write-operations"></a>쓰기 작업 모니터링

편의를 위해 쓰기가 완료되면 SUCCESS라는 sentinel 파일이 생성됩니다. 이렇게 하면 전체 파이프라인이 완료될 때까지 기다리지 않고 중간 쓰기가 완료된 시점을 확인할 수 있습니다.

## <a name="example-write-code"></a>쓰기 코드 예제

이 예에서는 데이터 흐름에 데이터를 로드하는 것부터 시작하겠습니다. 이 데이터를 다른 형식으로 재사용합니다.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```

예제 출력:
|   |  열1 |    열2 | 열3 | 열4  |열5   | 열6 | 열7 | 열8 | 열9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   없음|       아니요|     아니요  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   없음|       아니요|     아니요  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    없음|   아니요| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    없음|   아니요| 아니요| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    없음|   아니요| 아니요| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    없음|   아니요| 아니요| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   없음|   아니요| 아니요|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    없음|   아니요| 아니요| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    없음|   아니요| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    없음|   아니요| SV|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>구분 기호로 분리된 파일 예제

다음 코드는 `write_to_csv` 함수를 사용하여 구분 기호로 분리된 파일에 데이터를 씁니다.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

예제 출력:
|   |  열1 |    열2 | 열3 | 열4  |열5   | 열6 | 열7 | 열8 | 열9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   오류 |       아니요|     아니요  |   ENRS    |오류    |   오류 |   오류|    
|   1|      10003.0 |   99999.0 |   오류 |       아니요|     아니요  |   ENSO|       오류|        오류 |오류|   
|   2|  10010.0|    99999.0|    오류 |   아니요| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    오류 |   아니요| 아니요| |   오류|    오류|    오류|
|4| 10014.0|    99999.0|    오류 |   아니요| 아니요| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    오류 |   아니요| 아니요| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   오류 |   아니요| 아니요|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    오류 |   아니요| 아니요| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    오류 |   아니요| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    오류 |   아니요| SV|     |77000.0|   15500.0|    120.0|

앞의 출력에서 올바르게 구문 분석되지 않은 숫자로 인해 숫자 열에 몇 가지 오류가 나타나는 것을 볼 수 있습니다. CSV로 쓸 때 null 값은 기본적으로 "ERROR" 문자열로 대체됩니다.

쓰기 호출의 일부로 매개 변수를 추가하고 null 값을 나타내는 데 사용할 문자열을 지정합니다.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

앞의 코드에서 생성하는 출력은 다음과 같습니다.
|   |  열1 |    열2 | 열3 | 열4  |열5   | 열6 | 열7 | 열8 | 열9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       아니요|     아니요  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       아니요|     아니요  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   아니요| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   아니요| 아니요| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   아니요| 아니요| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   아니요| 아니요| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   아니요| 아니요|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   아니요| 아니요| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   아니요| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   아니요| SV|     |77000.0|   15500.0|    120.0|

### <a name="parquet-file-example"></a>Parquet 파일 예제

`write_to_csv`와 비슷하게, `write_to_parquet` 함수는 데이터 흐름이 실행될 때 실행되는 쓰기 Parquet 단계가 포함된 새 데이터 흐름을 반환합니다.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

데이터 흐름을 실행하여 쓰기 작업을 시작합니다.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

앞의 코드에서 생성하는 출력은 다음과 같습니다.
|   |  열1 |    열2 | 열3 | 열4  |열5   | 열6 | 열7 | 열8 | 열9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       아니요|     아니요  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       아니요|     아니요  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   아니요| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   아니요| 아니요| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   아니요| 아니요| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   아니요| 아니요| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   아니요| 아니요|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   아니요| 아니요| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   아니요| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   아니요| SV|     |77000.0|   15500.0|    120.0|
