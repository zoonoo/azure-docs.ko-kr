---
title: Amazon S3에서 Azure Storage로 데이터 마이그레이션
description: Azure 데이터 팩터리를 사용하여 Amazon S3에서 Azure 저장소로 데이터를 마이그레이션합니다.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 6f2db91a35573bc2cbdd0df2cb1ac09914cc956b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122647"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Azure 데이터 팩터리를 사용하여 Amazon S3에서 Azure 저장소로 데이터 마이그레이션 

Azure Data Factory는 Amazon S3에서 Azure Blob 저장소 또는 Azure Data Lake Gen2로 대규모로 데이터를 마이그레이션하는 성능이 뛰어나고 강력하며 비용 효율적인 메커니즘을 제공합니다.  이 문서에서는 데이터 엔지니어와 개발자를 위한 다음 정보를 제공합니다. 

> [!div class="checklist"]
> * 성능 
> * 복사 복원력
> * 네트워크 보안
> * 고급 솔루션 아키텍처 
> * 구현 모범 사례  

## <a name="performance"></a>성능

ADF는 다양한 수준에서 병렬 처리가 가능한 서버리스 아키텍처를 제공하므로 개발자는 파이프라인을 빌드하여 네트워크 대역폭뿐만 아니라 스토리지 IOPS 및 대역폭을 최대한 활용하여 환경에 대한 데이터 이동 처리량을 최대화할 수 있습니다. 

고객은 Amazon S3에서 Azure Blob Storage로 수억 개의 파일로 구성된 페타바이트 규모의 데이터를 성공적으로 마이그레이션했으며, 처리량은 2GBp 이상입니다. 

![성능](media/data-migration-guidance-s3-to-azure-storage/performance.png)

위의 그림은 다양한 수준의 병렬 처리를 통해 뛰어난 데이터 이동 속도를 달성하는 방법을 보여 줍니다.
 
- 단일 복사 활동은 확장 가능한 계산 리소스를 활용할 수 있습니다. [up to 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) 자체 호스팅 통합 런타임을 사용하는 경우 수동으로 컴퓨터를 확장하거나 여러[컴퓨터(최대 4개 노드)로](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)확장할 수 있으며 단일 복사 활동은 모든 노드에서 파일 집합을 분할합니다. 
- 단일 복사 활동은 여러 스레드를 사용하여 데이터 저장소에서 읽고 기록합니다. 
- ADF 컨트롤 흐름은 예를 들어 [For Each 루프를](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)사용하여 여러 복사 활동을 병렬로 시작할 수 있습니다. 

## <a name="resilience"></a>탄력성

단일 복사 활동 실행 내에서 ADF에는 데이터 저장소 또는 기본 네트워크에서 특정 수준의 일시적인 오류를 처리할 수 있도록 기본 제공 재시도 메커니즘이 있습니다. 

S3에서 Blob으로 이진 복사를 수행하고 S3에서 ADLS Gen2로 이진 복사를 수행할 때 ADF는 자동으로 검사일을 수행합니다.  복사 활동 실행이 실패했거나 시간 초과된 경우 후속 다시 시도에서 복사본은 처음부터 시작하지 않고 마지막 실패 지점에서 다시 시작됩니다. 

## <a name="network-security"></a>네트워크 보안 

기본적으로 ADF는 HTTPS 프로토콜을 통해 암호화된 연결을 사용하여 Amazon S3에서 Azure Blob 저장소 또는 Azure Data Lake Storage Gen2로 데이터를 전송합니다.  HTTPS는 전송 중에 데이터 암호화를 제공하고 도청 및 중간자 공격을 방지합니다. 

또는 공용 인터넷을 통해 데이터를 전송하지 않으려면 AWS Direct Connect와 Azure Express Route 간의 개인 피어링 링크를 통해 데이터를 전송하여 더 높은 보안을 달성할 수 있습니다.  이 작업을 수행할 수 있는 방법에 대한 아래 솔루션 아키텍처를 참조하십시오. 

## <a name="solution-architecture"></a>솔루션 아키텍처

공용 인터넷을 통해 데이터 마이그레이션:

