---
title: HDInsight의 Azure Data Lake Storage Gen2 개요
description: HDInsight의 Data Lake Storage Gen2에 대 한 개요입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: a7ae1a0f77d0d19fc48695ddc5cc3d3a14d65ab9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82195131"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>HDInsight의 Azure Data Lake Storage Gen2 개요

Azure Data Lake Storage Gen2는 Azure Data Lake Storage Gen1의 핵심 기능을 가져와 Azure Blob 스토리지에 통합합니다. 이러한 기능에는 Hadoop, Azure AD(Azure Active Directory) 및 POSIX 기반 ACL(액세스 제어 목록)과 호환되는 파일 시스템이 포함됩니다. 이러한 조합을 통해 Azure Data Lake Storage Gen1의 성능을 활용할 수 있습니다. 또한 Blob storage의 계층화 및 데이터 수명 주기 관리를 사용 합니다.

Azure Data Lake Storage Gen2에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)를 참조하세요.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2의 핵심 기능

* **Hadoop과 호환 되는 액세스:** Azure Data Lake Storage Gen2에서는 Hadoop 분산 파일 시스템 (HDFS)를 사용 하는 것 처럼 데이터를 관리 하 고 액세스할 수 있습니다. ABFS(Azure Blob Filesystem) 드라이버는 Azure HDInsight 및 Azure Databricks를 포함한 모든 Apache Hadoop 환경 내에서 사용할 수 있습니다. ABFS를 사용하여 Data Lake Storage Gen2에 저장된 데이터에 액세스합니다.

* **POSIX 권한 상위 집합:** Data Lake Gen2의 보안 모델은 Data Lake Storage Gen2와 관련 된 몇 가지 추가 세분성과 함께 ACL 및 POSIX 권한을 지원 합니다. 설정은 관리 도구 또는 Apache Hive 및 Apache Spark와 같은 프레임워크를 통해 구성할 수 있습니다.

* **비용 효율성:** Data Lake Storage Gen2는 저렴 한 저장소 용량 및 트랜잭션을 제공 합니다. Azure Blob storage 수명 주기를 통해 데이터를 수명 주기 동안 이동할 때 청구 비율을 조정 하 여 비용을 절감할 수 있습니다.

* **Blob storage 도구, 프레임 워크 및 앱과의 호환성:** Data Lake Storage Gen2는 Blob 저장소에 대 한 다양 한 도구, 프레임 워크 및 응용 프로그램을 계속 해 서 사용할 수 있습니다.

* **최적화 된 드라이버:** ABFS 드라이버는 빅 데이터 분석을 위해 특별히 최적화 되어 있습니다. 해당 REST API는 DFS(분산 파일 시스템) 엔드포인트(dfs.core.windows.net)를 통해 표시됩니다.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2의 새로운 기능

### <a name="managed-identities-for-secure-file-access"></a>보안 파일 액세스용 관리 ID

Azure HDInsight는 관리 ID를 사용하여 Azure Data Lake Storage Gen2의 파일에 대한 클러스터 액세스를 보호합니다. 관리 ID는 자동으로 관리되는 일단의 자격 증명을 Azure 서비스에 제공하는 Azure Active Directory의 기능입니다. 이러한 자격 증명은 Active Directory 인증을 지원하는 모든 서비스에 인증하는 데 사용할 수 있습니다. 관리 ID를 사용하면 코드 또는 구성 파일에 자격 증명을 저장할 필요가 없습니다.

자세한 내용은 [Azure 리소스에 대한 ID 관리](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

### <a name="azure-blob-file-system-driver"></a>Azure Blob 파일 시스템 드라이버

Apache Hadoop 애플리케이션은 기본적으로 로컬 디스크 스토리지에서 데이터를 읽고 쓰려고 합니다. Hadoop 응용 프로그램은 ABFS와 같은 Hadoop 파일 시스템 드라이버를 사용 하 여 클라우드 저장소를 사용할 수 있습니다. 정기적으로 Hadoop 파일 시스템 작업을 에뮬레이트하는 방식으로 작동 합니다. 드라이버는 애플리케이션에서 받은 명령을 실제 클라우드 스토리지 플랫폼이 이해할 수 있는 작업으로 변환합니다.

이전에 Hadoop 파일 시스템 드라이버는 모든 파일 시스템 작업을 클라이언트 쪽에서 호출 REST API Azure Storage으로 변환 했습니다. 그런 다음 REST API를 호출 합니다. 그러나 이러한 클라이언트 쪽 변환으로 인해 파일 이름 바꾸기와 같은 단일 파일 시스템 작업에 대해 여러 번의 REST API가 호출되었습니다. ABFS가 클라이언트 쪽에서 서버 쪽으로 Hadoop 파일 시스템 논리를 이동 했습니다. Azure Data Lake Storage Gen2 API는 이제 Blob API와 동시에 실행됩니다. 일반적인 Hadoop 파일 시스템 작업이 한 번의 REST API 호출로 실행될 수 있으므로 이 마이그레이션은 성능을 향상시킵니다.

자세한 내용은 [Azure Blob 파일 시스템 드라이버 (ABFS): Hadoop 용 전용 Azure Storage 드라이버](../storage/blobs/data-lake-storage-abfs-driver.md)를 참조 하세요.

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2의 URI 체계

Azure Data Lake Storage Gen2는 새 URI 체계를 사용하여 HDInsight에서 Azure Storage의 파일에 액세스합니다.

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI 체계는 SSL로 암호화 된 액세스를 제공 합니다.

`<FILE_SYSTEM_NAME>`은 Data Lake Storage Gen2 파일 시스템의 경로를 식별합니다.

`<ACCOUNT_NAME>`은 Azure Storage 계정 이름을 식별합니다. FQDN(정규화된 도메인 이름)이 필요합니다.

`<PATH>`는 파일 또는 디렉터리 HDFS 경로 이름입니다.

`<FILE_SYSTEM_NAME>` 및 `<ACCOUNT_NAME>`에 대한 값을 지정하지 않으면 기본 파일 시스템이 사용됩니다. 기본 파일 시스템의 파일에 대해서는 상대 경로나 절대 경로를 사용합니다. 예를 들어 HDInsight 클러스터와 함께 제공되는 `hadoop-mapreduce-examples.jar` 파일은 다음 경로 중 하나를 사용하여 참조할 수 있습니다.

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> HDInsight 버전 2.1 및 1.6 클러스터에서 파일 이름은 `hadoop-examples.jar`입니다. HDInsight 외부에서 파일을 사용할 때 대부분의 유틸리티는 ABFS 형식을 인식하지 않으며 대신 `example/jars/hadoop-mapreduce-examples.jar`과 같은 기본 경로 형식을 예상합니다.

자세한 내용은 [Azure Data Lake Storage Gen2 URI 사용](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Storage 소개](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1 개요](./overview-data-lake-storage-gen1.md)