---
title: 온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션
description: Azure 데이터 팩터리를 사용하여 온-프레미스 Netezza 서버에서 Azure로 데이터를 마이그레이션합니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: a0263880262da95f4d26ee8388da464e9a59efca
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416452"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Azure 데이터 팩터리를 사용하여 온-프레미스 Netezza 서버에서 Azure로 데이터를 마이그레이션합니다. 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory는 온-프레미스 Netezza 서버에서 Azure 저장소 계정 또는 Azure SQL Data Warehouse 데이터베이스로 대규모로 데이터를 마이그레이션하는 성능이 뛰어나고 강력하며 비용 효율적인 메커니즘을 제공합니다. 

이 문서에서는 데이터 엔지니어와 개발자를 위한 다음 정보를 제공합니다.

> [!div class="checklist"]
> * 성능 
> * 복사 복원력
> * 네트워크 보안
> * 고급 솔루션 아키텍처 
> * 구현 모범 사례  

## <a name="performance"></a>성능

Azure Data Factory는 다양한 수준에서 병렬 처리가 허용하는 서버리스 아키텍처를 제공합니다. 개발자인 경우 네트워크 대역폭과 데이터베이스 대역폭을 모두 완전히 사용하여 환경에 대한 데이터 이동 처리량을 최대화하는 파이프라인을 빌드할 수 있습니다.

