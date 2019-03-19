---
title: Azure virtual machines의 메인프레임 재호스팅 | Microsoft Docs
description: Microsoft Azure에서 virtual machines (Vm)를 사용 하는 IBM Z 기반 시스템과 같은 메인프레임 워크 로드를 다시 호스트 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192720"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure virtual machines의 재호스팅 메인프레임

워크 로드 마이그레이션 메인프레임에서 환경을 클라우드로 인프라 현대화 하 고 종종 비용을 절감할 수 있습니다. 대부분의 워크로드는 데이터베이스 이름을 업데이트하는 등 코드를 약간만 변경하면 Azure로 전송할 수 있습니다.

용어 *메인프레임* 대규모 컴퓨터 시스템에 일반적으로 참조 대부분 현재 배포 된 IBM 시스템 Z 서버나 MVS, DOS, VSE, os/390 또는 z/OS를 실행 하는 IBM plug-compatible 시스템도 합니다.

Azure 가상 머신 (VM)를 분리 하 여 단일 인스턴스에서 특정 응용 프로그램에 대 한 리소스 관리에 사용 됩니다. 메인프레임 IBM z/OS 등이 목적을 위해 논리 파티션 (LPARS)를 사용합니다. 메인프레임을 IBM Db2 데이터베이스에 대 한 연결 된 COBOL 프로그램과 CICS 지역에 대 한 하나의 LPAR 및 별도 LPAR를 사용할 수 있습니다. 일반적인 [Azure에서 n 계층 응용 프로그램](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) 각 계층에 대 한 서브넷으로 분할할 수 있습니다 가상 네트워크에 Azure Vm을 배포 합니다.

Azure Vm 메인프레임에 에뮬레이션 환경 및 리프트 앤 시프트 시나리오를 지 원하는 컴파일러를 실행할 수 있습니다. 개발 및 테스트 Azure 개발/테스트 환경에는 메인프레임 마이그레이션할 첫 번째 워크 로드 간에 자주 않습니다. 에뮬레이트할 수 있는 일반적인 서버 구성 요소는 다음 그림과 같이 온라인 트랜잭션 처리 (OLTP), batch 및 데이터 수집 시스템을 포함 합니다.

![Azure 에뮬레이션 환경 z/OS 기반 시스템을 실행할 수 있습니다.](media/01-overview.png)

파트너 솔루션을 사용 하 여 Azure에서 다시 호스트 된 다른 동안 일부 메인프레임 작업을 비교적 쉽게 Azure로 마이그레이션할 수 있습니다. 파트너 솔루션을 선택 하는 방법에 대 한 자세한 지침은 합니다 [Azure 메인프레임 마이그레이션 센터](https://azure.microsoft.com/migration/mainframe/) 수 있습니다.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Micro Focus 재호스팅 플랫폼을 사용 하는 개발/테스트 환경 설정

Micro Focus Enterprise Server에 사용할 수 있는 플랫폼을 다시 호스팅할 가장 큰 메인프레임 중 하나입니다. 사용 하 여 저렴 x86 z/OS 워크 로드를 실행할 Azure 플랫폼입니다.

시작 하려면 다음 문서를 참조 합니다.

- [Azure에서 Micro Focus Enterprise Server 4.0 및 엔터프라이즈 개발자 4.0 설치](./microfocus/set-up-micro-focus-on-azure.md)
- [Azure에서 마이크로 포커스 엔터프라이즈 개발자 4.0에 대 한 마이크로 포커스 CICS BankDemo 설정](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>Azure에서 TmaxSoft OpenFrame

TmaxSoft OpenFrame 손쉽게 기존 메인프레임 자산을 Azure로 전환 하는 솔루션을 다시 호스팅할 널리 사용 되는 메인프레임입니다. Azure에서 OpenFrame 환경을 개발, 데모, 테스트 또는 프로덕션 워크 로드에 적합 합니다.

시작 하려면 다운로드 합니다 [Azure에서 TmaxSoft OpenFrame 설치](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) 전자책 합니다.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>IBM Z 개발/테스트 12.0을 사용 하 여 개발/테스트 환경 설정

IBM Z 개발 및 테스트 환경 (IBM zD & T) 개발, 테스트 및 데모 z/OS 기반 응용 프로그램에 사용할 수 있는 Azure에서 비-프로덕션 환경을 설정 합니다.

Azure 에뮬레이션 환경 다양 한 응용 프로그램 개발자가 제어 배포 (ADCDs)를 통해 Z 인스턴스를 호스팅할 수 있습니다. Azure 및 Azure Stack에서 zD T Personal Edition, zD T 병렬 Sysplex, 및 zD & T Enterprise Edition을 실행할 수 있습니다.

시작 하려면 다음 문서를 참조 합니다.

-   [IBM zD Azure에서 T 12.0 설정](./ibm/install-ibm-z-environment.md)
-   [ZD (& T)에서 ADCD 설정](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>IBM DB2 pureScale를 Azure로 마이그레이션

IBM DB2 pureScale 환경은 Linux 운영 체제에서 Azure용 데이터베이스 클러스터에 고가용성 및 확장성을 제공합니다. 원본 환경과 동일하지 않더라도, Linux의 IBM DB2 pureScale은 메인프레임의 병렬 Sysplex 구성에서 실행되는 z/OS용 IBM DB2와 비슷한 고가용성 및 확장성 기능을 제공합니다.

시작 하려면 참조 [Azure에서 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)합니다.

## <a name="considerations"></a>고려 사항

서비스 (IaaS)로 Azure 인프라에 메인프레임 워크 로드를 마이그레이션하는 경우에 Azure Vm을 포함 하 여 주문형, 확장 가능한 컴퓨팅 리소스의 여러 형식에서 선택할 수 있습니다. Azure의 범위를 제공 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) 하 고 [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) Vm.

### <a name="high-availability-and-failover"></a>고가용성 및 장애 조치(failover)

Azure 약정 기반 서비스 수준 계약 (Sla)을 여기서 제공 여러 9 가용성은 서비스의 로컬 또는 지역 기반 복제를 사용 하 여 액세스에 최적화 된 기본값입니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

Vm과 같은 Azure IaaS에 대 한 장애 조치 인스턴스를 클러스터링 하는 장애 조치 등의 특정 시스템 기능에 의존 하 고 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets)합니다. 사용 하는 경우 Azure 플랫폼 서비스 (PaaS) 리소스로 같은 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) 하 고 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), 플랫폼에서는 장애 조치를 자동으로 처리 합니다.

