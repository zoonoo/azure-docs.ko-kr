---
title: Azure Data Factory를 사용 하 여 온-프레미스 Hadoop 클러스터에서 Azure Storage로 데이터 마이그레이션 Microsoft Docs
description: Azure Data Factory를 사용 하 여 온-프레미스 Hadoop 클러스터에서 Azure Storage로 데이터를 마이그레이션합니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211604"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Azure Data Factory를 사용 하 여 온-프레미스 Hadoop 클러스터에서 Azure Storage로 데이터를 마이그레이션합니다. 

Azure Data Factory은 온-프레미스 HDFS에서 Azure Blob Storage 또는 Azure Data Lake Storage Gen2으로 대규모로 데이터를 마이그레이션하는 데 성능과 강력 하 고 비용 효율적인 메커니즘을 제공 합니다. 기본적으로 Azure Data Factory에는 온-프레미스 HDFS에서 Azure로 데이터를 마이그레이션하는 두 가지 방법이 포함 되어 있습니다. 시나리오에 따라 각 항목을 선택할 수 있습니다. 

- ADF DistCp 모드입니다. ADF는 [Distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) 를 사용 하 여 Azure Blob ( [준비 된 복사](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)포함) 또는 Azure Data Lake Store로 파일을 복사 하는 것을 지원 합니다 .이 경우 adf 자체 호스팅 IR (통합 런타임)에서 실행 하는 대신 클러스터의 기능을 완벽 하 게 활용할 수 있습니다. 특히 클러스터가 매우 강력한 경우 복사 처리량을 향상 시킬 수 있습니다. Azure Data Factory의 구성에 따라 복사 작업은 자동으로 DistCp 명령을 생성 하 고, Hadoop 클러스터에 제출 하 고, 복사 상태를 모니터링 합니다. DistCp와 통합 된 ADF를 사용 하 여 고객은 기존 강력한 클러스터를 활용 하 여 최상의 복사 처리량을 얻을 수 있을 뿐만 아니라 ADF에서 유연한 예약 및 통합 모니터링 환경을 활용할 수도 있습니다. 기본적으로, 온-프레미스 hadoop 클러스터에서 Azure로 데이터를 마이그레이션하는 데는 ADF DistCp 모드가 권장 되는 방법입니다.
- ADF native IR 모드입니다. 일부 시나리오에서는 DistCp가 사용자의 사례에 적합 하지 않습니다. 예를 들어 VNET 환경에서 DistCp 도구는 Express 경로 개인 피어 링 + Azure Storage VNet 끝점)을 지원 하지 않습니다. 뿐만 아니라 기존 hadoop 클러스터를 엔진으로 사용 하 여 클러스터에서 많은 부하를 가져오므로 기존 ETL 작업의 성능에 영향을 줄 수 있으므로 데이터를 마이그레이션하는 것이 좋습니다. 이 경우 adf 기본 기능을 사용할 수 있습니다. 여기서, ADF integration runtime (IR)은 온-프레미스 HDFS에서 Azure로 데이터를 복사 하는 엔진 일 수 있습니다.

이 문서에서는 데이터 엔지니어와 개발자를 위한 두 가지 방법에 대해 다음 정보를 제공 합니다.
> [!div class="checklist"]
> * 성능 
> * 복원 력
> * 네트워크 보안
> * 개략적인 솔루션 아키텍처 
> * 구현 모범 사례  

## <a name="performance"></a>성능

ADF DistCp 모드에서 처리량은 DistCp 도구를 독립적으로 사용 하는 것과 동일 하며, 기존 hadoop 클러스터의 용량을 활용 합니다. DistCp (분산 복사)는 클러스터 간 대량 복사에 사용 되는 도구입니다. MapReduce를 사용 하 여 배포, 오류 처리, 복구 및 보고에 영향을 주지 않습니다. 파일 및 디렉터리 목록을 입력으로 확장 하 여 작업을 매핑하고, 각 작업은 원본 목록에 지정 된 파일의 파티션을 복사 합니다. DistCp와 통합 된 ADF를 사용 하 여 네트워크 대역폭과 저장소 IOPS 및 대역폭을 완벽 하 게 활용 하 여 환경에 대 한 데이터 이동 처리량을 최대화 하는 파이프라인을 빌드할 수 있습니다.  

