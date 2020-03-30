---
title: Azure 데이터 레이크 스토리지 Gen2를 지원하는 Azure 서비스 | 마이크로 소프트 문서
description: Azure 서비스 및 Azure 데이터 레이크 저장소 Gen2와 통합되는 Azure 서비스에 대해 알아보기
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 0cf8a4a78894cb7f0e1ddcf7aa18bbb4006bbeeb
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345643"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure 데이터 레이크 저장소 Gen2를 지원하는 Azure 서비스

Azure 서비스를 사용하여 데이터를 수집하고 분석을 수행하고 시각적 표현을 만들 수 있습니다. 이 문서에서는 지원되는 Azure 서비스 목록을 제공하고, 지원 수준을 공개하며, Azure Data Lake Storage Gen2에서 이러한 서비스를 사용하는 데 도움이 되는 문서에 대한 링크를 제공합니다.

## <a name="supported-azure-services"></a>지원되는 Azure 서비스

이 표에는 Azure Data Lake 저장소 Gen2에서 사용할 수 있는 Azure 서비스가 나열되어 있습니다. 이러한 테이블에 나타나는 항목은 지원이 계속 확장됨에 따라 시간이 지남에 따라 변경됩니다.

> [!NOTE]
> 지원 수준은 데이터 레이크 저장소 세대 2에서 서비스가 지원되는 방법을 나타냅니다.

|Azure 서비스 |지원 수준 |관련 문서 |
|---------------|-------------------|---|
|Azure 데이터 팩터리|일반 공급|[Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 로드](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|일반 공급|[Azure Databricks와 함께 사용](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [빠른 시작: Azure 데이터 브릭을 사용하여 Azure 데이터 레이크 저장소 Gen2의 데이터 분석](data-lake-storage-quickstart-create-databricks-account.md) <br>[자습서: Azure Databricks를 사용하여 데이터를 추출, 변환 및 로드합니다.](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[자습서: 스파크를 사용 하 여 Azure 데이터 브릭을 사용 하 여 액세스 데이터 호수 스토리지 Gen2 데이터](data-lake-storage-use-databricks-spark.md)|
|Azure 이벤트 허브 캡처| 일반 공급|[Azure Blob Storage 또는 Azure Data Lake Storage에서 Azure Event Hubs를 통해 이벤트 캡처](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps|일반 공급|[개요 - Azure Logic Apps란?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|일반 공급|[Azure 저장소 서비스의 데이터 액세스](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|일반 공급|[빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Azure 데이터 레이크 Gen2로의 송신](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| 일반 공급|[Azure 데이터 상자를 사용하여 온-프레미스 HDFS 저장소에서 Azure 저장소로 데이터 마이그레이션](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | 일반 공급|[Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Data Lake Storage Gen2에서 HDFS CLI 사용](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[튜토리얼: Azure HDInsight에서 아파치 하이브를 사용하여 데이터를 추출, 변환 및 로드](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub | 일반 공급|[IoT Hub 메시지 라우팅을 사용하여 다양한 엔드포인트에 디바이스-클라우드 메시지 보내기](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| 일반 공급|[Power BI를 사용하여 데이터 레이크 스토리지 Gen2의 데이터 분석](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|일반 공급|[Azure SQL Data Warehouse와 함께 사용](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SSIS(SQL Server Integration Services)|일반 공급|[Azure 저장소 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|미리 보기|[Azure 데이터 레이크 저장소 Gen2 문서 의 색인 및 검색(미리 보기)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|미리 보기|[Azure 데이터 탐색기를 사용하여 Azure 데이터 호수의 쿼리 데이터](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|아직 지원되지 않음|[Azure 데이터 레이크 저장소 Gen2 문서 의 색인 및 검색(미리 보기)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>참조

- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)
- [Azure 데이터 레이크 스토리지 Gen2에서 사용할 수 있는 Blob 저장소 기능](data-lake-storage-supported-blob-storage-features.md)
- [Azure 데이터 레이크 스토리지 Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage에 대한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md)