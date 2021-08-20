---
title: 필기 숫자의 MNIST 데이터베이스
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 필기 숫자 데이터 세트의 MNIST 데이터베이스를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1935c8702002fe00fd99de7d6b095d95ecce8475
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039076"
---
# <a name="the-mnist-database-of-handwritten-digits"></a>필기 숫자의 MNIST 데이터베이스

필기 숫자 MNIST 데이터베이스에는 예제 60,000개가 있는 학습 세트와 예제 10,000개가 있는 테스트 세트가 포함됩니다. 이 숫자는 크기를 표준화하였고 고정 크기 이미지로 중앙에 배치됩니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

이 데이터 세트는 [필기 숫자 MNIST 데이터베이스](http://yann.lecun.com/exdb/mnist/)에서 제공됩니다. 이 데이터 세트는 [미국 국립표준기술원](https://www.nist.gov/)에서 게시한 더 큰 [NIST 필기 양식 및 문자 데이터베이스](https://www.nist.gov/srd/nist-special-database-19)의 일부입니다.


## <a name="storage-location"></a>스토리지 위치

- Blob 계정: azureopendatastorage
- 컨테이너 이름: mnist

다음 4개 파일은 컨테이너에서 바로 사용할 수 있습니다.
  - train-images-idx3-ubyte.gz: 학습 세트 이미지(9,912,422바이트)
  - train-labels-idx1-ubyte.gz: 학습 세트 레이블(28,881바이트)
  - t10k-images-idx3-ubyte.gz: 테스트 세트 이미지(1,648,877바이트)
  - t10k-labels-idx1-ubyte.gz: 테스트 세트 레이블(4,542바이트)

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=mnist -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=mnist)** .

### <a name="load-mnist-into-a-data-frame-using-azure-machine-learning-tabular-datasets"></a>Azure Machine Learning 테이블 형식 데이터 세트를 사용하여 MNIST를 데이터 프레임에 로드합니다. 
Azure Machine Learning 데이터 세트에 대한 자세한 내용은 [Azure Machine Learning 데이터 세트 만들기](../machine-learning/how-to-create-register-datasets.md)를 참조하세요. 

#### <a name="get-complete-dataset-into-a-data-frame"></a>데이터 프레임으로 전체 데이터 세트 가져오기

```python
from azureml.opendatasets import MNIST

mnist = MNIST.get_tabular_dataset()
mnist_df = mnist.to_pandas_dataframe()
mnist_df.info()
```

#### <a name="get-train-and-test-data-frames"></a>학습 및 테스트 데이터 프레임 가져오기

```python
mnist_train = MNIST.get_tabular_dataset(dataset_filter='train')
mnist_train_df = mnist_train.to_pandas_dataframe()
X_train = mnist_train_df.drop("label", axis=1).astype(int).values/255.0
y_train = mnist_train_df.filter(items=["label"]).astype(int).values

mnist_test = MNIST.get_tabular_dataset(dataset_filter='test')
mnist_test_df = mnist_test.to_pandas_dataframe()
X_test = mnist_test_df.drop("label", axis=1).astype(int).values/255.0
y_test = mnist_test_df.filter(items=["label"]).astype(int).values
```

#### <a name="plot-some-images-of-the-digits"></a>숫자의 일부 이미지 플롯 

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

### <a name="download-or-mount-mnist-raw-files-azure-machine-learning-file-datasets"></a>MNIST 원시 파일 Azure Machine Learning 파일 데이터 세트를 다운로드하거나 탑재합니다. 
Linux 기반 컴퓨팅에 대해서만 작동합니다. Azure Machine Learning 데이터 세트에 대한 자세한 내용은 [Azure Machine Learning 데이터 세트 만들기](../machine-learning/how-to-create-register-datasets.md)를 참조하세요. 

```python
mnist_file = MNIST.get_file_dataset()
mnist_file
```

```python
mnist_file.to_path()
```

#### <a name="download-files-to-local-storage"></a>로컬 스토리지에 파일 다운로드

```python
import os
import tempfile

data_folder = tempfile.mkdtemp()
data_paths = mnist_file.download(data_folder, overwrite=True)
data_paths
```

#### <a name="mount-files-useful-when-training-job-will-run-on-a-remote-compute"></a>파일을 탑재합니다. 원격 컴퓨팅에서 학습 작업을 실행하는 경우에 유용합니다.

```python
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed MNIST gz files and return pandas dataframe of numpy arrays
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

```python
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with mnist_file.mount(mount_point):
    print("list dir...")
    print(os.listdir(mount_point))
    print("get the dataframe info of mounted data...")
    train_images_df = load_data(next(path for path in data_paths if path.endswith("train-images-idx3-ubyte.gz")))
    print(train_images_df.info())
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=mnist -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=mnist)** .

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
```

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/t10k-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/t10k-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-labels.gz'))
```

```python
import gzip
import struct

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res
```

```python
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(
    data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(
    data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

<!-- nbend -->

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=mnist -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=mnist)** .

### <a name="load-mnist-into-a-data-frame-using-azure-machine-learning-tabular-datasets"></a>Azure Machine Learning 테이블 형식 데이터 세트를 사용하여 MNIST를 데이터 프레임에 로드합니다. 
Azure Machine Learning 데이터 세트에 대한 자세한 내용은 [Azure Machine Learning 데이터 세트 만들기](../machine-learning/how-to-create-register-datasets.md)를 참조하세요. 

#### <a name="get-complete-dataset-into-a-data-frame"></a>데이터 프레임으로 전체 데이터 세트 가져오기

```python
# This is a package in preview.
from azureml.opendatasets import MNIST

mnist = MNIST.get_tabular_dataset()
mnist_df = mnist.to_spark_dataframe()
```

```python
display(mnist_df.limit(5))
```

### <a name="download-or-mount-mnist-raw-files-azure-machine-learning-file-datasets"></a>MNIST 원시 파일 Azure Machine Learning 파일 데이터 세트를 다운로드하거나 탑재합니다. 
Linux 기반 컴퓨팅에 대해서만 작동합니다. Azure Machine Learning 데이터 세트에 대한 자세한 내용은 [Azure Machine Learning 데이터 세트 만들기](../machine-learning/how-to-create-register-datasets.md)를 참조하세요. 

```python
mnist_file = MNIST.get_file_dataset()
mnist_file
```

```python
mnist_file.to_path()
```

#### <a name="download-files-to-local-storage"></a>로컬 스토리지에 파일 다운로드

```python
import os
import tempfile

mount_point = tempfile.mkdtemp()
mnist_file.download(mount_point, overwrite=True)
```

#### <a name="mount-files-useful-when-training-job-will-run-on-a-remote-compute"></a>파일을 탑재합니다. 원격 컴퓨팅에서 학습 작업을 실행하는 경우에 유용합니다.

```python
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed MNIST gz files and return numpy arrays
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

```python
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))
print(os.listdir(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with mnist_file.mount(mount_point):
    print(context.mount_point )
    print(os.listdir(mount_point))  
    train_images_df = load_data(os.path.join(mount_point, 'train-images-idx3-ubyte.gz'))
    print(train_images_df.info())
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.