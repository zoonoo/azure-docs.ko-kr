---
title: Azure Data Factory를 사용 하 여 온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션 | Microsoft Docs
description: Azure Data Factory를 사용 하 여 온-프레미스 Netezza 서버에서 Azure로 데이터를 마이그레이션합니다.
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
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259562"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Azure Data Factory를 사용 하 여 온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션 

Azure Data Factory은 온-프레미스 Netezza 서버에서 Azure Storage 또는 Azure SQL Data Warehouse으로 대규모로 데이터를 마이그레이션하는 데 성능과 강력 하 고 비용 효율적인 메커니즘을 제공 합니다. 이 문서에서는 데이터 엔지니어와 개발자에 대 한 다음 정보를 제공 합니다.

> [!div class="checklist"]
> * 성능 
> * 복원 력
> * 네트워크 보안
> * 개략적인 솔루션 아키텍처 
> * 구현 모범 사례  

## <a name="performance"></a>성능

Azure Data Factory는 서로 다른 수준에서 병렬 처리를 허용 하는 서버 리스 아키텍처를 제공 합니다 .이를 통해 개발자는 네트워크 대역폭 뿐만 아니라 데이터베이스 대역폭을 완벽 하 게 활용 하 여에 대 한 데이터 이동 처리량을 최대화할 수 있습니다. 개발.

