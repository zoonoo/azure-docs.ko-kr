---
title: Azure Machine Learning Data Prep SDK를 사용하여 데이터 쓰기 - Python
description: Azure Machine Learning Data Prep SDK를 사용하여 데이터를 쓰는 방법을 알아봅니다. 데이터 흐름의 모든 지점에서, 지원되는 위치(로컬 파일 시스템, Azure Blob Storage 및 Azure Data Lake Storage)의 파일에 데이터를 쓸 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: da5823473b7f69fa0a6c65d5ea7319bfd2e92720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946768"
---
# <a name="write-data-with-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK를 사용하여 데이터 쓰기
데이터 흐름의 모든 지점에서 데이터를 쓸 수 있습니다. 이러한 쓰기는 결과 데이터 흐름에 단계로 추가되어 데이터 흐름이 있을 때마다 실행됩니다. 파이프라인의 쓰기 단계에 대한 제한이 없기 때문에 간편하게 문제 해결을 위한 중간 결과를 쓰거나 다른 파이프라인에서 선택할 수 있습니다. 각 쓰기 단계를 실행할 때마다 데이터 흐름의 데이터를 끌어오게 됩니다. 예를 들어 쓰기 단계가 3개 있는 데이터 흐름은 데이터 집합의 레코드를 세 번 읽고 처리합니다.

## <a name="writing-to-files"></a>파일에 쓰기
[Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py)를 사용하면 지원되는 위치(로컬 파일 시스템, Azure Blob Storage 및 Azure Data Lake Storage)의 파일에 데이터를 쓸 수 있습니다. 데이터는 병렬 쓰기가 가능하도록 여러 파티션 파일에 기록됩니다. 쓰기가 완료되면 SUCCESS라는 sentinel 파일도 생성됩니다. 이렇게 하면 전체 파이프라인이 완료되기를 기다릴 필요 없이 중간 쓰기가 언제 완료되는지 식별할 수 있습니다.

Spark에서 데이터 흐름을 실행하는 경우 빈 폴더에 써야 합니다. 기존 폴더에 쓰기를 실행하면 작업이 실패합니다. 대상 폴더가 비어 있거나 실행마다 다른 대상 위치를 사용해야 합니다. 그렇지 않으면 쓰기가 실패합니다.

Azure Machine Learning Data Prep SDK는 다음과 같은 파일 형식을 지원합니다.
-   구분 기호로 분리된 파일(CSV, TSV 등)
-   Parquet 파일

이 예에서는 데이터 흐름에 데이터를 로드하는 것부터 시작하겠습니다. 이 데이터를 다른 형식으로 재사용할 것입니다.

```

import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

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

## <a name="delimited-files"></a>구분 기호로 분리된 파일
아래 줄은 쓰기 단계를 사용하여 새 데이터 흐름을 만들지만, 아직 실제 쓰기가 발생하지 않았습니다. 데이터 흐름이 실행되면 쓰기가 실행됩니다.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))
```
이제 쓰기 작업을 실행하는 데이터 흐름을 실행합니다.
```
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
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

작성된 데이터에 올바르게 구문 분석되지 않은 숫자로 인해 여러 가지 숫자 열 오류가 있습니다. CSV로 쓸 때 이러한 null 값은 기본적으로 "ERROR" 문자열로 대체됩니다. 쓰기 호출의 일부로 매개 변수를 추가하고 null 값을 나타내는 데 사용할 문자열을 지정할 수 있습니다.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
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
## <a name="parquet-files"></a>Parquet 파일

 위의 `write_to_csv` 함수와 비슷하게, `write_to_parquet` 함수는 데이터 흐름이 실행될 때 실행되는 쓰기 Parquet 단계가 포함된 새 데이터 흐름을 반환합니다.

```
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```
 이제 쓰기 작업을 실행하는 데이터 흐름을 실행합니다.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```
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
