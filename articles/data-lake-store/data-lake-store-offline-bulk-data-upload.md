---
title: 오프라인 방법을 사용하여 Azure Data Lake Storage Gen1에 대량 데이터 업로드 | Microsoft Docs
description: AdlCopy 도구를 사용하여 Azure Storage Blob에서 Data Lake Storage Gen1로 데이터 복사
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 43b482324f0244baf52edbb8989a56dd12833331
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104469"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-azure-data-lake-storage-gen1"></a>Azure Import/Export 서비스를 사용하여 Azure Data Lake Storage Gen1에 오프라인 데이터 복사
이 문서에서는 [Azure Import/Export 서비스](../storage/common/storage-import-export-service.md)와 같은 오프라인 복사 방법을 사용하여 대량 데이터 집합(200GB 초과)을 Azure Data Lake Storage Gen1에 복사하는 방법을 알아봅니다. 특히 이 문서에서 예제로 사용하는 파일의 크기는 디스크에서 339,420,860,416바이트(약 319GB)입니다. 이 파일을 319GB.tsv라고 하겠습니다.

Azure Import/Export 서비스를 사용하면 하드 디스크 드라이브를 Azure 데이터 센터에 제공하여 대량 데이터를 Azure Blob Storage로 더 안전하게 전송할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Storage 계정**
* **Azure Data Lake Storage Gen1 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.

## <a name="preparing-the-data"></a>데이터 준비

Import/Export 서비스를 사용하려면 먼저 전송할 데이터 파일을 **200GB 미만의 복사본**으로 분할해야 합니다. 200GB보다 큰 파일인 경우 가져오기 도구를 작동할 수 없기 때문입니다. 이 자습서에서는 파일을 각각 100GB의 청크로 분할했습니다. [Cygwin](https://cygwin.com/install.html)을 사용하면 이렇게 수행할 수 있습니다. Cygwin은 Linux 명령을 지원합니다. 이 경우 다음 명령을 사용합니다.

    split -b 100m 319GB.tsv

분할 작업에서는 아래와 같은 이름의 파일을 만듭니다.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>데이터와 함께 디스크 준비
**드라이브 준비** 섹션의 [Azure Import/Export 서비스 사용](../storage/common/storage-import-export-service.md) 지침에 따라 하드 드라이브를 준비합니다. 전체 시퀀스는 다음과 같습니다.

1. Auzre Import/Export 서비스에 사용할 요구 사항을 충족하는 하드 디스크를 확보합니다.
2. 하드 디스크가 Azure 데이터 센터에 제공된 후에 데이터를 복사할 Azure Storage 계정을 확인합니다.
3. [Azure Import/Export 도구](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) 명령줄 유틸리티를 사용합니다. 이 도구를 사용하는 방법을 보여 주는 샘플 코드 조각은 다음과 같습니다.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    자세한 코드 조각에 대해서는 [Azure Import/Export 서비스 사용](../storage/common/storage-import-export-service.md)을 참조하세요.
4. 위 명령은 지정된 위치에 저널 파일을 만듭니다. 이 저널 파일을 사용하여 [Azure Portal](https://portal.azure.com)에서 가져오기 작업을 만듭니다.

## <a name="create-an-import-job"></a>가져오기 작업 만들기
이제 **가져오기 작업 만들기** 섹션의 [Azure Import/Export 서비스 사용](../storage/common/storage-import-export-service.md)의 지침에 따라 가져오기 작업을 만들 수 있습니다. 이 가져오기 작업에 대해 다른 세부 정보를 사용하여 디스크 드라이브를 준비하는 동안 생성된 저널 파일을 제공합니다.

## <a name="physically-ship-the-disks"></a>물리적 디스크 배송
이제 디스크를 Azure 데이터 센터에 물리적으로 제공할 수 있습니다. 여기서 데이터는 가져 오기 작업을 만드는 동안 사용자가 제공한 Azure Storage Blob으로 복사됩니다. 또한 작업을 만드는 동안 나중에 추적 정보를 제공하도록 선택한 경우 가져오기 작업으로 돌아가서 추적 번호를 업데이트할 수 있습니다.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Azure Storage Blob에서 Azure Data Lake Storage Gen1로 데이터 복사
가져오기 작업의 상태가 완료되었다고 표시되면 지정한 Azure Storage Blob에서 데이터를 사용할 수 있는지 확인할 수 있습니다. 그런 다음, 다양한 방법으로 Blob에서 Azure Data Lake Storage Gen1로 해당 데이터를 이동할 수 있습니다. 데이터를 업로드하는 데 사용 가능한 모든 옵션은 [Data Lake Storage Gen1에 데이터 수집](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1)을 참조하세요.

이 섹션에서는 데이터 복사용 Azure Data Factory 파이프라인을 만드는 데 사용할 수 있는 JSON 정의를 제공합니다. 이러한 JSON 정의는 [Azure Portal](../data-factory/tutorial-copy-data-portal.md) 또는 [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md)에서 사용할 수 있습니다.

### <a name="source-linked-service-azure-storage-blob"></a>원본에 연결된 서비스(Azure Storage Blob)
```
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-azure-data-lake-storage-gen1"></a>대상에 연결된 서비스(Azure Data Lake Storage Gen1)
```
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```
### <a name="input-data-set"></a>입력 데이터 집합
```
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
### <a name="output-data-set"></a>출력 데이터 집합
```
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```
### <a name="pipeline-copy-activity"></a>파이프라인(복사 작업)
```
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```
자세한 내용은 [Azure Data Factory를 사용하여 Azure Storage Blob에서 Azure Data Lake Storage Gen1로 데이터 이동](../data-factory/connector-azure-data-lake-store.md)을 참조하세요.

## <a name="reconstruct-the-data-files-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1에서 데이터 파일 다시 생성
319GB의 파일로 시작하고 작은 크기의 파일로 분할하여 Azure Import/Export 서비스를 사용하여 파일을 전송할 수 있었습니다. 이제 데이터가 Azure Data Lake Storage Gen1에 있으므로 파일을 원래 크기로 다시 생성할 수 있습니다. 다음 Azure PowerShell cmdlet을 사용하여 이 작업을 수행할 수 있습니다.

```
# Login to our account
Connect-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext -SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>다음 단계
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1에서 Azure Data Lake Analytics 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1에서 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
