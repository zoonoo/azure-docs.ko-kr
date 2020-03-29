---
title: 온-프레미스 Hadoop 클러스터에서 Azure 저장소로 데이터 마이그레이션
description: Azure 데이터 팩터리를 사용하여 온-프레미스 Hadoop 클러스터에서 Azure 저장소로 데이터를 마이그레이션하는 방법을 알아봅니다.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: afccbdbbfd5b8ddeefa621448d6170d937b518f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931443"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Azure 데이터 팩터리를 사용하여 온-프레미스 Hadoop 클러스터에서 Azure 저장소로 데이터를 마이그레이션합니다. 

Azure Data Factory는 온-프레미스 HDFS에서 Azure Blob 저장소 또는 Azure Data Lake 저장소 Gen2로 대규모로 데이터를 마이그레이션하기 위한 성능이 뛰어나고 강력하며 비용 효율적인 메커니즘을 제공합니다. 

데이터 팩터리는 온-프레미스 HDFS에서 Azure로 데이터를 마이그레이션하기 위한 두 가지 기본 방법을 제공합니다. 시나리오에 따라 접근 방식을 선택할 수 있습니다. 

- **데이터 팩터리 DistCp** 모드(권장): 데이터 팩터리에서 [DistCp(분산](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) 복사본)를 사용하여 Azure Blob 저장소(준비된 [복사본](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)포함) 또는 Azure Data Lake 저장소 Gen2에 있는 것처럼 파일을 복사할 수 있습니다. DistCp와 통합된 데이터 팩토리를 사용하여 기존 강력한 클러스터를 활용하여 최상의 복사 처리량을 달성합니다. 또한 데이터 팩토리에서 유연한 스케줄링과 통합 모니터링 환경을 활용할 수 있습니다. 데이터 팩터리 구성에 따라 복사 활동은 자동으로 DistCp 명령을 생성하고, 데이터를 Hadoop 클러스터에 제출한 다음 복사 상태를 모니터링합니다. 온-프레미스 Hadoop 클러스터에서 Azure로 데이터를 마이그레이션하기 위해 데이터 팩터리 DistCp 모드를 권장합니다.
- **데이터 팩터리 네이티브 통합 런타임 모드**: DistCp는 모든 시나리오에서 옵션이 아닙니다. 예를 들어 Azure 가상 네트워크 환경에서 DistCp 도구는 Azure 저장소 가상 네트워크 끝점을 사용하여 Azure ExpressRoute 개인 피어링을 지원하지 않습니다. 또한 기존 Hadoop 클러스터를 데이터를 마이그레이션하기 위한 엔진으로 사용하지 않으려는 경우도 있으므로 클러스터에 무거운 부하를 두지 않아 기존 ETL 작업의 성능에 영향을 줄 수 있습니다. 대신 온-프레미스 HDFS에서 Azure로 데이터를 복사하는 엔진으로 Data Factory 통합 런타임의 기본 기능을 사용할 수 있습니다.

이 문서에서는 두 가지 방법에 대한 다음 정보를 제공합니다.
> [!div class="checklist"]
> * 성능 
> * 복사 복원력
> * 네트워크 보안
> * 고급 솔루션 아키텍처 
> * 구현 모범 사례  

## <a name="performance"></a>성능

데이터 팩터리 DistCp 모드에서 처리량은 DistCp 도구를 독립적으로 사용하는 경우와 동일합니다. 데이터 팩터리 DistCp 모드는 기존 Hadoop 클러스터의 용량을 최대화합니다. 대규모 클러스터 간 또는 클러스터 내 복사에 DistCp를 사용할 수 있습니다. 

DistCp는 MapReduce를 사용하여 배포, 오류 처리 및 복구 및 보고에 영향을 미칩니다. 파일 및 디렉터리 목록을 작업 매핑을 위한 입력으로 확장합니다. 각 작업은 원본 목록에 지정된 파일 파티션을 복사합니다. DistCp와 통합된 Data Factory를 사용하여 파이프라인을 구축하여 네트워크 대역폭, 스토리지 IOPS 및 대역폭을 최대한 활용하여 환경에 대한 데이터 이동 처리량을 극대화할 수 있습니다.  

