---
title: OJ Sales Simulated
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 OJ Sales Simulated 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 8f835e12bbfd17474fd2b928809394c93c19eae5
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039061"
---
# <a name="oj-sales-simulated"></a>OJ Sales Simulated 

이 데이터 세트는 Dominick의 OJ 데이터 세트에서 파생된 것이며, Azure Machine Learning에서 수천 개의 모델을 동시에 학습시킬 수 있는 시뮬레이션된 추가 데이터를 포함합니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

데이터에는 121주 동안의 오렌지 주스 주간 판매가 포함됩니다. 포함된 상점 수가 3,991개이고 상점당 오렌지 주스 브랜드가 3개이므로 11,973개 모델을 학습시킬 수 있습니다.

[원래 데이터 집합 설명을 보거나](http://www.cs.unitn.it/~taufer/QMMA/L10-OJ-Data.html) [데이터 세트를 다운로드](http://www.cs.unitn.it/~taufer/Data/oj.csv)합니다.

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| Advert | int |  | 1 | 해당 주 동안 오렌지 주스에 대한 광고가 없는지 여부를 나타내는 값입니다. 0: 광고 없음 1: 광고 |
| 브랜드 | 문자열 |  | dominicks tropicana | 오렌지 주스 브랜드 |
| 가격 | double |  | 2.6 2.09 | 오렌지 주스 가격(USD) |
| 수량 | int |  | 10939 11638 | 해당 주에 판매된 오렌지 주스의 수량 |
| 수익 | double |  | 38438.4 36036.0 | 해당 주의 오렌지 주스 판매 수익(USD) |
| 스토어 | int |  | 2658 1396 | 오렌지 주스가 판매된 상점 번호 |
| WeekStarting | timestamp |  | 1990-08-09 00:00:00 1992-02-20 00:00:00 | 판매가 관련된 주를 나타내는 데이터 |

## <a name="preview"></a>미리 보기

| WeekStarting | 스토어 | 브랜드 | 수량 | Advert | 가격 | 수익 |
|-|-|-|-|-|-|-|
| 10/1/1992 12:00:00 AM | 3571 | minute.maid | 13247 | 1 | 2.42 | 32057.74 |
| 10/1/1992 12:00:00 AM | 2999 | minute.maid | 18461 | 1 | 2.69 | 49660.09 |
| 10/1/1992 12:00:00 AM | 1198 | minute.maid | 13222 | 1 | 2.64 | 34906.08 |
| 10/1/1992 12:00:00 AM | 3916 | minute.maid | 12923 | 1 | 2.45 | 31661.35 |
| 10/1/1992 12:00:00 AM | 1688 | minute.maid | 9380 | 1 | 2.46 | 23074.8 |
| 10/1/1992 12:00:00 AM | 1040 | minute.maid | 18841 | 1 | 2.31 | 43522.71 |
| 10/1/1992 12:00:00 AM | 1938 | minute.maid | 14202 | 1 | 2.19 | 31102.38 |
| 10/1/1992 12:00:00 AM | 2405 | minute.maid | 16326 | 1 | 2.05 | 33468.3 |
| 10/1/1992 12:00:00 AM | 1972 | minute.maid | 16380 | 1 | 2.12 | 34725.6 |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated)** .

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_config()
```

```python
from azureml.opendatasets import OjSalesSimulated
```

### <a name="read-data-from-azure-open-datasets"></a>Azure Open Datasets에서 데이터 읽기

```python
# Create a Data Directory in local path
import os

oj_sales_path = "oj_sales_data"

if not os.path.exists(oj_sales_path):
    os.mkdir(oj_sales_path)
```

```python
# Pull all of the data
oj_sales_files = OjSalesSimulated.get_file_dataset()

# or pull a subset of the data
oj_sales_files = OjSalesSimulated.get_file_dataset(num_files=10)
```

```python
oj_sales_files.download(oj_sales_path, overwrite=True)
```

### <a name="upload-the-individual-datasets-to-blob-storage"></a>개별 데이터 세트를 Blob Storage에 업로드
Blob에 데이터를 업로드하고 이 csv 파일 폴더에서 FileDataset를 만듭니다.  

```python
target_path = 'oj_sales_data'

datastore.upload(src_dir = oj_sales_path,
                target_path = target_path,
                overwrite = True, 
                show_progress = True)
```

### <a name="create-the-file-dataset"></a>파일 데이터 세트 만들기 
[FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset)를 만들려면 데이터의 경로를 정의해야 합니다. 


```python
from azureml.core.dataset import Dataset

ds_name = 'oj_data'
path_on_datastore = datastore.path(target_path + '/')

input_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

### <a name="register-the-file-dataset-to-the-workspace"></a>작업 영역에 파일 데이터 세트 등록 
데이터 세트를 예측을 위해 파이프라인에 입력으로 호출할 수 있도록 작업 영역에 데이터 세트를 등록하려고 합니다. 


```python
registered_ds = input_ds.register(ws, ds_name, create_new_version=True)
named_ds = registered_ds.as_named_input(ds_name)
```

<!-- nbend -->

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=sample-oj-sales-simulated)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
# Download or mount OJ Sales raw files Azure Machine Learning file datasets.
# This works only for Linux based compute. See https://docs.microsoft.com/en-us/azure/machine-learning/service/how-to-create-register-datasets to learn more about datasets.

from azureml.opendatasets import OjSalesSimulated

ojss_file = OjSalesSimulated.get_file_dataset()
ojss_file
```

```
ojss_file.to_path()
```

```
# Download files to local storage
import os
import tempfile

mount_point = tempfile.mkdtemp()
ojss_file.download(mount_point, overwrite=True)
```

```
# Mount files. Useful when training job will run on a remote compute.
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed OJ Sales Simulated gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        gz.read(4)
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return pd.DataFrame(res)
```

```
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))
print(os.listdir(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with ojss_file.mount(mount_point):
    print(os.listdir(mount_point))  
    train_images_df = load_data(os.path.join(mount_point, 'train-tabular-oj-ubyte.gz'))
    print(train_images_df.info())
```

<!-- nbend -->

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.