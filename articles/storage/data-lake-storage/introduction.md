---
title: Azure Data Lake Storage Gen2 미리 보기 소개
description: Azure Data Lake Storage Gen2 미리 보기에 대한 개요 제공
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 072573b16fbeebac1ec942b0be508cf901b5cd27
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42140711"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Azure Data Lake Storage Gen2 미리 보기 소개

Azure Data Lake Storage Gen2 미리 보기는 [Azure Blob Storage](../blobs/storage-blobs-introduction.md) 위에 빌드된 빅 데이터 분석 전용 기능 집합입니다. 이를 사용하면 파일 시스템 및 개체 저장소 패러다임을 모두 사용하여 데이터를 조작할 수 있습니다. 이로써 Data Lake Storage Gen2는 모든 데이터에서 분석 값을 추출할 수 있는 유일한 클라우드 기반 다중 모달 저장소 서비스입니다.

Data Lake Storage Gen2는 분석 데이터의 전체 수명 주기에 필요한 모든 특성을 갖추고 있습니다. 이것은 두 가지 기존 저장소 서비스의 기능을 수렴한 결과입니다. 파일 시스템 의미 체계, 파일 수준 보안 및 확장과 같은 [Azure Data Lake Storage Gen1](../../data-lake-store/index.md)의 기능은 [Azure Blob Storage](../blobs/storage-blobs-introduction.md)의 낮은 비용, 계층화된 저장소, 높은 가용성/재해 복구 성능 및 대규모 SDK/도구 에코시스템과 결합됩니다. Data Lake Storage Gen2에서 분석 워크로드에 맞게 최적화된 파일 시스템 인터페이스의 이점이 추가되는 동안 개체 저장소의 모든 특성이 유지됩니다.

## <a name="designed-for-enterprise-big-data-analytics"></a>엔터프라이즈 빅 데이터 분석에 맞게 설계됨

Data Lake Storage Gen2는 Azure에서 EDL(Enterprise Data Lakes)을 빌드하기 위한 기본 저장소 서비스입니다. 처음부터 수백 기가비트의 처리량을 지속하면서 많은 페타바이트의 정보를 제공하도록 설계된 Data Lake Storage Gen2는 방대한 양의 데이터를 쉽게 관리하는 방법을 제공합니다.

Data Lake Storage Gen2의 기본 기능은 데이터 액세스 성능을 개선하기 위해 개체/파일을 디렉터리 계층 구조로 구성하는 [계층 구조 네임스페이스](./namespace.md)를 Blob Storage 서비스에 추가한 것입니다. 또한 계층 구조 네임스페이스를 사용하면 Data Lake Storage Gen2를 사용하여 동시에 개체 저장소 및 파일 시스템 패러다임을 둘 다 지원할 수 있습니다. 예를 들어, 일반적인 개체 저장소 이름 지정 규칙은 이름에 슬래시를 사용하여 계층 구조 폴더 구조를 모방합니다. 이 구조는 Data Lake Storage Gen2에서 구현됩니다. 디렉터리 이름 바꾸기 또는 삭제와 같은 작업은 디렉터리의 이름 접두사를 공유하는 모든 개체를 열거 및 처리하는 대신 디렉터리에 대한 단일 원자성 메타데이터 작업이 됩니다.

과거 클라우드 기반 분석은 성능, 관리 및 보안 영역을 양보해야 했습니다. Data Lake Storage Gen2는 이러한 각 측면을 다음과 같은 방법으로 해결합니다.

- 분석의 필수 구성 요소로 데이터를 복사하거나 변환할 필요가 없으므로 **성능**이 최적화됩니다. 계층 구조 네임스페이스는 전반적인 작업 성능을 개선하는 디렉터리 관리 작업의 성능을 크게 개선합니다.

- 디렉터리 및 하위 디렉터리를 통해 파일을 구성하고 조작할 수 있으므로 **관리**가 더 쉽습니다.

- 폴더 또는 개별 파일에 POSIX 권한을 정의할 수 있으므로 **보안**을 강제로 적용할 수 있습니다.

- Data Lake Storage Gen2가 저비용 [Azure Blob Storage](../blobs/storage-blobs-introduction.md) 위에 빌드되므로 **비용 효과**를 얻을 수 있습니다. 추가 기능을 통해 Azure에서 빅 데이터 분석을 실행하기 위한 총 소유 비용이 더 낮아집니다.

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2의 주요 기능

