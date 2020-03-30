---
title: Azure 데이터 팩터리에서 복사 작업을 사용하여 메타데이터 및 ACL 보존
description: Azure Data Factory에서 복사 작업을 사용하여 복사하는 동안 메타데이터 및 ACL을 보존하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: b73cd73a18d286f221c7be2c624719e1d23d7c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153831"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Azure 데이터 팩터리에서 복사 작업을 사용하여 메타데이터 및 ACL 보존

Azure Data Factory 복사 활동을 사용하여 원본에서 싱크로 데이터를 복사하는 경우 다음 시나리오에서 메타데이터 및 ACL을 보존할 수도 있습니다.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>호수 마이그레이션을 위한 메타데이터 보존

[Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob](connector-azure-blob-storage.md)및 Azure Data Lake Storage [Gen2를](connector-azure-data-lake-storage.md)포함하여 한 데이터 레이크에서 다른 데이터 레이크로 데이터를 마이그레이션할 때 데이터와 함께 파일 메타데이터를 보존하도록 선택할 수 있습니다.

복사 활동은 데이터 복사 중에 다음 특성을 보존하는 데 지원됩니다.

- **모든 고객이 지정한 메타데이터** 
- 그리고 다음 **다섯 데이터 저장소 내장 시스템 속성** `contentType`: ( `contentLanguage` `contentEncoding`아마존 `contentDisposition` `cacheControl`S3 제외), , .

Amazon S3/Azure Data Lake Storage Gen2/Azure Blob에서 이진 **형식의** Azure Data Lake Storage Gen2/Azure Blob에서 있는 파일을 이진 형식으로 복사하면 활동 작성을 위한 활동**설정 탭또는** 복사 데이터 도구의 **설정** 페이지에서 **보존** > 옵션을 찾을 수 있습니다.

![메타데이터 보존 활동 복사](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

다음은 복사 활동 JSON 구성의 예입니다(참조) `preserve` 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>데이터 레이크 스토리지 Gen1/Gen2에서 Gen2로 ACL 보존

Azure Data Lake Storage Gen1에서 Gen2로 업그레이드하거나 ADLS Gen2 간에 데이터를 복사하는 경우 데이터 파일과 함께 POSIX 액세스 제어 목록(ACL)을 보존하도록 선택할 수 있습니다. 액세스 제어에 대한 자세한 내용은 [Azure Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md) 및 Azure 데이터 레이크 저장소 [Gen2의 액세스 제어를](../storage/blobs/data-lake-storage-access-control.md)참조하십시오.

복사 활동은 데이터 복사 중에 다음과 같은 유형의 ACL을 보존하는 것을 지원합니다. 하나 이상의 유형을 선택할 수 있습니다.

- **ACL**: 파일 및 디렉터리에서 POSIX 액세스 제어 목록을 복사하고 보존합니다. 원본에서 싱크까지 기존 ACL 전체를 복사합니다. 
- **소유자**: 파일 및 디렉터리 소유 사용자를 복사하고 보존합니다. 싱크 데이터 레이크 스토리지 Gen2에 대한 수퍼 사용자 액세스가 필요합니다.
- **그룹**: 소유 파일 및 디렉터리 그룹을 복사하고 보존합니다. 데이터 레이크 저장소 Gen2 또는 소유 사용자(소유 사용자가 대상 그룹의 구성원인 경우)를 싱크하는 수퍼 사용자 액세스가 필요합니다.

폴더에서 복사하도록 지정하면 Data Factory는 해당 폴더에 대한 ACL과 해당 폴더의 `recursive` 파일 및 디렉토리를 true로 설정한 경우 복제합니다. 단일 파일에서 복사하도록 지정하면 해당 파일의 ACL이 복사됩니다.

>[!NOTE]
>ADF를 사용하여 데이터 레이크 저장소 Gen1/Gen2에서 Gen2로 ACL을 보존하면 싱크 Gen2의 해당 폴더/파일에 있는 기존 ACL이 덮어씁니다.

>[!IMPORTANT]
>ACL을 보존하도록 선택한 경우 데이터 팩터리에서 싱크 데이터 레이크 저장소 Gen2 계정에 대해 작동할 수 있는 충분한 권한을 부여해야 합니다. 예를 들어 계정 키 인증을 사용하거나 저장소 Blob 데이터 소유자 역할을 서비스 주체 또는 관리되는 ID에 할당합니다.

소스를 이진 형식 또는 이진 복사 옵션으로 Data Lake Storage Gen1/Gen2로 구성하고 이진 형식 또는 이진 복사 옵션을 사용하여 Data Lake Storage Gen2로 싱크하면 데이터 복사 도구의 **설정** 페이지 또는 활동 작성을 위한 **복사 활동** > **설정** 탭에서 **보존** 옵션을 찾을 수 있습니다.

![데이터 레이크 스토리지 Gen1/Gen2에서 Gen2 보존 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

다음은 복사 활동 JSON 구성의 예입니다(참조) `preserve` 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>다음 단계

다른 복사 작업 문서를 참조하세요.

- [활동 개요 복사](copy-activity-overview.md)
- [복사 작업 성능](copy-activity-performance.md)
