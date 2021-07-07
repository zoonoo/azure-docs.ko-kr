---
title: ClinVar Annotations
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 ClinVar Annotations 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 384eb565882cf5f62cec1c6d38769647a82ec712
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039115"
---
# <a name="clinvar-annotations"></a>ClinVar Annotations

[ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/)는 인간 변이와 표현형 간의 관계가 지원 증거와 함께 포함된 보고서의 자유롭게 액세스할 수 있는 공개 아카이브입니다. ClinVar는 인간 변이와 관찰된 건강 상태 간에 주장된 관계 및 해당 해석의 기록에 쉽게 액세스할 수 있게 해주며 관련 커뮤니케이션을 쉽게 할 수 있게 해줍니다. 또한 유전체학 워크플로 및 애플리케이션에 통합할 수 있는 다양한 일련의 임상 해석에 액세스할 수 있게 해줍니다.

데이터에 대한 자세한 내용은 [데이터 사전](https://www.ncbi.nlm.nih.gov/projects/clinvar/ClinVarDataDictionary.pdf) 및 [FAQ](https://www.ncbi.nlm.nih.gov/clinvar/docs/faq/)를 참조하세요.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>데이터 원본

이 데이터 세트는 ftp://ftp.ncbi.nlm.nih.gov/pub/clinvar/xml/의 미러입니다.

## <a name="data-volumes-and-update-frequency"></a>데이터 볼륨 및 업데이트 빈도

이 데이터 세트는 약 56GB의 데이터를 포함하며 매일 업데이트됩니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 서부 2 및 미국 중서부 Azure 지역에 저장됩니다. 선호도를 위해 미국 서부 2 또는 미국 중서부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="data-access"></a>데이터 액세스

미국 서부 2: https://datasetclinvar.blob.core.windows.net/dataset

미국 중서부: https://datasetclinvar-secondary.blob.core.windows.net/dataset

[SAS 토큰](../storage/common/storage-sas-overview.md): sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=qFPPwPba1RmBvaffkzkLuzabYU5dZstSTgMwxuLNME8%3D

## <a name="use-terms"></a>사용 약관
데이터는 제한 없이 사용할 수 있습니다. 자세한 내용 및 인용 세부 정보는 [Accessing and using data in ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/docs/maintenance_use/)(ClinVar에서 데이터 액세스 및 사용)를 참조하세요.

## <a name="contact"></a>연락처

이 데이터 세트에 대한 질문이나 피드백은 clinvar@ncbi.nlm.nih.gov에 문의하세요.

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-clinvar -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-clinvar)** .

## <a name="getting-the-clinvar-data-from-azure-open-dataset"></a>Azure Open Datasets에서 ClinVar 데이터 가져오기

여러 가지 퍼블릭 유전체학 데이터가 Azure Open Datasets로 [여기](https://azure.microsoft.com/services/open-datasets/catalog/)에 업로드되었습니다. 이 개방형 데이터 세트에 연결된 Blob 서비스를 만듭니다. 아래에서 `ClinVar` 데이터 세트에 대한 Azure Open Datasets의 데이터 호출 프로시저 예제를 찾을 수 있습니다.

사용자는 이 Notebook을 통해 다음 경로를 호출하고 다운로드할 수 있습니다. https://datasetclinvar.blob.core.windows.net/dataset/ClinVarFullRelease_00-latest.xml.gz.md5

> [!NOTE]
> 사용자가 Azure ML SDK를 사용하여 데이터를 보려면 Azure CLI를 통해 Azure 계정에 로그인해야 합니다. 반면, 데이터를 다운로드하기 위해 어떤 작업도 수행할 필요가 없습니다.

Azure CLI 설치에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="calling-the-data-from--clinvar-data-set"></a>‘ClinVar 데이터 세트’에서 데이터 호출

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
reference_dataset = Dataset.File.from_files('https://datasetclinvar.blob.core.windows.net/dataset')
mount = reference_dataset.mount()
```

```python
import os

REF_DIR = '/dataset'
path = mount.mount_point + REF_DIR

with mount:
    print(os.listdir(path))
```

```python
import pandas as pd

# create mount context
mount.start()

# specify path to README file
REF_DIR = '/dataset'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, REF_DIR, '_README')

# read README file
metadata = pd.read_table(metadata_filename)
metadata
```

### <a name="download-the-specific-file"></a>특정 파일 다운로드

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetclinvar', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=qFPPwPba1RmBvaffkzkLuzabYU5dZstSTgMwxuLNME8%3D')     
blob_service_client.get_blob_to_path('dataset', 'ClinVarFullRelease_00-latest.xml.gz.md5', './ClinVarFullRelease_00-latest.xml.gz.md5')
```

<!-- nbend -->

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.