![솔루션 아키텍처- 공용 네트워크](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- 이 아키텍처에서는 공용 인터넷을 통해 HTTPS를 사용하여 데이터가 안전하게 전송됩니다. 
- 원본 Amazon S3와 대상 Azure Blob 저장소 또는 Azure Data Lake 저장소 Gen2모두 모든 네트워크 IP 주소의 트래픽을 허용하도록 구성됩니다.  특정 IP 범위로 네트워크 액세스를 제한하는 방법에 대한 아래 두 번째 아키텍처를 참조하십시오. 
- 서버리스 방식으로 마력의 양을 쉽게 확장하여 네트워크 및 스토리지 대역폭을 최대한 활용하여 환경에 가장 적합한 처리량을 얻을 수 있습니다. 
- 이 아키텍처를 사용하여 초기 스냅숏 마이그레이션 및 델타 데이터 마이그레이션을 모두 수행할 수 있습니다. 

개인 링크를 통해 데이터 마이그레이션: 

![솔루션 아키텍처- 개인 네트워크](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- 이 아키텍처에서 데이터 마이그레이션은 AWS Direct Connect와 Azure Express Route 간의 개인 피어링 링크를 통해 수행되어 데이터가 공용 인터넷을 통해 트래버스되지 않습니다.  AWS VPC 및 Azure 가상 네트워크를 사용해야 합니다. 
- 이 아키텍처를 달성하려면 Azure 가상 네트워크 내의 Windows VM에 ADF 자체 호스팅 통합 런타임을 설치해야 합니다.  자체 호스팅 IR VM을 수동으로 확장하거나 여러 VM(최대 4개 노드)으로 확장하여 네트워크 및 스토리지 IOPS/대역폭을 최대한 활용할 수 있습니다. 
- HTTPS를 통해 데이터를 전송할 수 있지만 소스 S3에 대한 네트워크 액세스를 특정 IP 범위로 잠그려는 경우 AWS VPC를 제거하고 개인 링크를 HTTPS로 대체하여 이 아키텍처의 변형을 채택할 수 있습니다.  Azure 가상 및 자체 호스팅 IR을 Azure VM에 유지하여 허용 목록 목적으로 정적 공개적으로 라우팅 가능한 IP를 가질 수 있도록 해야 합니다. 
- 이 아키텍처를 사용하여 초기 스냅숏 데이터 마이그레이션과 델타 데이터 마이그레이션을 모두 수행할 수 있습니다. 

## <a name="implementation-best-practices"></a>구현 모범 사례 

### <a name="authentication-and-credential-management"></a>인증 및 자격 증명 관리 

- Amazon S3 계정에 인증하려면 [IAM 계정에 대한 액세스 키를](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)사용해야 합니다. 
- Azure Blob 저장소에 연결하기 위해 여러 인증 유형이 지원됩니다.  Azure [리소스에 대한 관리되는 ID를](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) 사용하는 것이 좋습니다.  또는 [서비스 보안 주체,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [공유 액세스 서명](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)또는 저장소 계정 [키를](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)사용하여 Azure Blob 저장소에 인증할 수 있습니다. 
- Azure Data Lake 저장소 Gen2에 연결하기 위해 여러 인증 유형도 지원됩니다.  [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) 또는 [저장소 계정 키를](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 사용할 수도 있지만 [Azure 리소스에 대해 관리되는 ID를](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) 사용하는 것이 좋습니다. 
- Azure 리소스에 대해 관리되는 ID를 사용하지 않는 경우 ADF 연결 서비스를 수정하지 않고도 중앙에서 키를 중앙에서 관리하고 회전할 수 있도록 [Azure Key Vault에 자격 증명을 저장하는](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 것이 좋습니다.  이는 [CI/CD의 모범 사례](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)중 하나이기도 합니다. 

### <a name="initial-snapshot-data-migration"></a>초기 스냅샷 데이터 마이그레이션 

특히 100TB 이상의 데이터를 마이그레이션할 때는 데이터 파티션을 권장합니다.  데이터를 분할하려면 '접두사' 설정을 활용하여 Amazon S3의 폴더와 파일을 이름으로 필터링한 다음 각 ADF 복사 작업은 한 번에 하나의 파티션을 복사할 수 있습니다.  더 나은 처리량을 위해 여러 ADF 복사 작업을 동시에 실행할 수 있습니다. 

네트워크 또는 데이터 저장소 일시적인 문제로 인해 복사 작업이 실패하면 실패한 복사 작업을 다시 실행하여 AWS S3에서 해당 특정 파티션을 다시 로드할 수 있습니다.  다른 파티션을 로드하는 다른 모든 복사 작업은 영향을 받지 않습니다. 

### <a name="delta-data-migration"></a>델타 데이터 마이그레이션 

AWS S3에서 새 파일이나 변경된 파일을 식별하는 가장 성능이 뛰어난 방법은 시간 분할된 명명 규칙을 사용하는 것입니다. 

또는 AWS S3의 데이터가 분할된 시간이 아닌 경우 ADF는 LastModifiedDate를 통해 새 파일이나 변경된 파일을 식별할 수 있습니다.   ADF가 AWS S3의 모든 파일을 스캔하고 마지막으로 수정된 타임스탬프가 특정 값보다 큰 새 파일과 업데이트된 파일만 복사하는 것이 작동 방식입니다.  S3에 많은 수의 파일이 있는 경우 필터 조건과 일치하는 파일 수에 관계없이 초기 파일 검색에 시간이 오래 걸릴 수 있습니다.  이 경우 파일 검색이 병렬로 이루어질 수 있도록 초기 스냅숏 마이그레이션에 동일한 '접두사' 설정을 사용하여 데이터를 먼저 분할하는 것이 좋습니다.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Azure VM에서 자체 호스팅된 통합 런타임이 필요한 시나리오의 경우 

개인 링크를 통해 데이터를 마이그레이션하든 Amazon S3 방화벽에서 특정 IP 범위를 허용하든 Azure Windows VM에 자체 호스팅 통합 런타임을 설치해야 합니다. 

- 각 Azure VM에 대해 시작하는 권장 구성은 32vCPU 및 128GB 메모리로 Standard_D32s_v3.  데이터 마이그레이션 중에 IR VM의 CPU 및 메모리 사용률을 계속 모니터링하여 성능을 높이기 위해 VM을 추가로 확장해야 하는지 또는 비용을 절감하기 위해 VM을 축소해야 하는지 확인할 수 있습니다. 
- 또한 단일 자체 호스팅 IR을 사용하여 최대 4개의 VM 노드를 연결하여 확장할 수도 있습니다.  자체 호스팅 IR에 대해 실행되는 단일 복사 작업은 파일 집합을 자동으로 분할하고 모든 VM 노드를 활용하여 파일을 병렬로 복사합니다.  고가용성의 경우 데이터 마이그레이션 중에 단일 실패 지점을 피하기 위해 2개의 VM 노드로 시작하는 것이 좋습니다. 

### <a name="rate-limiting"></a>속도 제한 

모범 사례로, 적절한 파티션 크기를 결정할 수 있도록 대표 샘플 데이터 집합을 사용하여 성능 POC를 수행합니다. 

기본 DIU 설정을 사용 하 고 단일 파티션 및 단일 복사 활동으로 시작 합니다.  데이터 저장소의 네트워크 대역폭 제한 또는 IOPS/대역폭 제한에 도달하거나 단일 복사 작업에서 허용되는 최대 256DIU에 도달할 때까지 DIU 설정을 점진적으로 늘립니다. 

그런 다음 환경의 한계에 도달할 때까지 동시 복사 활동 수를 점진적으로 늘립니다. 

ADF 복사 활동에 의해 보고된 제한 오류가 발생하면 ADF에서 동시성 또는 DIU 설정을 줄이거나 네트워크 및 데이터 저장소의 대역폭/IOPS 제한을 늘리는 것이 좋습니다.  

### <a name="estimating-price"></a>가격 예측 

> [!NOTE]
> 이는 가상의 가격 책정 예제입니다.  실제 가격은 사용자 환경의 실제 처리량에 따라 다릅니다.

S3에서 Azure Blob Storage로 데이터를 마이그레이션하기 위해 구성된 다음 파이프라인을 고려하십시오. 

![가격 책정 파이프라인](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

우리는 다음과 같은 가정하자 : 

- 총 데이터 볼륨은 2 PB입니다. 
- 첫 번째 솔루션 아키텍처를 사용하여 HTTPS를 통해 데이터 마이그레이션 
- 2 PB는 1K 파티션으로 나누며 각 복사본은 하나의 파티션을 이동합니다. 
- 각 복사본은 DIU=256으로 구성되며 1GBps 처리량을 달성합니다. 
- ForEach 동시성은 2로 설정되고 집계 처리량은 2GBps입니다. 
- 마이그레이션을 완료하는 데 총 292시간이 걸립니다. 

위의 가정을 기반으로 한 예상 가격은 다음과 같습니다. 

![가격 표](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>추가 참조 
- [아마존 간단한 스토리지 서비스 커넥터](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob Storage 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [복사 활동 성능 튜닝 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [자체 호스팅 통합 런타임 생성 및 구성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [자체 호스팅 통합 런타임 HA 및 확장성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [데이터 이동 보안 고려 사항](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure 키 자격 증명에 자격 증명 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [시간 분할 된 파일 이름에 따라 증분 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [LastModifiedDate를 기반으로 새 파일 및 변경된 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>템플릿

다음은 Amazon S3에서 Azure Data Lake Storage Gen2로 수억 개의 파일로 구성된 페타바이트 규모의 데이터를 마이그레이션하는 것으로 시작하는 [템플릿입니다.](solution-template-migration-s3-azure.md)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)
