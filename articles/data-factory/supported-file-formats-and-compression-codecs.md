---
title: Azure 데이터 팩터리에서 지원되는 파일 형식
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419037"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Azure Data Factory에서 지원되는 파일 형식 및 압축 코덱
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*이 문서는 다음 커넥터에 적용 됩니다: [아마존 S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure 데이터 호수 저장소 Gen1,](connector-azure-data-lake-store.md)Azure 데이터 호수 저장소 [Gen2,](connector-azure-data-lake-storage.md) [Azure 파일 저장소,](connector-azure-file-storage.md) [파일 시스템,](connector-file-system.md) [FTP,](connector-ftp.md) [Google 클라우드 스토리지,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)및 [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

[복사 활동을](copy-activity-overview.md) 사용하여 두 파일 기반 데이터 저장소 사이의 있는 것처럼 파일을 복사할 수 있으며, 이 경우 직렬화 또는 직렬화 없이 데이터를 효율적으로 복사할 수 있습니다. 

또한 지정된 형식의 파일을 구문 분석하거나 생성할 수도 있습니다. 예를 들어 다음을 수행할 수 있습니다.

* 온-프레미스 SQL Server 데이터베이스에서 데이터를 복사하고 Parquet 형식으로 Azure Data Lake Storage Gen2에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트(CSV) 형식으로 파일을 복사하고 Avro 형식으로 Azure Blob 저장소에 씁니다.
* 온-프레미스 파일 시스템에서 압축이 있는 파일을 복사하고, 즉시 압축을 풀고, 추출된 파일을 Azure Data Lake Storage Gen2에 씁니다.
* Azure Blob 저장소에서 Gzip 압축 텍스트(CSV) 형식의 데이터를 복사하여 Azure SQL 데이터베이스에 씁니다.
* 직렬화/역직렬화 또는 압축/압축 해제가 필요한 더 많은 활동.

## <a name="next-steps"></a>다음 단계

다른 복사 작업 문서를 참조하세요.

- [활동 개요 복사](copy-activity-overview.md)
- [복사 작업 성능](copy-activity-performance.md)
