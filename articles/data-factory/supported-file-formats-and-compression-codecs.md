---
title: Azure Data Factory에서 지원 되는 파일 형식
description: 이 항목에서는 Azure Data Factory에서 파일 기반 커넥터가 지원하는 파일 형식 및 압축 코드를 설명합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: dbcfad3dd3db9f5c9431e07d85d77a77a10283c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419037"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Azure Data Factory에서 지원되는 파일 형식 및 압축 코덱
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*이 문서는 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md)커넥터에 적용 됩니다.*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

[복사 작업](copy-activity-overview.md) 을 사용 하 여 두 파일 기반 데이터 저장소 간에 파일을 있는 그대로 복사할 수 있습니다 .이 경우 데이터는 직렬화 나 deserialization 없이 효율적으로 복사 됩니다. 

또한 지정 된 형식의 파일을 구문 분석 하거나 생성할 수도 있습니다. 예를 들어 다음을 수행할 수 있습니다.

* 온-프레미스 SQL Server 데이터베이스에서 데이터를 복사 하 고 Parquet 형식으로 Azure Data Lake Storage Gen2에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트 (CSV) 형식의 파일을 복사 하 여 Avro 형식으로 Azure Blob 저장소에 씁니다.
* 온-프레미스 파일 시스템에서 압축 된 파일을 복사 하 여 즉시 압축을 풀고 압축을 푼 파일을 Azure Data Lake Storage Gen2에 기록 합니다.
* Azure Blob storage에서 Gzip CSV (압축 텍스트) 형식으로 데이터를 복사 하 여 Azure SQL Database에 기록 합니다.
* Serialization/deserialization, 압축/압축 해제가 필요한 많은 작업이 있습니다.

## <a name="next-steps"></a>다음 단계

다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능](copy-activity-performance.md)