> [!NOTE]
> Data Lake Storage Gen2의 공개 미리 보기 기간에 아래 나열된 일부 기능의 가용성은 달라질 수 있습니다. 미리 보기 프로그램 중에 새 기능과 지역이 릴리스되면 이 정보가 전달됩니다.
> Data Lake Storage Gen2의 공개 미리 보기에 [등록](https://aka.ms/adlsgen2signup)하세요.  

- **Hadoop 호환 액세스**: Data Lake Storage Gen2를 사용하면 [HDFS(Hadoop 분산 파일 시스템)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)를 사용하는 것처럼 데이터를 관리하고 액세스할 수 있습니다. 새 [ABFS 드라이버](./abfs-driver.md)는 [Azure HDInsight](../../hdinsight/index.yml) 및 [Azure Databricks](../../azure-databricks/index.yml)를 포함한 모든 Apache Hadoop 환경 내에서 Data Lake Storage Gen2에 저장된 데이터에 액세스하는 데 사용할 수 있습니다.

- **POSIX 권한 상위 집합**: Data Lake Gen2의 보안 모델은 Data Lake Storage Gen2와 관련된 몇 가지 추가 세분성과 함께 ACL 및 POSIX 권한을 완벽하게 지원합니다. 설정은 관리 도구 또는 Hive 및 Spark 같은 프레임워크를 통해 구성할 수 있습니다.

- **비용 효과**: Data Lake Storage Gen2는 낮은 비용의 저장소 용량 및 트랜잭션을 제공합니다. 데이터가 전체 수명 주기를 통해 전환됨에 따라 청구 요금이 바뀌므로 [Azure Blob Storage 수명 주기](../common/storage-lifecycle-managment-concepts.md)와 같은 기본 제공 기능을 통해 비용이 최소한으로 유지됩니다.

- **Blob Storage 도구, 프레임워크 및 앱 사용**: Data Lake Storage Gen2는 현재 Blob Storage용으로 제공되는 다양한 도구, 프레임워크 및 응용 프로그램을 계속 사용합니다.

- **최적화된 드라이버**: `abfs` 드라이버는 빅 데이터 분석을 위해 [특별히 최적화](./abfs-driver.md)되었습니다. 해당 REST API는 `dfs` 엔드포인트, `dfs.core.windows.net`를 통해 표시됩니다.

## <a name="scalability"></a>확장성

Azure Storage는 Data Lake Storage Gen2 또는 Blob Storage 인터페이스를 통해 액세스하는지 여부에 관계없이 계획적으로 확장 가능합니다. ‘많은 엑사바이트의 데이터’를 저장하고 제공할 수 있습니다. 이 저장소 양은 높은 IOPS(초당 입출력 작업 수) 수준에서 Gbps(초당 기가비트 수)로 측정되는 처리량에 사용할 수 있습니다. 지속성 외에도 서비스, 계정 및 파일 수준에서 측정되는 거의 지속적인 요청별 대기 시간으로 처리가 실행됩니다.

## <a name="cost-effectiveness"></a>비용 효과

Data Lake Storage Gen2를 Azure Blob Storage 위에 빌드하는 다양한 이점 중 하나는 저장소 용량 및 트랜잭션의 [낮은 비용](https://azure.microsoft.com/pricing/details/storage)입니다. 다른 클라우드 저장소 서비스와 달리 Data Lake Storage Gen2는 분석을 수행하기 전에 데이터를 이동하거나 변환하지 않아도 되므로 비용이 절감됩니다.

또한 [계층 구조 네임스페이스](./namespace.md)와 같은 기능은 많은 분석 작업의 전반적인 성능을 크게 개선합니다. 이 성능 개선은 동일한 양의 데이터를 처리하는 데 필요한 계산 성능이 감소하므로 종단 간 분석 작업에 대한 TCO(총 소유 비용)가 낮아짐을 의미합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Data Lake Storage Gen2의 기본 개념을 설명하고 데이터를 저장, 액세스, 관리하고 데이터에서 인사이트를 얻는 방법을 자세히 살펴봅니다.

* [계층 구조 네임스페이스](./namespace.md)
* [저장소 계정을 만드는](./quickstart-create-account.md)
* [Azure Data Lake Storage Gen2로 HDInsight 클러스터 만들기](./quickstart-create-connect-hdi-cluster.md)
* [Azure Databricks에서 Azure Data Lake Storage Gen2 계정 사용](./quickstart-create-databricks-account.md) 