### <a name="scalability"></a>확장성

메인프레임 클라우드 환경 확장 하는 동안, 일반적으로 확장 합니다. Azure의 범위를 제공 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 하 고 [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 요구 사항에 맞게 크기입니다. 클라우드를 위나 아래로 일치 정확한 사용자 사양에도 확장 됩니다. 전원, 저장소 및 서비스 계산 [확장](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) 사용량 기반 청구 모델에서 주문형입니다.

### <a name="storage"></a>Storage

클라우드에서 유연 하 고 확장 가능한 저장소 옵션의 범위 있고 필요한 것에 대해서만 비용을 지불 합니다. [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction)는 데이터 개체용으로 대폭 확장 가능한 개체 저장소, 클라우드용 파일 시스템 서비스, 안정적인 메시징 저장소 및 NoSQL 저장소를 제공합니다. VM의 경우에는 관리형 디스크와 비관리형 디스크에서 안전한 영구 디스크 스토리지가 제공됩니다.

### <a name="backup-and-recovery"></a>Backup 및 복구

고유한 재해 복구 사이트 유지 관리 하는 비용이 많이 드는 제안일 수 있습니다. Azure에 구현 하기 쉽게 하 고 비용 효율적인 옵션에 대 한 [백업](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)를 [복구](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), 및 [중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 또는 지역 수준에서 또는 지리적 중복을 통해.

## <a name="azure-government-for-mainframe-migrations"></a>메인프레임 마이그레이션에 대 한 azure Government

공공 부문 엔터티 보다 현대적이 고 유연한 플랫폼을 메인프레임 응용 프로그램을 이동할 환영 합니다. Microsoft Azure Government cloud services 글로벌 Microsoft Azure 플랫폼을 기반으로 하지만 연방, 주 및 지방 정부 시스템에 대 한 패키지의 인스턴스를 물리적으로 분리 된 경우 세계적 수준의 보안, 보호 및 규정 준수 서비스 미국 정부 기관 및 파트너를 위한 구체적으로 제공합니다.

Azure Government 이러한 유형의 환경 해야 하는 시스템에 대 한 FedRAMP 영향에 대 한는 Provisional Authority to Operate (P-ATO)를 획득 합니다. 

시작 하려면 다운로드 [메인프레임 응용 프로그램에 대 한 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)합니다.

## <a name="learn-more"></a>자세한 정보

광범위 한 메인프레임 마이그레이션을 고려 하는 경우 [파트너](partner-workloads.md) 에코 시스템은 하는 데 사용할 수 있습니다. 파트너 솔루션 선택과 관련한 상세 지침은 [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx)를 참조하세요.

참고 항목:

-   [메인프레임 마이그레이션](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Azure로 마이그레이션 메인프레임 익히기](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
