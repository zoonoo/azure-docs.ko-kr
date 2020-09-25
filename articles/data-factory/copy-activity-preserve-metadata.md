---
title: Azure Data Factory의 복사 작업을 사용 하 여 메타 데이터 및 Acl 유지
description: Azure Data Factory에서 복사 작업을 사용 하 여 복사 하는 동안 메타 데이터 및 Acl을 유지 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: a96b04df56dc7d5ea26463073d673275b8a4a8c4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324300"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Azure Data Factory의 복사 작업을 사용 하 여 메타 데이터 및 Acl 유지

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 복사 작업을 사용 하 여 원본에서 싱크로 데이터를 복사 하는 경우 다음과 같은 시나리오에서 메타 데이터와 Acl을 함께 유지할 수도 있습니다.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> Lake migration에 대 한 메타 데이터 유지

[Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)및 [Azure File Storage](connector-azure-file-storage.md)를 포함 한 data lake에서 다른 data lake로 데이터를 마이그레이션하는 경우 데이터와 함께 파일 메타 데이터를 유지 하도록 선택할 수 있습니다.

복사 작업은 데이터 복사 중 다음 특성 유지를 지원 합니다.

- **모든 고객이 지정한 메타 데이터** 
- 및 다음 **5 개의 데이터 저장소 기본 제공 시스템 속성**: `contentType` , `contentLanguage` (Amazon S3의 경우 제외), `contentEncoding` `contentDisposition` `cacheControl` ,,

**메타 데이터의 차이점을 처리 합니다.** Amazon S3 및 Azure Storage는 고객이 지정한 메타 데이터의 키에 다른 문자 집합을 허용 합니다. 복사 작업을 사용 하 여 메타 데이터를 유지 하도록 선택 하면 ADF에서 자동으로 잘못 된 문자를 ' _ '로 바꿉니다.

Amazon S3/Azure Data Lake Storage Gen2/azure blob/azure File Storage에서 Azure Data Lake Storage Gen2/azure blob/azure File Storage에 이진 형식으로 파일을 있는 그대로 복사 하는 경우 활동 제작을 위한 **Preserve** **복사 작업**  >  **설정** 탭 또는 데이터 복사 도구의 **설정** 페이지에서 보존 옵션을 찾을 수 있습니다.

![복사 작업 메타 데이터 유지](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

다음은 복사 작업 JSON 구성의 예입니다 (참조 `preserve` ). 

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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> Data Lake Storage Gen1/Gen2에서 Gen2로 Acl 유지

Azure Data Lake Storage Gen1에서 Gen2로 업그레이드 하거나 ADLS Gen2 간에 데이터를 복사 하는 경우 데이터 파일과 함께 POSIX Acl (액세스 제어 목록)을 유지 하도록 선택할 수 있습니다. 액세스 제어에 대 한 자세한 내용은 [Azure Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md) 및 [Azure Data Lake Storage Gen2에서 access control](../storage/blobs/data-lake-storage-access-control.md)을 참조 하세요.

복사 작업은 데이터를 복사 하는 동안 다음 유형의 Acl 유지를 지원 합니다. 하나 이상의 형식을 선택할 수 있습니다.

- **ACL**: 파일 및 디렉터리에 대 한 POSIX 액세스 제어 목록을 복사 하 고 유지 합니다. 원본에서 싱크로 전체 기존 Acl을 복사 합니다. 
- **소유자**: 파일 및 디렉터리를 소유 하는 사용자를 복사 하 고 유지 합니다. 싱크 Data Lake Storage Gen2에 대 한 슈퍼 사용자 액세스가 필요 합니다.
- **그룹**: 소유 하는 파일 및 디렉터리 그룹을 복사 하 고 유지 합니다. 싱크 Data Lake Storage Gen2 또는 소유 사용자 (소유 하는 사용자도 대상 그룹의 멤버인 경우)에 대 한 슈퍼 사용자 액세스 권한이 필요 합니다.

폴더에서 복사 하도록 지정 하는 경우 `recursive` 이 true로 설정 된 경우 지정 된 폴더와 해당 폴더에 있는 파일 및 디렉터리에 대 한 acl을 Data Factory 복제 합니다. 단일 파일에서 복사 하도록 지정 하면 해당 파일에 대 한 Acl이 복사 됩니다.

>[!NOTE]
>ADF를 사용 하 여 Data Lake Storage Gen1/Gen2에서 Gen2로 Acl을 유지 하는 경우 싱크에 있는 기존 Acl Gen2's 해당 폴더/파일을 덮어씁니다.

>[!IMPORTANT]
>Acl을 유지 하도록 선택 하는 경우 싱크 Data Lake Storage Gen2 계정에 대해 작동 하는 Data Factory에 대 한 충분 한 권한을 부여 해야 합니다. 예를 들어 계정 키 인증을 사용 하거나 저장소 Blob 데이터 소유자 역할을 서비스 주체 또는 관리 id에 할당 합니다.

이진 형식 또는 이진 복사 옵션을 사용 하 여 소스를 Data Lake Storage Gen1/Gen2로 구성 하거나 이진 형식 또는 이진 복사 옵션을 사용 하 여 Data Lake Storage Gen2으로 싱크를 구성 하는 경우, 작업 작성을 위한 **복사 작업**설정 탭 또는 데이터 복사 도구의 **설정** 페이지에서 **보존** 옵션을 찾을 수 있습니다  >  **Settings** .

![Gen2/Gen2 to Preserve ACL Data Lake Storage Gen1](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

다음은 복사 작업 JSON 구성의 예입니다 (참조 `preserve` ). 

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

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능](copy-activity-performance.md)