![성능](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- 단일 복사 작업은 확장 가능한 계산 리소스를 활용할 수 있습니다. Azure Integration Runtime를 사용 하는 경우 서버를 사용 하지 않는 방식으로 각 복사 작업에 대해 [최대 256 DIUs를](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) 지정할 수 있습니다. 자체 호스트 된 Integration Runtime를 사용 하는 경우 수동으로 컴퓨터를 확장 하거나 여러 컴퓨터로 확장할 수 있습니다 ([최대 4 개 노드](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)). 단일 복사 작업은 모든 노드에 파티션을 배포 합니다. 
- 단일 복사 작업은 여러 스레드를 사용 하 여 데이터 저장소에서 읽고 씁니다. 
- Azure Data Factory 제어 흐름은 [For each 루프](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)를 사용 하는 등의 여러 복사 작업을 병렬로 시작할 수 있습니다. 

[복사 작업 성능 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) 에서 자세한 내용을 볼 수 있습니다.

## <a name="resilience"></a>복원력

단일 복사 작업 실행 내에서 Azure Data Factory에는 기본 제공 재시도 메커니즘이 있으므로 데이터 저장소나 기본 네트워크에서 특정 수준의 일시적인 오류를 처리할 수 있습니다.

또한 Azure Data Factory 복사 작업은 원본 및 싱크 데이터 저장소 간에 데이터를 복사할 때 호환 되지 않는 행을 처리 하는 두 가지 방법을 제공 합니다. 호환 되지 않는 데이터가 발견 될 때 복사 작업을 중단 하 고 실패 하거나 호환 되지 않는 데이터 행을 건너뛰어 나머지 데이터를 계속 복사할 수 있습니다. 또한 Azure Blob storage 또는 Azure Data Lake Store에서 호환 되지 않는 행을 기록 하 여 오류의 원인을 파악 하 고, 데이터 원본에서 데이터를 수정 하 고, 복사 작업을 다시 시도할 수 있습니다.

## <a name="network-security"></a>네트워크 보안 

기본적으로 Azure Data Factory 온-프레미스 Netezza 서버에서 Azure Storage 또는 HTTPS 프로토콜을 통한 암호화 된 연결을 사용 하는 Azure SQL Data Warehouse 데이터를 전송 합니다. 전송 중인 데이터 암호화를 제공 하 고 도청 및 메시지 가로채기 (man-in-the-middle) 공격을 방지 합니다.

또는 공용 인터넷을 통해 데이터를 전송 하지 않으려면 Azure Express 경로를 통해 개인 피어 링 링크를 통해 데이터를 전송 하 여 더 높은 수준의 보안을 달성할 수 있습니다. 이를 달성할 수 있는 방법은 아래의 솔루션 아키텍처를 참조 하세요.

## <a name="solution-architecture"></a>솔루션 아키텍처

공용 인터넷을 통해 데이터 마이그레이션:

![솔루션-아키텍처-공용-네트워크](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- 이 아키텍처에서 데이터는 공용 인터넷을 통해 https를 사용 하 여 안전 하 게 전송 됩니다.
- 이 아키텍처를 구현 하려면 회사 방화벽 뒤에 있는 windows 컴퓨터에 자체 호스팅 통합 런타임을 Azure Data Factory 설치 해야 합니다. Windows 컴퓨터의 Azure Data Factory 자체 호스팅 통합 런타임이 Netezza 서버에 직접 액세스할 수 있는지 확인 합니다. 수동으로 컴퓨터를 확장 하거나 여러 컴퓨터로 확장 하 여 네트워크 및 데이터 저장소 대역폭을 완벽 하 게 활용 하 여 데이터를 복사할 수 있습니다.
- 초기 스냅숏 데이터 마이그레이션과 델타 데이터 마이그레이션은 모두이 아키텍처를 사용 하 여 달성할 수 있습니다.

개인 링크를 통해 데이터 마이그레이션: 

![해결 방법-개인-네트워크](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- 이 아키텍처에서 데이터 마이그레이션은 공용 인터넷을 통해 순회 되지 않도록 Azure Express 경로를 통해 개인 피어 링 링크를 통해 수행 됩니다. 
- 이 아키텍처를 구현 하려면 Azure virtual network 내의 Windows VM에 자체 호스팅 통합 런타임을 Azure Data Factory 설치 해야 합니다. 수동으로 Vm을 확장 하거나 여러 Vm으로 확장 하 여 네트워크 및 데이터 저장소 대역폭을 완벽 하 게 활용 하 여 데이터를 복사할 수 있습니다.
- 초기 스냅숏 데이터 마이그레이션과 델타 데이터 마이그레이션은 모두이 아키텍처를 사용 하 여 달성할 수 있습니다.

## <a name="implementation-best-practices"></a>구현 모범 사례 

### <a name="authentication-and-credential-management"></a>인증 및 자격 증명 관리 

- Netezza에 인증 하기 위해 [연결 문자열을 통해 ODBC 인증](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)을 사용할 수 있습니다. 
- Azure Blob Storage에 연결 하기 위해 여러 인증 유형이 지원 됩니다.  [Azure 리소스에 대해 관리](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) 되는 id를 사용 하는 것이 좋습니다. azure AD에서 자동으로 관리 되는 Azure Data Factory 식별을 기반으로 구축 된 연결 된 서비스 정의에서 자격 증명을 제공 하지 않고 파이프라인을 구성할 수 있습니다.  또는 [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [공유 액세스 서명](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)또는 [저장소 계정 키](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)를 사용 하 여 Azure Blob Storage에 인증할 수 있습니다. 
- Azure Data Lake Storage Gen2에 연결 하는 데에도 여러 인증 형식이 지원 됩니다.  [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) 또는 [저장소 계정 키](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 를 사용할 수도 있지만 [Azure 리소스에 관리](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) 되는 id를 사용 하는 것이 좋습니다. 
- Azure SQL Data Warehouse에 연결 하는 데에도 여러 인증 형식이 지원 됩니다. [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) 또는 [SQL 인증](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) 도 사용할 수 있지만 [Azure 리소스에 관리](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) 되는 id를 사용 하는 것이 좋습니다.
- Azure 리소스에 관리 되는 id를 사용 하지 않는 경우 [Azure Key Vault에 자격 증명을 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 하는 것이 좋습니다. Azure Data Factory 연결 된 서비스를 수정 하지 않고 키를 중앙에서 보다 쉽게 관리 하 고 회전할 수 있습니다.  [CI/CD에 대 한 모범 사례](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)중 하나 이기도 합니다. 

### <a name="initial-snapshot-data-migration"></a>초기 스냅숏 데이터 마이그레이션 

작은 테이블의 경우 볼륨 크기가 100 GB 보다 작거나 2 시간 내에 Azure로 마이그레이션될 수 있는 경우 각 복사 작업을 통해 테이블당 데이터를 로드할 수 있습니다. 여러 Azure Data Factory 복사 작업을 실행 하 여 처리량을 높일 수 있도록 여러 테이블을 동시에 로드할 수 있습니다. 

각 복사 작업 내에서 [parallelCopies setting](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) with data partition 옵션을 사용 하 여 병렬 쿼리를 실행 하 고 파티션 별로 데이터를 복사 하 여 일부 병렬 처리 수준에 도달할 수도 있습니다. 아래 세부 정보를 사용 하 여 두 가지 데이터 파티션 옵션을 선택할 수 있습니다.
- 데이터 조각에서 시작 하는 것이 더 효율적 이기 때문에 권장 됩니다.  ParallelCopies 설정의 병렬 처리 수가 Netezza 서버에 있는 테이블의 총 데이터 조각 파티션 수 아래에 있는지 확인 합니다.  
- 각 데이터 조각 파티션의 볼륨 크기가 여전히 큰 경우 (예: 10gb 보다 큰 경우) 동적 범위 파티션으로 전환 하는 것이 좋습니다 .이 경우 파티션 수와 각 파티션의 볼륨 크기를 보다 유연 하 게 정의할 수 있습니다. 파티션 열, 상한 및 하 한을 기준으로 합니다.

큰 테이블의 경우 볼륨 크기가 100 GB 보다 크거나 2 시간 내에 Azure로 마이그레이션할 수 없는 경우 사용자 지정 쿼리를 통해 데이터를 분할 한 다음 각 복사 작업에서 한 번에 하나의 파티션을 복사 하는 것이 좋습니다. 더 나은 처리량을 위해 여러 Azure Data Factory 복사 작업을 동시에 실행할 수 있습니다. 각 복사 작업 대상이 사용자 지정 쿼리로 하나의 파티션을 로드 하는 경우 데이터 조각 또는 동적 범위를 통해 병렬 처리를 사용 하도록 설정 하 여 처리량을 늘릴 수 있습니다. 

네트워크 또는 데이터 저장소의 일시적인 문제로 인해 복사 작업이 실패 하는 경우 실패 한 복사 작업을 다시 실행 하 여 테이블에서 특정 파티션을 다시 다시 로드할 수 있습니다. 다른 파티션을 로드 하는 다른 모든 복사 작업에는 영향을 주지 않습니다.

Azure SQL Data Warehouse에 데이터를 로드할 때 Polybase는 Azure blob storage를 사용 하 여 복사 작업 내에서 스테이징으로 사용 하도록 제안 하는 것이 좋습니다.

### <a name="delta-data-migration"></a>델타 데이터 마이그레이션 

테이블에서 새 행 또는 업데이트 된 행을 식별 하는 방법은 타임 스탬프 열을 사용 하거나 스키마 내에서 키를 증가 시키고 다음에 데이터를 로드할 때 델타 데이터를 필터링 하는 데 사용할 수 있는 외부 테이블에 최신 값을 상위 워터 마크로 저장 하는 것입니다. 

각 테이블은 다른 워터 마크 열을 사용 하 여 새 행 또는 업데이트 된 행을 식별할 수 있습니다. 각 행이 특정 워터 마크 열 이름 및 상위 워터 마크 값을 사용 하 여 Netezza 서버에 있는 하나의 테이블을 나타내는 외부 컨트롤 테이블을 만드는 것이 좋습니다. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Azure VM 또는 컴퓨터에서 자체 호스팅 통합 런타임 구성

Netezza 서버에서 Azure로 데이터를 마이그레이션하는 경우 Netezza server가 회사 방화벽 뒤 또는 VNET 환경 내에서 제공 되는 것이 아니라 이동할 엔진인 windows 컴퓨터 또는 VM에 자체 호스팅 Integration runtime을 설치 해야 합니다. 데이터로.

- 각 컴퓨터 또는 VM에 대해에서 시작 하는 권장 구성은 32 vCPU 및 128 GB 메모리를 사용 하는 것입니다. 데이터 마이그레이션 중에 IR 컴퓨터의 CPU 및 메모리 사용률을 계속 모니터링 하 여 성능 향상을 위해 컴퓨터를 추가로 확장 하거나 컴퓨터를 축소 하 여 비용을 절감할 수 있는지 확인할 수 있습니다.
- 단일 자체 호스팅 IR을 사용 하 여 최대 4 개의 노드를 연결 하 여 규모를 확장할 수도 있습니다. 자체 호스팅 IR에 대해 실행 되는 단일 복사 작업은 자동으로 모든 VM 노드를 활용 하 여 데이터를 병렬로 복사 합니다. 고가용성을 위해 데이터 마이그레이션 중 단일 실패 지점을 방지 하기 위해 2 개의 VM 노드로 시작 하는 것이 좋습니다.

### <a name="rate-limiting"></a>속도 제한

각 복사 작업의 적절 한 파티션 크기를 확인할 수 있도록 대표적인 샘플 데이터 집합을 사용 하 여 성능 POC를 수행 하는 것이 가장 좋습니다. 각 파티션이 2 시간 내에 Azure에 로드 되도록 하는 것이 좋습니다.  

단일 자체 호스팅 IR 컴퓨터를 사용 하 여 테이블을 복사 하는 단일 복사 작업으로 시작 합니다. 테이블의 데이터 조각 파티션 수에 따라 parallelCopies 설정을 점차적으로 늘리고 복사 작업에서 볼 수 있는 처리량에 따라 2 시간 내에 전체 테이블을 Azure에 로드할 수 있는지 확인 합니다. 

이러한 작업을 수행할 수 없는 경우와 동시에 자체 호스팅 IR 노드 및 데이터 저장소의 용량이 충분히 활용 되지 않는 경우 네트워크 제한 또는 데이터 저장소의 대역폭 제한에 도달할 때까지 동시 복사 작업 수를 점진적으로 증가 시킵니다. 

자체 호스팅 IR 컴퓨터에서 CPU/메모리 사용률을 계속 모니터링 하 고, CPU/메모리가 완전히 활용 되는 경우 컴퓨터를 확장 하거나 여러 컴퓨터로 확장할 수 있습니다. 

Azure Data Factory 복사 작업에서 보고 하는 제한 오류가 발생할 경우 Azure Data Factory에서 동시성 또는 parallelCopies 설정을 줄이거나 네트워크 및 데이터 저장소의 대역폭/IOPS 제한을 늘려야 합니다. 


### <a name="estimating-price"></a>가격 예측 

온-프레미스 Netezza 서버에서 Azure SQL data warehouse로 데이터를 마이그레이션하기 위해 생성 된 다음 파이프라인을 고려 합니다.

![가격-파이프라인](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

다음을 가정해 보세요. 

- 총 데이터 볼륨은 50 TB입니다. 
- 첫 번째 솔루션 아키텍처를 사용 하 여 데이터 마이그레이션 (Netezza server는 방화벽 뒤에 온-프레미스)
- 50 TB는 500 파티션으로 나뉘어 각 복사 작업은 한 개의 파티션을 이동 합니다.
- 각 복사 작업은 4 대의 컴퓨터에 대해 하나의 자체 호스팅 IR로 구성 되며 20Mbps 처리량을 달성 합니다. 복사 작업 내에서 parallelCopies은 4로 설정 되 고 테이블에서 데이터를 로드 하는 각 스레드는 5Mbps 처리량을 달성 합니다.
- ForEach 동시성은 3으로 설정 되 고 집계 처리량은 60 MBps입니다.
- 마이그레이션을 완료 하는 데는 총 243 시간이 소요 됩니다.

다음은 위의 가정에 따라 예상 되는 가격입니다. 

![가격 책정-테이블](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 이는 가상의 가격 책정 예입니다. 실제 가격은 사용자 환경의 실제 처리량에 따라 달라 집니다. 자체 호스팅 통합 런타임을 설치한 windows 컴퓨터의 가격은 포함 되지 않습니다. 

### <a name="additional-references"></a>추가 참조 
- [Azure Data Factory를 사용 하 여 온-프레미스 관계형 데이터 웨어하우스에서 Azure로 데이터 마이그레이션](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Netezza 커넥터](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC 커넥터](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob Storage 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL Data Warehouse 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [복사 작업 성능 조정 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [자체 호스팅 Integration Runtime 만들기 및 구성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [자체 호스팅 통합 런타임 HA 및 확장성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [데이터 이동 보안 고려 사항](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure Key Vault에 자격 증명 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [한 테이블에서 증분 방식으로 데이터 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [여러 테이블에서 증분 방식으로 데이터 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [가격 책정 페이지 Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory를 사용 하 여 여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)