ADF native IR 모드에서는 서로 다른 수준에서 병렬 처리를 수행할 수 있습니다 .이를 통해 저장소 IOPS 및 대역폭을 완벽 하 게 활용 하 여 자체 호스팅 IR 컴퓨터를 수동으로 확장 하거나 확장 하 여 데이터 이동 처리량을 최대화할 수 있습니다. 자체 호스팅 IR 다중 컴퓨터.

- 단일 복사 작업은 확장 가능한 계산 리소스를 활용할 수 있습니다. 자체 호스팅 integration runtime을 사용 하 여 수동으로 컴퓨터를 확장 하거나 여러 컴퓨터로 확장할 수 있습니다 ([최대 4 개 노드](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)). 단일 복사 작업은 모든 노드에 걸쳐 해당 파일 집합을 분할 합니다. 
- 단일 복사 작업은 여러 스레드를 사용 하 여 데이터 저장소에서 읽고 씁니다. 
- ADF 제어 흐름은 [For each 루프](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)를 사용 하는 등의 여러 복사 작업을 병렬로 시작할 수 있습니다. 

[복사 작업 성능 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) 에서 자세한 내용을 볼 수 있습니다.

## <a name="resilience"></a>복원력

ADF DistCp 모드에서는 다른 DistCp 명령줄 매개 변수를 활용할 수 있습니다. 예를 들어-i, 오류 무시,-업데이트, 소스 파일 및 대상 파일 크기가 다른 경우 데이터 쓰기 등의 다양 한 복원 력 수준을 달성할 수 있습니다.

ADF native IR 모드에서 단일 복사 작업 실행 내의 ADF에는 기본 제공 재시도 메커니즘이 있으므로 데이터 저장소나 기본 네트워크에서 특정 수준의 일시적인 오류를 처리할 수 있습니다. 온-프레미스 HDFS에서 Blob으로, 온-프레미스 HDFS에서 ADLS Gen2로 이진 복사를 수행 하는 경우 ADF는 자동으로 검사점을 확장 합니다. 복사 작업 실행이 실패 하거나 시간이 초과 되 면 이후 다시 시도 (다시 시도 횟수 > 1)에서 처음부터 시작 하는 대신 마지막 오류 지점에서 복사본이 다시 시작 됩니다.

## <a name="network-security"></a>네트워크 보안 

기본적으로 ADF는 HTTPS 프로토콜을 통한 암호화 된 연결을 사용 하 여 온-프레미스 HDFS에서 Azure Blob Storage 또는 Azure Data Lake Storage Gen2로 데이터를 전송 합니다.  HTTPS는 전송 중인 데이터 암호화를 제공 하며 도청 및 메시지 가로채기 (man-in-the-middle) 공격을 방지 합니다. 

또는 공용 인터넷을 통해 데이터를 전송 하지 않으려면 Azure Express 경로를 통해 개인 피어 링 링크를 통해 데이터를 전송 하 여 더 높은 수준의 보안을 달성할 수 있습니다. 이를 달성할 수 있는 방법은 아래의 솔루션 아키텍처를 참조 하세요.

## <a name="solution-architecture"></a>솔루션 아키텍처

공용 인터넷을 통해 데이터 마이그레이션:

![솔루션-아키텍처-공용-네트워크](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- 이 아키텍처에서 데이터는 공용 인터넷을 통해 HTTPS를 사용 하 여 안전 하 게 전송 됩니다. 
- 공개 네트워크 환경에서를 사용 하는 것이 좋습니다. 이렇게 하면 기존 강력한 클러스터를 활용 하 여 최상의 복사 처리량을 얻을 수 있을 뿐만 아니라 ADF에서 유연한 예약 및 통합 모니터링 환경을 활용할 수도 있습니다.
- DistCp 명령을 hadoop 클러스터에 제출 하 고 복사 상태를 모니터링 하려면 회사 방화벽 뒤에 있는 Windows 컴퓨터에 ADF 자체 호스팅 통합 런타임을 설치 해야 합니다. 이 컴퓨터는 데이터를 이동 하기 위한 엔진이 아닙니다 (제어 목적 으로만). 컴퓨터 용량은 데이터 이동 처리량에 영향을 주지 않습니다.
- DistCp 명령의 기존 매개 변수가 지원 됩니다.


개인 링크를 통해 데이터 마이그레이션: 

![해결 방법-개인-네트워크](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- 이 아키텍처에서 데이터 마이그레이션은 공용 인터넷을 통해 순회 되지 않도록 Azure Express 경로를 통해 개인 피어 링 링크를 통해 수행 됩니다.
- DistCp 도구는 Express 경로 개인 피어 링 + Azure Storage VNet 끝점을 지원 하지 않으므로 통합 런타임을 통해 ADF 기본 기능을 사용 하 여 데이터를 마이그레이션하는 것이 좋습니다.
- 이 아키텍처를 구현 하려면 Azure virtual network 내의 Windows VM에 ADF 자체 호스팅 통합 런타임을 설치 해야 합니다. 수동으로 VM을 확장 하거나 여러 Vm으로 확장 하 여 네트워크 및 저장소 IOPS/대역폭을 완벽 하 게 활용할 수 있습니다.
- 각 Azure VM (ADF 자체 호스팅 통합 런타임 설치 됨)에 대해 시작 하는 권장 구성은 32 vCPU 및 128 GB 메모리로 Standard_D32s_v3 됩니다. 데이터 마이그레이션 중에 vm의 CPU 및 메모리 사용률을 계속 모니터링 하 여 더 나은 성능을 위해 VM을 강화 하거나 비용을 절약 하기 위해 vm을 확장할 필요가 있는지 확인할 수 있습니다.
- 단일 자체 호스팅 IR을 사용 하 여 최대 4 개의 VM 노드를 연결 하 여 규모를 확장할 수도 있습니다. 자체 호스팅 IR에 대해 실행 되는 단일 복사 작업은 자동으로 파일 집합을 분할 하 고 모든 VM 노드를 활용 하 여 파일을 병렬로 복사 합니다. 고가용성을 위해 데이터 마이그레이션 중 단일 실패 지점을 방지 하기 위해 두 개의 VM 노드로 시작 하는 것이 좋습니다.
- 초기 스냅숏 데이터 마이그레이션과 델타 데이터 마이그레이션은 모두이 아키텍처를 사용 하 여 달성할 수 있습니다.


## <a name="implementation-best-practices"></a>구현 모범 사례 

### <a name="authentication-and-credential-management"></a>인증 및 자격 증명 관리 

- HDFS에 인증 하려면 [Windows (Kerberos) 또는 익명](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)을 사용할 수 있습니다. 
- Azure Blob Storage에 연결 하기 위해 여러 인증 유형이 지원 됩니다.  [Azure 리소스에 관리](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) 되는 id를 사용 하는 것이 좋습니다. azure AD에서 자동으로 관리 되는 ADF 식별을 기반으로 구축 된 연결 된 서비스 정의에서 자격 증명을 제공 하지 않고 파이프라인을 구성할 수 있습니다.  또는 [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [공유 액세스 서명](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)또는 [저장소 계정 키](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)를 사용 하 여 Azure Blob Storage에 인증할 수 있습니다. 
- Azure Data Lake Storage Gen2에 연결 하는 데에도 여러 인증 형식이 지원 됩니다.  [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) 또는 [저장소 계정 키](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 를 사용할 수도 있지만 [Azure 리소스에 관리](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) 되는 id를 사용 하는 것이 좋습니다. 
- Azure 리소스에 관리 되는 id를 사용 하지 않는 경우 [에는 Azure Key Vault에 자격 증명을 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 하는 것이 좋습니다. ADF 연결 된 서비스를 수정 하지 않고도 키를 중앙에서 중앙에서 관리 하 고 회전할 수 있습니다.  [CI/CD에 대 한 모범 사례](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)중 하나 이기도 합니다. 

### <a name="initial-snapshot-data-migration"></a>초기 스냅숏 데이터 마이그레이션 

ADF DistCp 모드에서는 초기 데이터 마이그레이션 동작을 제어 하기 위해 서로 다른 매개 변수를 사용 하 여 DistCp 명령을 제출할 하나의 복사 작업을 만들 수 있습니다. 

ADF native IR 모드에서 데이터 파티션은 10TB를 초과 하는 데이터를 마이그레이션할 때 특히 권장 됩니다. 데이터를 분할 하려면 HDFS에서 폴더 이름을 활용 합니다. 그러면 각 ADF 복사 작업에서 한 번에 하나의 폴더 파티션을 복사할 수 있습니다. 더 나은 처리량을 위해 여러 ADF 복사 작업을 동시에 실행할 수 있습니다.
네트워크 또는 데이터 저장소의 일시적인 문제로 인해 복사 작업이 실패 하는 경우 실패 한 복사 작업을 다시 실행 하 여 HDFS에서 특정 파티션을 다시 다시 로드할 수 있습니다. 다른 파티션을 로드 하는 다른 모든 복사 작업에는 영향을 주지 않습니다.

### <a name="delta-data-migration"></a>델타 데이터 마이그레이션 

ADF DistCp 모드에서 DistCp 명령줄 매개 변수 "-업데이트, 원본 파일 및 대상 파일의 크기가 다른 경우 데이터 쓰기"를 활용 하 여 델타 데이터 마이그레이션을 달성할 수 있습니다.

ADF native IR 모드에서 HDFS에서 새로운 파일 또는 변경 된 파일을 식별 하는 가장 효율적인 방법은 시간 분할 명명 규칙을 사용 하는 것입니다. HDFS의 데이터는 파일 또는 폴더 이름 (예:/yyyy/mm/dd/)의 시간 조각 정보를 사용 하 여 분할 된 시간입니다. 파일 .csv)를 사용할 수 있습니다. 그러면 파이프라인에서 증분식으로 복사할 파일/폴더를 쉽게 식별할 수 있습니다.
또는 HDFS의 데이터가 시간 분할 되지 않은 경우 ADF는 LastModifiedDate 새 파일 또는 변경 된 파일을 식별할 수 있습니다. 이 방법은 ADF가 HDFS에서 모든 파일을 검사 하 고 마지막으로 수정 된 타임 스탬프가 특정 값 보다 큰 새 파일 및 업데이트 된 파일만 복사 하는 것입니다. HDFS에 많은 수의 파일이 있는 경우 초기 파일 검색은 필터 조건과 일치 하는 파일 수에 관계 없이 시간이 오래 걸릴 수 있습니다. 이 경우 초기 스냅숏 마이그레이션에 동일한 파티션을 사용 하 여 데이터를 먼저 분할 하는 것이 좋습니다. 따라서 파일 검색은 병렬로 수행 될 수 있습니다.

### <a name="estimating-price"></a>가격 예측 

HDFS에서 Azure Blob Storage로 데이터를 마이그레이션하기 위해 생성 된 다음 파이프라인을 고려 합니다. 

![가격-파이프라인](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

다음을 가정해 보세요. 

- 총 데이터 볼륨은 1 PB입니다.
- 두 번째 솔루션 아키텍처를 사용 하 여 데이터 마이그레이션 (ADF native IR 모드)
- 1 PB는 1000 파티션으로 나뉘어 각 복사본은 하나의 파티션으로 이동 합니다.
- 각 복사 작업은 4 대의 컴퓨터에 연결 된 하나의 자체 호스팅 IR로 구성 되며 500 MBps 처리량을 달성 합니다.
- ForEach 동시성은 4로 설정 되 고 집계 처리량은 2Gbps입니다.
- 마이그레이션을 완료 하는 데는 총 146 시간이 소요 됩니다.


다음은 위의 가정에 따라 예상 되는 가격입니다. 

![가격 책정-테이블](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> 이는 가상의 가격 책정 예입니다.  실제 가격은 사용자 환경의 실제 처리량에 따라 달라 집니다.
> Azure Windows VM (자체 호스팅 통합 런타임이 설치 된)의 가격은 포함 되지 않습니다.

### <a name="additional-references"></a>추가 참조 
- [HDFS 커넥터](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob Storage 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [복사 작업 성능 조정 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [자체 호스팅 Integration Runtime 만들기 및 구성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [자체 호스팅 통합 런타임 HA 및 확장성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [데이터 이동 보안 고려 사항](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure Key Vault에 자격 증명 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [분할 된 시간 파일 이름에 따라 증분 방식으로 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [LastModifiedDate에 따라 새 파일 및 변경 된 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory를 사용 하 여 여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)