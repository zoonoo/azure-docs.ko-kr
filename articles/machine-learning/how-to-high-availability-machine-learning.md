---
title: 복원 력을 늘리는 방법
titleSuffix: Azure Machine Learning
description: 고가용성 구성을 사용 하 여 Azure Machine Learning 관련 리소스를 가동 중지 시간 보다 더 탄력적으로 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098907"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Azure Machine Learning의 복원 력 향상

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

고가용성 구성을 사용 하 여 Azure Machine Learning 관련 리소스의 복원 력을 높이는 방법에 대해 알아봅니다. Azure Machine Learning 따라 달라 지는 Azure 서비스는 고가용성을 위해 구성할 수 있습니다. 이 문서에서는 고가용성을 위해 구성할 수 있는 서비스에 대 한 정보를 제공 하 고 이러한 리소스 구성 정보에 대 한 링크를 제공 합니다.

> [!NOTE]
> Azure Machine Learning 자체는 재해 복구 옵션을 제공 하지 않습니다.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 Azure 서비스 이해

Azure Machine Learning는 여러 Azure 서비스에 종속 되며 여러 계층이 있습니다. 일부는 (고객) 구독에 프로 비전 됩니다. 이러한 서비스의 고가용성 구성을 담당 합니다. 일부는 Microsoft 구독에서 만들어지고 Microsoft에서 관리 합니다.

* **Azure Machine Learning 인프라**: Azure Machine Learning 작업 영역을 위한 Microsoft 관리 환경입니다.

* **연결 된 리소스**: 작업 영역을 만드는 Azure Machine Learning 동안 구독에 프로 비전 된 리소스입니다. 여기에는 ACR (Azure Storage Azure Key Vault, Azure Container Registry) 및 App Insights가 포함 됩니다. 이러한 리소스에 대 한 고가용성 설정을 담당 합니다.
  * 기본 저장소에는 모델, 학습 로그 데이터 및 데이터 집합과 같은 데이터가 있습니다.
  * 저장소, ACR, 데이터 저장소에 대 한 자격 증명이 Key Vault에 있습니다.
  * ACR에는 학습 및 추론 환경에 대 한 docker 이미지가 있습니다.
  * App Insights는 모니터링 Azure Machine Learning입니다.

* **계산 리소스**: 작업 영역 배포 후에 만든 리소스입니다. 예를 들어 기계 학습 모델을 학습 하기 위해 계산 인스턴스 또는 계산 클러스터를 만들 수 있습니다.
  * 계산 인스턴스 및 계산 클러스터: Microsoft에서 관리 하는 모델 개발 환경.
  * 기타 리소스: 컴퓨팅 리소스는 AKS (Azure Kubernetes Service), ACI (Azure Databricks, Azure Container Instances) 및 HDInsight와 같은 Azure Machine Learning에 연결할 수 있습니다. 고가용성 설정에 대해 책임을 집니다.

* **추가 데이터 저장소**: 학습 데이터에 대 한 Azure Storage, Azure Data Lake Storage, Azure SQL Database 등의 추가 데이터 저장소를 탑재할 수 Azure Machine Learning.  이러한 사용자는 구독 내에 있으며 고가용성 설정을 담당 합니다.

다음 표에서는 Microsoft에서 관리 하는 서비스 및 기본적으로 항상 사용 가능한 서비스를 보여 줍니다.

| 서비스 | 관리자 | 기본적으로 HA |
| ----- | ----- | ----- |
| **Azure Machine Learning 인프라** | Microsoft | |
| **연결 된 리소스** |
| Azure Storage | 사용자 | |
| Azure Key Vault | 사용자 | ✓ |
| Azure Container Registry | 사용자 | |
| Application Insights | 사용자 | 해당 없음 |
| **계산 리소스** |
| 계산 인스턴스 | Microsoft |  |
| 계산 클러스터 | Microsoft |  |
| Azure Kubernetes Service와 같은 기타 리소스 <br>Azure Databricks, Azure Container Instance, Azure HDInsight | 사용자 |  |
| Azure Storage, Azure SQL Database 등의 **추가 데이터 저장소**<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>Azure Databricks 파일 시스템 | 사용자 | |

이 문서의 나머지 부분에 있는 정보를 사용 하 여 이러한 각 서비스의 가용성을 높이기 위해 수행 해야 하는 작업에 대해 알아보세요.