데이터 팩터리 네이티브 통합 런타임 모드는 또한 서로 다른 수준에서 병렬 처리할 수 있도록 합니다. 병렬 처리를 사용하여 네트워크 대역폭, 스토리지 IOPS 및 대역폭을 최대한 활용하여 데이터 이동 처리량을 극대화할 수 있습니다.

- 단일 복사 활동은 확장 가능한 컴퓨팅 리소스를 활용할 수 있습니다. 자체 호스팅 통합 런타임을 사용하면 수동으로 컴퓨터를 확장하거나 여러[컴퓨터(최대 4개 노드)로](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)확장할 수 있습니다. 단일 복사 활동은 모든 노드에서 파일 집합을 분할합니다. 
- 단일 복사 활동은 여러 스레드를 사용하여 데이터 저장소를 읽고 기록합니다. 
- 데이터 팩터리 제어 흐름은 여러 복사 활동을 병렬로 시작할 수 있습니다. 예를 들어 [For Each 루프를](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)사용할 수 있습니다. 

자세한 내용은 복사 [활동 성능 가이드를](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)참조하십시오.

## <a name="resilience"></a>탄력성

데이터 팩터리 DistCp 모드에서는 다양한 DistCp 명령줄 매개 변수(예: `-i`실패 `-update`무시 또는 소스 파일 및 대상 파일의 크기가 다를 때 데이터 쓰기)를 사용하여 복원력 의 수준이 다를 수 있습니다.

데이터 팩터리 네이티브 통합 런타임 모드에서 단일 복사 활동 실행에서 Data Factory에는 기본 제공 재시도 메커니즘이 있습니다. 데이터 저장소 또는 기본 네트워크에서 특정 수준의 일시적인 오류를 처리할 수 있습니다. 

온-프레미스 HDFS에서 Blob 저장소로, 온-프레미스 HDFS에서 데이터 레이크 스토어 Gen2로 이진 복사를 수행할 때 데이터 팩터리는 자동으로 검사점을 대규모로 수행합니다. 복사 활동 실행이 실패하거나 시간이 초과되면 후속 재시도(재시도 수가 1일 > 있는지 확인)에서 복사본은 처음부터 시작하는 대신 마지막 실패 지점에서 다시 시작됩니다.

## <a name="network-security"></a>네트워크 보안 

기본적으로 Data Factory는 HTTPS 프로토콜을 통해 암호화된 연결을 사용하여 온-프레미스 HDFS에서 Blob 저장소 또는 Azure Data Lake Storage Gen2로 데이터를 전송합니다. HTTPS는 전송 중에 데이터 암호화를 제공하고 도청 및 중간자 공격을 방지합니다. 

또는 공용 인터넷을 통해 데이터를 전송하지 않으려면 보안을 강화하여 ExpressRoute를 통해 개인 피어링 링크를 통해 데이터를 전송할 수 있습니다. 

## <a name="solution-architecture"></a>솔루션 아키텍처

이 이미지는 공용 인터넷을 통해 데이터를 마이그레이션하는 것을 묘사합니다.

