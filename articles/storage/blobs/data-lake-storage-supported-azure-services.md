---
title: Azure Data Lake Storage Gen2를 지 원하는 Azure 서비스 | Microsoft Docs
description: Azure Data Lake Storage Gen2와 통합 하는 Azure 서비스에 대해 알아보기
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 71df31562971af8bcdb38be76894c1d0abeccc3f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925580"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2를 지 원하는 Azure 서비스

Azure 서비스를 사용 하 여 데이터를 수집 하 고 분석을 수행 하 고 시각적 표현을 만들 수 있습니다. 이 문서는 지원 되는 Azure 서비스의 목록을 제공 하 고, 지원 공개을 제공 하며, Azure Data Lake Storage Gen2에서 이러한 서비스를 사용 하는 데 도움이 되는 문서에 대 한 링크를 제공 합니다.

## <a name="supportedazureservices"></a>지원 되는 Azure 서비스

이 표에는 Azure Data Lake Storage Gen2에서 사용할 수 있는 Azure 서비스가 나열 되어 있습니다. 이러한 테이블에 표시 되는 항목은 지원이 계속 확장 될 때 시간이 지남에 따라 변경 됩니다.

> [!NOTE]
> 지원 수준은 서비스가 Gen 2 Data Lake Storage 지원 되는 방법만을 나타냅니다.

|Azure 서비스 |지원 수준 |관련 문서 |
|---------------|-------------------|---|
|Azure 데이터 팩터리|일반 공급|[Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 로드](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|일반 공급|[Azure Databricks와 함께 사용](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [빠른 시작: Azure Databricks를 사용 하 여 Azure Data Lake Storage Gen2에서 데이터 분석](data-lake-storage-quickstart-create-databricks-account.md) <br>[자습서: Azure Databricks을 사용 하 여 데이터 추출, 변환 및 로드](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[자습서: Spark를 사용 하 여 Azure Databricks으로 데이터 Data Lake Storage Gen2 액세스](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hubs 캡처| 일반 공급|[Azure Blob Storage 또는 Azure Data Lake Storage에서 Azure Event Hubs를 통해 이벤트를 캡처합니다.](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps|일반 공급|[개요-Azure Logic Apps 이란?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|일반 공급|[Azure storage 서비스의 데이터에 액세스](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|일반 공급|[빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Azure Data Lake Gen2로 송신](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| 일반 공급|[Azure Data Box를 사용 하 여 온-프레미스 HDFS 저장소에서 Azure Storage 데이터를 마이그레이션합니다.](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | 일반 공급|[Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Data Lake Storage Gen2에서 HDFS CLI 사용](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[자습서: Azure HDInsight에서 Apache Hive를 사용 하 여 데이터 추출, 변환 및 로드](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub | 일반 공급|[IoT Hub 메시지 라우팅을 사용 하 여 다른 끝점으로 장치-클라우드 메시지 보내기](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| 일반 공급|[Power BI를 사용 하 여 Data Lake Storage Gen2 데이터 분석](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|일반 공급|[Azure SQL Data Warehouse와 함께 사용](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SSIS(SQL Server Integration Services)|일반 공급|[Azure Storage 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search| 미리 보기|[문서 Azure Data Lake Storage Gen2 인덱스 및 검색 (미리 보기)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Content Delivery Network|아직 지원 되지 않음|[문서 Azure Data Lake Storage Gen2 인덱스 및 검색 (미리 보기)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2의 알려진 문제](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob storage 기능](data-lake-storage-supported-blob-storage-features.md)
- [Azure Data Lake Storage Gen2를 지 원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage에 대한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md)