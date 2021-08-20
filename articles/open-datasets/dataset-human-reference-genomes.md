---
title: Human Reference Genomes
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 Human Reference Genomes 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1e1b1fa785d4ab91329216983c5b694cc484860a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297727"
---
# <a name="human-reference-genomes"></a>Human Reference Genomes

이 데이터 세트에는 [Genome Reference Consortium](https://www.ncbi.nlm.nih.gov/grc)(게놈 참조 컨소시엄)에서 조합한 두 가지 인간 게놈 참조인 Hg19 및 Hg38이 포함되어 있습니다.

Hg19(GRCh37) 데이터에 대한 자세한 내용은 [NCBI의 GRCh37 보고서](https://www.ncbi.nlm.nih.gov/assembly/GCF_000001405.13/)를 참조하세요.

Hg38 데이터에 대한 자세한 내용은 [NCBI의 GRCh38 보고서](http://www.ncbi.nlm.nih.gov/assembly/GCF_000001405.26/)를 참조하세요.

이 데이터에 대한 기타 세부 정보는 [NCBI RefSeq](https://www.ncbi.nlm.nih.gov/refseq/) 사이트에서 확인할 수 있습니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>데이터 원본

이 데이터 세트의 출처는 다음의 두 FTP 위치입니다.

ftp://ftp.ncbi.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/all_assembly_versions/GCF_000001405.25_GRCh37.p13/

ftp://ftp.ncbi.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/

Blob 이름에 URI의 “vertebrate_mammalian” 세그먼트로 시작하는 접두사가 붙어 있습니다.

## <a name="data-volumes-and-update-frequency"></a>데이터 볼륨 및 업데이트 빈도

이 데이터 세트는 약 10GB의 데이터를 포함하며 매일 업데이트됩니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 서부 2 및 미국 중서부 Azure 지역에 저장됩니다. 선호도를 위해 미국 서부 2 또는 미국 중서부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="data-access"></a>데이터 액세스

미국 서부 2: 'https://datasetreferencegenomes.blob.core.windows.net/dataset '

미국 중서부: 'https://datasetreferencegenomes-secondary.blob.core.windows.net/dataset '

[SAS 토큰](../storage/common/storage-sas-overview.md): sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=JtQoPFqiC24GiEB7v9zHLi4RrA2Kd1r%2F3iFt2l9%2FlV8%3D

## <a name="use-terms"></a>사용 약관

데이터는 제한 없이 사용할 수 있습니다. 자세한 내용 및 인용 세부 정보는 [NCBI Reference Sequence Database site](https://www.ncbi.nlm.nih.gov/refseq/)(NCBI 참조 시퀀스 데이터베이스 사이트)를 참조하세요.

## <a name="contact"></a>연락처

이 데이터 세트에 대한 질문이나 피드백은 [Genome Reference Consortium](https://www.ncbi.nlm.nih.gov/grc/contact-us)(게놈 참조 컨소시엄)에 문의하세요.

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-reference-genomes -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-reference-genomes)** .

## <a name="getting-the-reference-genomes-from-azure-open-datasets"></a>Azure Open Datasets에서 참조 게놈 가져오기

여러 가지 퍼블릭 유전체학 데이터가 Azure Open Datasets로 [여기](https://azure.microsoft.com/services/open-datasets/catalog/)에 업로드되었습니다. 이 개방형 데이터 세트에 연결된 Blob 서비스를 만듭니다. 아래에서 `Reference Genomes` 데이터 세트에 대한 Azure Open Datasets의 데이터 호출 프로시저 예제를 찾을 수 있습니다.

사용자는 이 Notebook을 통해 'https://datasetreferencegenomes.blob.core.windows.net/dataset/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure/genomic_regions_definitions.txt ' 경로를 호출하여 다운로드할 수 있습니다.

**중요 참고:** 사용자가 Azure ML SDK를 사용하여 데이터를 보려면 Azure CLI를 통해 Azure 계정에 로그인해야 합니다. 반면, 데이터를 다운로드하기 위해 어떤 작업도 수행할 필요가 없습니다.

[Azure CLI를 설치합니다](/cli/azure/install-azure-cli).

### <a name="calling-the-data-from--reference-genome-datasets"></a>‘참조 게놈 데이터 세트’에서 데이터 호출

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
reference_dataset = Dataset.File.from_files('https://datasetreferencegenomes.blob.core.windows.net/dataset')
mount = reference_dataset.mount()
```

```python
import os

REF_DIR = '/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure'
path = mount.mount_point + REF_DIR

with mount:
    print(os.listdir(path))
```

```python
import pandas as pd

# create mount context
mount.start()

# specify path to genomic_regions_definitions.txt file
REF_DIR = 'vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, REF_DIR, 'genomic_regions_definitions.txt')

# read genomic_regions_definitions.txt file
metadata = pd.read_table(metadata_filename)
metadata
```

### <a name="download-the-specific-file"></a>특정 파일 다운로드

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetreferencegenomes',sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=JtQoPFqiC24GiEB7v9zHLi4RrA2Kd1r%2F3iFt2l9%2FlV8%3D')     
blob_service_client.get_blob_to_path('dataset/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure', 'genomic_regions_definitions.txt', './genomic_regions_definitions.txt')
```

<!-- nbend -->

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.