![성능 다이어그램](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

앞의 다이어그램은 다음과 같이 해석될 수 있습니다.

- 단일 복사 활동은 확장 가능한 컴퓨팅 리소스를 활용할 수 있습니다. Azure 통합 런타임을 사용하는 경우 서버없는 방식으로 각 복사 활동에 대해 [최대 256개의 DIUs를](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) 지정할 수 있습니다. 자체 호스팅 통합 런타임(자체 호스팅 IR)을 사용하면 수동으로 컴퓨터를 확장하거나 여러[컴퓨터(최대 4개 노드)로](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)확장할 수 있으며 단일 복사 활동은 모든 노드에 파티션을 배포합니다. 

- 단일 복사 활동은 여러 스레드를 사용하여 데이터 저장소를 읽고 기록합니다. 

- Azure Data Factory 컨트롤 흐름은 여러 복사 활동을 병렬로 시작할 수 있습니다. 예를 들어 For [Each 루프를](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)사용하여 시작할 수 있습니다. 

자세한 내용은 [활동 성능 및 확장성 복사 가이드를](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)참조하십시오.

## <a name="resilience"></a>탄력성

단일 복사 활동 실행 내에서 Azure Data Factory에는 데이터 저장소 또는 기본 네트워크에서 특정 수준의 일시적인 오류를 처리할 수 있는 기본 제공 재시도 메커니즘이 있습니다.

Azure Data Factory 복사 활동을 사용하면 원본 및 싱크 데이터 저장소 간에 데이터를 복사할 때 호환되지 않는 행을 처리하는 두 가지 방법이 있습니다. 복사 활동을 중단하고 실패하거나 호환되지 않는 데이터 행을 건너뛰면 나머지 데이터를 계속 복사할 수 있습니다. 또한 오류의 원인을 알아보려면 Azure Blob 저장소 또는 Azure Data Lake Store에서 호환되지 않는 행을 기록하고 데이터 원본의 데이터를 수정하고 복사 작업을 다시 시도할 수 있습니다.

## <a name="network-security"></a>네트워크 보안 

기본적으로 Azure Data Factory는 하이퍼텍스트 전송 프로토콜 보안(HTTPS)을 통해 암호화된 연결을 사용하여 온-프레미스 Netezza 서버에서 Azure 저장소 계정 또는 Azure SQL Data Warehouse 데이터베이스로 데이터를 전송합니다. HTTPS는 전송 중에 데이터 암호화를 제공하고 도청 및 중간자 공격을 방지합니다.

또는 공용 인터넷을 통해 데이터를 전송하지 않으려면 Azure Express Route를 통해 개인 피어링 링크를 통해 데이터를 전송하여 더 높은 보안을 달성할 수 있습니다. 

다음 섹션에서는 더 높은 보안을 달성하는 방법에 대해 설명합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

이 섹션에서는 데이터를 마이그레이션하는 두 가지 방법에 대해 설명합니다.

### <a name="migrate-data-over-the-public-internet"></a>공용 인터넷을 통해 데이터 마이그레이션

![공용 인터넷을 통해 데이터 마이그레이션](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

앞의 다이어그램은 다음과 같이 해석될 수 있습니다.

- 이 아키텍처에서는 공용 인터넷을 통해 HTTPS를 사용하여 데이터를 안전하게 전송합니다.

- 이 아키텍처를 달성하려면 회사 방화벽 뒤에 있는 Windows 컴퓨터에 Azure Data Factory 통합 런타임(자체 호스팅)을 설치해야 합니다. 이 통합 런타임이 Netezza 서버에 직접 액세스할 수 있는지 확인합니다. 네트워크 및 데이터 저장 대역폭을 사용하여 데이터를 복사하려면 수동으로 컴퓨터를 확장하거나 여러 컴퓨터로 확장할 수 있습니다.

- 이 아키텍처를 사용하여 초기 스냅숏 데이터와 델타 데이터를 모두 마이그레이션할 수 있습니다.

### <a name="migrate-data-over-a-private-network"></a>개인 네트워크를 통해 데이터 마이그레이션 

![개인 네트워크를 통해 데이터 마이그레이션](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

앞의 다이어그램은 다음과 같이 해석될 수 있습니다.

- 이 아키텍처에서는 Azure Express Route를 통해 개인 피어링 링크를 통해 데이터를 마이그레이션하고 데이터는 공용 인터넷을 통해 트래버스하지 않습니다. 

- 이 아키텍처를 달성하려면 Azure 가상 네트워크 내의 Windows 가상 시스템(VM)에 Azure Data Factory 통합 런타임(자체 호스팅)을 설치해야 합니다. 네트워크 및 데이터 저장 대역폭을 사용하여 데이터를 복사하려면 수동으로 VM을 확장하거나 여러 VM으로 확장할 수 있습니다.

- 이 아키텍처를 사용하여 초기 스냅숏 데이터와 델타 데이터를 모두 마이그레이션할 수 있습니다.

## <a name="implement-best-practices"></a>모범 사례 구현 

### <a name="manage-authentication-and-credentials"></a>인증 및 자격 증명 관리 

- Netezza를 인증하려면 [연결 문자열을 통해 ODBC 인증을](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)사용할 수 있습니다. 

- Azure Blob 저장소를 인증하려면 다음을 수행합니다. 

   - [Azure 리소스에 대해 관리되는 ID를](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)사용하는 것이 좋습니다. Azure Active Directory(Azure AD)에서 자동으로 관리되는 Azure Data Factory ID 위에 구축된 관리 ID를 사용하면 연결된 서비스 정의에서 자격 증명을 제공할 필요 없이 파이프라인을 구성할 수 있습니다.  

   - 또는 [서비스 주체,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [공유 액세스 서명](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)또는 저장소 계정 [키를](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)사용하여 Azure Blob 저장소에 인증할 수 있습니다. 

- Azure 데이터 레이크 저장소 Gen2로 인증하려면 다음을 수행하십시오. 

   - [Azure 리소스에 대해 관리되는 ID를](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)사용하는 것이 좋습니다.
   
   - [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) 또는 [저장소 계정 키를](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)사용할 수도 있습니다. 

- Azure SQL 데이터 웨어하우스를 인증하려면 다음을 수행하십시오.

   - [Azure 리소스에 대해 관리되는 ID를](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)사용하는 것이 좋습니다.
   
   - [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) 또는 [SQL 인증을](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)사용할 수도 있습니다.

- Azure 리소스에 대해 관리되는 ID를 사용하지 않는 경우 Azure Data Factory 연결 서비스를 수정하지 않고도 키를 중앙에서 쉽게 관리하고 회전할 수 있도록 [Azure Key Vault에 자격 증명을 저장하는](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 것이 좋습니다. 이는 [CI/CD의 모범 사례](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)중 하나이기도 합니다. 

### <a name="migrate-initial-snapshot-data"></a>초기 스냅샷 데이터 마이그레이션 

작은 테이블(즉, 볼륨이 100GB 미만이거나 2시간 이내에 Azure로 마이그레이션할 수 있는 테이블)의 경우 테이블당 각 복사 작업 로드 데이터를 만들 수 있습니다. 처리량을 높이기 위해 여러 Azure Data Factory 복사 작업을 실행하여 별도의 테이블을 동시에 로드할 수 있습니다. 

각 복사 작업 내에서 병렬 쿼리를 실행하고 파티션별로 데이터를 복사하려면 다음 데이터 파티션 옵션 중 하나를 사용하여 [ `parallelCopies` 속성 설정을](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) 사용하여 일정 수준의 병렬 처리에 도달할 수도 있습니다.

- 효율성을 높이기 위해 데이터 조각부터 시작하는 것이 좋습니다.  설정의 `parallelCopies` 값이 Netezza 서버의 테이블의 총 데이터 조각 파티션 수보다 작해야 합니다.  

- 각 데이터 조각 파티션의 볼륨이 여전히 큰 경우(예: 10GB 이상) 동적 범위 파티션으로 전환하는 것이 좋습니다. 이 옵션을 사용하면 파티션 열, 상한 및 하한으로 인해 파티션 수와 각 파티션의 볼륨을 보다 유연하게 정의할 수 있습니다.

볼륨이 100GB 이상이거나 2시간 이내에 Azure로 마이그레이션할 *수 없는* 큰 테이블의 경우 사용자 지정 쿼리별로 데이터를 분할한 다음 각 복사 작업 복사본을 한 번에 하나의 파티션으로 만드는 것이 좋습니다. 더 나은 처리량을 위해 여러 Azure Data Factory 복사 작업을 동시에 실행할 수 있습니다. 사용자 지정 쿼리별로 하나의 파티션을 로드하는 각 복사 작업 대상에 대해 데이터 조각 또는 동적 범위를 통해 병렬 처리를 사용하도록 설정하여 처리량을 늘릴 수 있습니다. 

네트워크 또는 데이터 저장소 일시적인 문제로 인해 복사 작업이 실패하면 실패한 복사 작업을 다시 실행하여 테이블에서 해당 특정 파티션을 다시 로드할 수 있습니다. 다른 파티션을 로드하는 다른 복사 작업은 영향을 받지 않습니다.

Azure SQL 데이터 웨어하우스 데이터베이스에 데이터를 로드하는 경우 Azure Blob 저장소를 스테이징으로 사용하여 복사 작업 내에서 PolyBase를 사용하도록 설정하는 것이 좋습니다.

### <a name="migrate-delta-data"></a>델타 데이터 마이그레이션 

테이블에서 새 행또는 업데이트된 행을 식별하려면 스키마 내에서 타임스탬프 열 또는 증분 키를 사용합니다. 그런 다음 최신 값을 외부 테이블에 높은 워터마크로 저장한 다음 다음에 데이터를 로드할 때 델타 데이터를 필터링하는 데 사용할 수 있습니다. 

각 테이블은 다른 워터마크 열을 사용하여 새 행 또는 업데이트된 행을 식별할 수 있습니다. 외부 제어 테이블을 만드는 것이 좋습니다. 표에서 각 행은 Netezza 서버의 특정 워터마크 열 이름과 높은 워터마크 값을 가진 하나의 테이블을 나타냅니다. 

### <a name="configure-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 구성

Netezza 서버에서 Azure로 데이터를 마이그레이션하는 경우 서버가 회사 방화벽 뒤의 온-프레미스인지 가상 네트워크 환경 내에 있든 간에 데이터를 이동하는 데 사용되는 엔진인 Windows 컴퓨터 또는 VM에 자체 호스팅 IR을 설치해야 합니다. 자체 호스팅 IR을 설치할 때 다음 방법을 권장합니다.

- 각 Windows 컴퓨터 또는 VM에 대해 32vCPU 및 128GB 메모리의 구성으로 시작합니다. 데이터 마이그레이션 중에 IR 시스템의 CPU 및 메모리 사용량을 계속 모니터링하여 더 나은 성능을 위해 컴퓨터를 추가로 확장해야 하는지 또는 비용을 절감하기 위해 컴퓨터를 축소해야 하는지 확인할 수 있습니다.

- 단일 자체 호스팅 IR을 사용하여 최대 4개의 노드를 연결하여 확장할 수도 있습니다. 자체 호스팅 IR에 대해 실행되는 단일 복사 작업은 모든 VM 노드를 자동으로 적용하여 데이터를 병렬로 복사합니다. 고가용성의 경우 데이터 마이그레이션 중에 단일 실패 지점을 방지하려면 4개의 VM 노드로 시작합니다.

### <a name="limit-your-partitions"></a>파티션 제한

모범 사례로 대표 샘플 데이터 집합을 사용하여 POC(성능 증명 개념 증명)를 수행하여 각 복사 활동에 적합한 파티션 크기를 결정할 수 있습니다. 2시간 이내에 각 파티션을 Azure에 로드하는 것이 좋습니다.  

테이블을 복사하려면 자체 호스팅IR 컴퓨터로 단일 복사 활동으로 시작합니다. 테이블의 `parallelCopies` 데이터 조각 파티션 수에 따라 설정을 점진적으로 늘립니다. 복사 작업의 처리량에 따라 전체 테이블을 2시간 이내에 Azure에 로드할 수 있는지 확인합니다. 

2시간 이내에 Azure에 로드할 수 없고 자체 호스팅 IR 노드및 데이터 저장소의 용량이 완전히 사용되지 않는 경우 네트워크 제한 또는 데이터 저장소의 대역폭 제한에 도달할 때까지 동시 복사 활동 수를 점진적으로 늘립니다. 

자체 호스팅 IR 컴퓨터에서 CPU 및 메모리 사용량을 계속 모니터링하고 CPU와 메모리가 완전히 사용되는 경우 컴퓨터를 확장하거나 여러 컴퓨터로 확장할 준비를 하십시오. 

Azure Data Factory 복사 활동에 의해 보고된 제한 오류가 발생하면 Azure Data `parallelCopies` Factory에서 동시성 또는 설정을 줄이거나 네트워크 및 데이터 저장소의 초당 대역폭 또는 I/O 작업 제한을 늘리는 것이 좋습니다. 


### <a name="estimate-your-pricing"></a>가격 예측 

온-프레미스 Netezza 서버에서 Azure SQL Data Warehouse 데이터베이스로 데이터를 마이그레이션하도록 구성된 다음 파이프라인을 고려하십시오.

![가격 책정 파이프라인](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

다음 명령문이 참이라고 가정해 봅시다. 

- 총 데이터 볼륨은 50테라바이트(TB)입니다. 

- Netezza 서버는 방화벽 뒤에 있는 온-프레미스)를 사용하여 데이터를 마이그레이션하고 있습니다.

- 50TB 볼륨은 500개의 파티션으로 나누며 각 복사 활동은 하나의 파티션을 이동합니다.

- 각 복사 활동은 4대의 컴퓨터에 대해 하나의 자체 호스팅 IR으로 구성되며 초당 20MB(MBps)의 처리량을 달성합니다. (복사 활동 `parallelCopies` 내에서 4로 설정되고 테이블에서 데이터를 로드하는 각 스레드는 5MBps 처리량을 달성합니다.)

- ForEach 동시성은 3으로 설정되고 집계 처리량은 60MBps입니다.

- 마이그레이션을 완료하는 데 는 총 243시간이 걸립니다.

앞의 가정을 바탕으로 예상 가격은 다음과 같습니다. 

![가격 표](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 앞표에 표시된 가격은 가설입니다. 실제 가격은 사용자 환경의 실제 처리량에 따라 다릅니다. 자체 호스팅 IR이 설치된 Windows 컴퓨터의 가격은 포함되어 있지 않습니다. 

### <a name="additional-references"></a>추가 참조

자세한 내용은 다음 문서 및 가이드를 참조하십시오.

- [Azure 데이터 팩터리를 사용하여 온-프레미스 관계형 데이터 웨어하우스 데이터베이스에서 Azure로 데이터 마이그레이션](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [네테자 커넥터](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC 커넥터](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob 저장소 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL 데이터 웨어하우스 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [복사 활동 성능 튜닝 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [자체 호스팅 통합 런타임 만들기 및 구성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [자체 호스팅 통합 런타임 HA 및 확장성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [데이터 이동 보안 고려 사항](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure Key Vault에 자격 증명 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [한 테이블에서 데이터를 증분 방식으로 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [여러 테이블에서 데이터를 증분 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure 데이터 팩터리 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>다음 단계

- [Azure 데이터 팩터리를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)