## <a name="associated-resources"></a>연결 된 리소스

> [!IMPORTANT]
> Azure Machine Learning는 GRS (지역 중복 저장소) 또는 GZRS (지역 중복 저장소) 또는 읽기 액세스 지역 중복 저장소 (RA-GRS) 또는 읽기 액세스 지역 중복 저장소 (RA-GZRS)를 사용 하는 기본 저장소 계정 장애 조치 (failover)를 지원 하지 않습니다.

아래 정보를 사용 하 여 각 리소스의 고가용성 설정이 올바른지 확인 합니다.

* **Azure Storage**: 고가용성 설정을 구성 하려면 [Azure Storage 중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy)(영문)을 참조 하세요.
* **Azure Key Vault**: 기본 고가용성 서비스를 제공 하며 사용자 작업은 필요 하지 않습니다.  [Azure Key Vault 가용성 및 중복성을](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)참조 하세요.
* **Azure Container Registry**: 지역에서 복제를 위해 프리미엄 SKU를 선택 합니다. [Azure Container Registry에서 지역에서 복제를](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)참조 하세요.
* **Application Insights**: 고가용성 설정을 제공 하지 않습니다. [Application Insights의 데이터 수집, 보존 및 저장소](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept)에서 데이터 보존 기간 및 세부 정보를 조정할 수 있습니다.

## <a name="compute-resources"></a>계산 리소스

아래 설명서를 사용 하 여 각 리소스의 고가용성 설정이 올바른지 확인 합니다.

* **Azure Kubernetes service**: [AKS (azure Kubernetes service)에서 비즈니스 연속성 및 재해 복구에 대 한 모범 사례](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) 를 참조 하 고 [가용성 영역을 사용 하는 AKS (azure Kubernetes service) 클러스터를 만듭니다](https://docs.microsoft.com/azure/aks/availability-zones). AKS 클러스터가 Azure Machine Learning (스튜디오, SDK 또는 ML CLI 사용)에 의해 만들어진 경우 지역 간 고가용성은 지원 되지 않습니다.
* **Azure Databricks**: [Azure Databricks 클러스터에 대 한 지역별 재해 복구를](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)참조 하세요.
* **Azure Container Instance**: ACI orchestrator는 장애 조치 (failover)를 담당 합니다. [Azure Container Instances 및 컨테이너 orchestrator를](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship)참조 하세요.
* **Azure hdinsight**: [azure hdinsight에서 지원 되는 고가용성 서비스](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components)를 참조 하세요.

## <a name="additional-data-stores"></a>추가 데이터 저장소

아래 설명서를 사용 하 여 각 리소스의 고가용성 설정이 올바른지 확인 합니다.

* **Azure Blob Container/Azure 파일 공유/Azure Data Lake Gen2**: 기본 저장소와 동일 합니다.
* **Azure Data Lake Gen1**:[Azure Data Lake Storage Gen1의 데이터에 대 한 재해 복구 지침](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)을 참조 하세요.
* **Azure SQL Database**: [고가용성 및 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability)를 참조 하세요.
* **Azure Database for PostgreSQL**: [Azure Database for PostgreSQL 단일 서버에서 고가용성 개념을](https://docs.microsoft.com/azure/postgresql/concepts-high-availability)참조 하세요.
* **Azure Database for MySQL**: [Azure Database for MySQL의 비즈니스 연속성 이해를](https://docs.microsoft.com/azure/mysql/concepts-business-continuity)참조 하세요.
* **Databricks 파일 시스템**: [Azure Databricks 클러스터에 대 한 지역 재해 복구를](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)참조 하세요.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

사용자 고유의 키 (고객이 관리 하는 키)를 제공 하 여 Azure Machine Learning 작업 영역을 배포 하는 경우 Cosmos DB도 구독 내에서 프로 비전 됩니다. 이 경우에는 사용자가 고가용성을 담당 합니다. [Azure Cosmos DB에서 고가용성을](https://docs.microsoft.com/azure/cosmos-db/high-availability) 참조 하세요.

## <a name="next-steps"></a>다음 단계

연결 된 리소스가 있는 Azure Machine Learning를 고가용성 설정과 함께 배포 하려면 [Azure Resource Manager 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)사용 합니다.