![공용 네트워크를 통해 데이터를 마이그레이션하기 위한 솔루션 아키텍처를 보여 주는 다이어그램](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- 이 아키텍처에서는 공용 인터넷을 통해 HTTPS를 사용하여 데이터를 안전하게 전송합니다. 
- 공용 네트워크 환경에서 데이터 팩터리 DistCp 모드를 사용하는 것이 좋습니다. 강력한 기존 클러스터를 활용하여 최상의 복사 처리량을 달성할 수 있습니다. 또한 Data Factory에서 유연한 스케줄링 및 통합 모니터링 환경을 활용할 수 있습니다.
- 이 아키텍처의 경우 회사 방화벽 뒤에 있는 Windows 컴퓨터에 Data Factory 자체 호스팅 통합 런타임을 설치하여 DistCp 명령을 Hadoop 클러스터에 제출하고 복사 상태를 모니터링해야 합니다. 기계는 데이터를 이동하는 엔진이 아니기 때문에(제어 목적으로만) 컴퓨터의 용량은 데이터 이동 처리량에 영향을 주지 않습니다.
- DistCp 명령의 기존 매개 변수가 지원됩니다.

이 이미지는 개인 링크를 통해 데이터를 마이그레이션하는 것을 묘사합니다. 

![개인 네트워크를 통해 데이터를 마이그레이션하기 위한 솔루션 아키텍처를 보여 주는 다이어그램](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- 이 아키텍처에서 데이터는 Azure ExpressRoute를 통해 개인 피어링 링크를 통해 마이그레이션됩니다. 데이터는 공용 인터넷을 통해 트래버스하지 않습니다.
- DistCp 도구는 Azure 저장소 가상 네트워크 끝점을 사용하여 ExpressRoute 개인 피어링을 지원하지 않습니다. 통합 런타임을 통해 Data Factory의 기본 기능을 사용하여 데이터를 마이그레이션하는 것이 좋습니다.
- 이 아키텍처의 경우 Azure 가상 네트워크의 Windows VM에 Data Factory 자체 호스팅 통합 런타임을 설치해야 합니다. 수동으로 VM을 확장하거나 여러 VM으로 확장하여 네트워크 및 스토리지 IOPS 또는 대역폭을 완전히 활용할 수 있습니다.
- 각 Azure VM(데이터 팩터리 자체 호스팅 통합 런타임이 설치된 경우)으로 시작하는 권장 구성은 32vCPU 및 128GB 메모리로 Standard_D32s_v3. 데이터 마이그레이션 중에 VM의 CPU 및 메모리 사용량을 모니터링하여 성능을 높이기 위해 VM을 확장해야 하는지 또는 비용을 줄이기 위해 VM을 축소해야 하는지 확인할 수 있습니다.
- 또한 단일 자체 호스팅 통합 런타임으로 최대 4개의 VM 노드를 연결하여 확장할 수도 있습니다. 자체 호스팅 통합 런타임에 대해 실행되는 단일 복사 작업은 파일 집합을 자동으로 분할하고 모든 VM 노드를 사용하여 파일을 병렬로 복사합니다. 고가용성을 위해 데이터 마이그레이션 중에 단일 실패 지점 시나리오를 피하기 위해 두 개의 VM 노드로 시작하는 것이 좋습니다.
- 이 아키텍처를 사용하면 초기 스냅숏 데이터 마이그레이션 및 델타 데이터 마이그레이션을 사용할 수 있습니다.

## <a name="implementation-best-practices"></a>구현 모범 사례

데이터 마이그레이션을 구현할 때 이러한 모범 사례를 따르는 것이 좋습니다.

### <a name="authentication-and-credential-management"></a>인증 및 자격 증명 관리 

- HDFS를 인증하려면 [Windows(Kerberos) 또는 익명](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)을 사용할 수 있습니다. 
- Azure Blob 저장소에 연결하기 위해 여러 인증 유형이 지원됩니다.  [Azure 리소스에 대해 관리되는 ID를](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)사용하는 것이 좋습니다. Azure Active Directory(Azure AD)에서 자동으로 관리되는 데이터 팩터리 ID 위에 구축된 관리 ID를 사용하면 연결된 서비스 정의에 자격 증명을 제공하지 않고 파이프라인을 구성할 수 있습니다. 또는 [서비스 주체,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [공유 액세스 서명](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)또는 저장소 계정 키를 사용하여 Blob [저장소에](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)인증할 수 있습니다. 
- 데이터 레이크 저장소 Gen2에 연결하기 위해 여러 인증 유형도 지원됩니다.  [Azure 리소스에 대해 관리되는 ID를](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)사용하는 것이 좋지만 [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) 또는 [저장소 계정 키를](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)사용할 수도 있습니다. 
- Azure 리소스에 대해 관리되는 ID를 사용하지 않는 경우 데이터 팩터리 연결된 서비스를 수정하지 않고도 키를 중앙에서 쉽게 관리하고 회전할 수 있도록 [Azure Key Vault에 자격 증명을 저장하는](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 것이 좋습니다. 이는 [CI/CD의 모범 사례이기도](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)합니다. 

### <a name="initial-snapshot-data-migration"></a>초기 스냅샷 데이터 마이그레이션 

데이터 팩터리 DistCp 모드에서는 하나의 복사 활동을 만들어 DistCp 명령을 제출하고 다른 매개 변수를 사용하여 초기 데이터 마이그레이션 동작을 제어할 수 있습니다. 

Data Factory 네이티브 통합 런타임 모드에서는 특히 10TB 이상의 데이터를 마이그레이션할 때 데이터 파티션을 권장합니다. 데이터를 분할하려면 HDFS의 폴더 이름을 사용합니다. 그런 다음 각 데이터 팩터리 복사 작업은 한 번에 하나의 폴더 파티션을 복사할 수 있습니다. 더 나은 처리량을 위해 여러 데이터 팩터리 복사 작업을 동시에 실행할 수 있습니다.

네트워크 또는 데이터 저장소 일시적인 문제로 인해 복사 작업이 실패하면 실패한 복사 작업을 다시 실행하여 HDFS에서 해당 특정 파티션을 다시 로드할 수 있습니다. 다른 파티션을 로드하는 다른 복사 작업은 영향을 받지 않습니다.

### <a name="delta-data-migration"></a>델타 데이터 마이그레이션 

데이터 팩터리 DistCp 모드에서는 델타 데이터 마이그레이션을 위해 `-update`DistCp 명령줄 매개 변수를 사용하여 소스 파일 및 대상 파일의 크기가 다를 때 데이터를 작성할 수 있습니다.

Data Factory 네이티브 통합 모드에서 HDFS에서 새 파일이나 변경된 파일을 식별하는 가장 성능이 뛰어난 방법은 시간 분할된 명명 규칙을 사용하는 것입니다. HDFS의 데이터가 파일 또는 폴더 이름(예: */yyyy/mm/dd/file.csv)의*시간 분할 정보로 시간 분할된 경우 파이프라인은 증분 방식으로 복사할 파일 및 폴더를 쉽게 식별할 수 있습니다.

또는 HDFS의 데이터가 시간 분할되지 않은 경우 Data Factory는 **LastModifiedDate** 값을 사용하여 새 파일이나 변경된 파일을 식별할 수 있습니다. Data Factory는 HDFS의 모든 파일을 검사하고 설정된 값보다 큰 마지막으로 수정된 타임스탬프가 있는 새 파일과 업데이트된 파일만 복사합니다. 

HDFS에 많은 파일이 있는 경우 필터 조건과 일치하는 파일 수에 관계없이 초기 파일 검색에 시간이 오래 걸릴 수 있습니다. 이 시나리오에서는 초기 스냅숏 마이그레이션에 사용 했던 동일한 파티션을 사용 하 여 먼저 데이터를 분할 하는 것이 좋습니다. 그런 다음 파일 검색이 병렬로 발생할 수 있습니다.

### <a name="estimate-price"></a>예상 가격 

HDFS에서 Azure Blob 저장소로 데이터를 마이그레이션하기 위한 다음 파이프라인을 고려하십시오. 

![가격 파이프라인을 보여 주는 다이어그램](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

다음 정보를 가정해 보겠습니다. 

- 총 데이터 볼륨은 1 PB입니다.
- 데이터 팩터리 네이티브 통합 런타임 모드를 사용하여 데이터를 마이그레이션합니다.
- 1PB는 1,000개의 파티션으로 나뉘며 각 복사본은 하나의 파티션을 이동합니다.
- 각 복사 활동은 4대의 컴퓨터와 연관되어 500-MBps 처리량을 달성하는 하나의 자체 호스팅 통합 런타임으로 구성됩니다.
- ForEach 동시성은 **4로** 설정되고 집계 처리량은 2GBps입니다.
- 마이그레이션을 완료하는 데 는 총 146시간이 걸립니다.

여기에 우리의 가정에 따라 예상 가격입니다: 

![가격 계산을 표시하는 테이블](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> 이는 가상의 가격 책정 예제입니다. 실제 가격은 사용자 환경의 실제 처리량에 따라 다릅니다.
> Azure Windows VM(자체 호스팅 통합 런타임이 설치된 경우)의 가격은 포함되지 않습니다.

### <a name="additional-references"></a>추가 참조

- [HDFS 커넥터](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob 저장소 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [복사 활동 성능 튜닝 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [자체 호스팅 통합 런타임 만들기 및 구성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [자체 호스팅 통합 런타임 고가용성 및 확장성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [데이터 이동 보안 고려 사항](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure 키 자격 증명에 자격 증명 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [시간 분할된 파일 이름에 따라 증분 방식으로 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [LastModifiedDate를 기반으로 새 파일 및 변경된 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [데이터 팩토리 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>다음 단계

- [Azure 데이터 팩터리를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)