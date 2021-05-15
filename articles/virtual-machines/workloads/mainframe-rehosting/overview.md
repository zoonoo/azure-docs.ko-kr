---
title: Azure 가상 머신의 메인프레임 재호스팅
description: Microsoft Azure에서 VM(가상 머신)을 사용하여 IBM Z 기반 시스템과 같은 메인프레임 워크로드를 재호스팅.
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.openlocfilehash: 1baba0afc08c6a4df63c38cafe9231411ecf8f1f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048853"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure 가상 머신의 메인프레임 재호스팅

메인프레임 환경에서 클라우드로 워크로드를 마이그레이션하면 인프라를 현대화하고 비용을 절감할 수 있습니다. 대부분의 워크로드는 데이터베이스 이름을 업데이트하는 등 코드를 약간만 변경하면 Azure로 전송할 수 있습니다.

일반적으로 *메인프레임* 이라는 용어는 대형 컴퓨터 시스템을 의미합니다. 특히 현재 사용 중인 대다수는 IBM System Z 서버 또는 MVS, DOS, VSE, OS/390 또는 Z/OS를 실행하는 IBM 플러그 호환 시스템입니다.

Azure VM(가상 머신)은 단일 인스턴스의 특정 애플리케이션에 대한 리소스를 격리하고 관리하는 데 사용됩니다. IBM z/OS와 같은 메인프레임은 이 용도로 LPARS(논리 파티션)를 사용합니다. 예를 들어 메인프레임은 COBOL 프로그램이 연결된 CICS 영역에 대한 LPAR과 IBM Db2 데이터베이스 용으로 각기 별도의 LPAR(논리 파티션)을 사용할 수 있습니다. [Azure의 일반적인 n 계층 애플리케이션](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) 은 Azure VM을 각 계층에 대한 서브넷으로 나눌 수 있는 가상 네트워크에 배포합니다.

Azure VM은 리프트 앤 시프트 시나리오를 지원하는 메인프레임 에뮬레이션 환경 및 컴파일러를 실행할 수 있습니다. 개발 및 테스트는 주로 메인프레임에서 Azure 개발/테스트 환경으로 마이그레이션하는 첫 번째 워크로드 중 하나입니다. 에뮬레이트할 수 있는 일반적인 서버 구성 요소로는 다음 그림에서 나타내는 것과 같은 OLTP(온라인 트랜잭션 처리), 일괄 처리, 데이터 수집 시스템 등이 있습니다.

![Azure의 에뮬레이션 환경을 사용하면 z/OS 기반 시스템을 실행할 수 있습니다.](media/01-overview.png)

일부 메인프레임 워크로드는 상대적으로 쉽게 Azure로 마이그레이션할 수 있는 반면, 다른 일부 작업은 파트너 솔루션을 사용하여 Azure에서 다시 호스팅할 수 있습니다. 파트너 솔루션을 선택하는 방법에 대한 자세한 지침은 [Azure 메인프레임 마이그레이션 센터가](https://azure.microsoft.com/migration/mainframe/) 도움이 될 수 있습니다.

## <a name="mainframe-migration"></a>메인프레임 마이그레이션

다시 호스트, 다시 빌드, 바꾸기 또는 사용 중지하시겠습니까? IaaS 또는 PaaS? 메인프레임 애플리케이션에 적합한 마이그레이션 전략을 결정하려면 Azure 아키텍처 센터의 [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) 가이드를 참조하세요.

## <a name="micro-focus-rehosting-platform"></a>Micro Focus 재호스팅 플랫폼

Micro Focus Enterprise Server는 사용할 수 있는 가장 큰 메인프레임 재호스팅 플랫폼 중 하나입니다. Azure에서 더 저렴한 x86 플랫폼에서 z/OS 워크로드를 실행하는 데 사용할 수 있습니다.

시작하기:

- [Azure에 Enterprise Server 및 Enterprise Developer 설치](./microfocus/set-up-micro-focus-azure.md)
- [Azure에서 Enterprise Developer를 위한 CICS BankDemo 설정](./microfocus/demo.md)
- [Azure의 Docker 컨테이너에서 Enterprise Server 실행](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure의 TmaxSoft OpenFrame

TmaxSoft OpenFrame은 리프트 앤 시프트 시나리오에서 사용되는 인기있는 메인프레임 재호스팅 솔루션입니다. Azure의 OpenFrame 환경은 개발, 데모, 테스트 또는 프로덕션 워크로드에 적합합니다.

시작하기:

- [TmaxSoft OpenFrame 시작](./tmaxsoft/get-started.md)
- [전자책을 다운로드합니다](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z 개발 및 테스트 환경(IBM zD&T)은 Z/OS 기반 애플리케이션의 개발, 테스트 및 데모에 사용할 수 있는 비프로덕션 환경을 Azure에 설정합니다.

Azure의 에뮬레이션 환경에서는 ADCDs(애플리케이션 개발자 제어 배포)를 통해 다양한 종류의 Z 인스턴스를 호스트할 수 있습니다. Azure 및 Azure Stack에서 zD&T Personal Edition, zD&T Parallel Sysplex 및 zD&T Enterprise Edition을 실행할 수 있습니다.

시작하기:

- [Azure에서 IBM zD&T 12.0 설정](./ibm/install-ibm-z-environment.md)
- [zD&T에서 ADCD 설정](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure의 IBM DB2 pureScale

IBM DB2 pureScale은 Azure 환경을 위한 데이터베이스 클러스터를 제공합니다. 원래 환경과 동일하지는 않지만 병렬 Sysplex 설정에서 실행되는 z/OS 용 IBM DB2로 유사한 가용성 및 규모를 제공합니다.

시작하려면 [Azure에서 IBM DB2 pureScale](.//ibm/ibm-db2-purescale-azure.md)를 참조하세요.

## <a name="considerations"></a>고려 사항

메인프레임 워크로드를 Azure IaaS(서비스 제공 인프라)로 마이그레이션하는 경우 Azure VM을 포함하여 여러 유형의 주문형 확장 가능한 컴퓨팅 리소스에서 선택할 수 있습니다. Azure는 일정 범위의 [Linux](../../linux/overview.md) 및 [Windows](../../windows/overview.md) VM을 제공합니다.

### <a name="compute"></a>컴퓨팅

Azure 컴퓨팅 능력은 메인프레임 용량과 비교해 보면 쉽게 이해할 수 있습니다. 메인프레임 워크로드를 Azure로 이동하는 것을 고려하는 경우 MIPS(초당 100만 지침)의 메인프레임 메트릭을 가상 CPU와 비교합니다. 

[메인프레임 컴퓨팅을 Azure로 이동](./concepts/mainframe-compute-azure.md)하는 방법을 알아봅니다.

### <a name="high-availability-and-failover"></a>고가용성 및 장애 조치(failover)

Azure는 약정 기반 SLA(서비스 수준 계약)를 제공합니다. 다중 9 가용성은 기본값이며 SLA는 로컬 또는 지역 기반 서비스 복제를 사용하여 최적화할 수 있습니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

VM과 같은 Azure IaaS를 사용하여 특정 시스템 함수는 장애 조치(failover) 클러스터링 인스턴스 및 가용성 집합과 같은 장애 조치(failover) 지원을 제공합니다. Azure PaaS(platform as a service) 리소스를 사용하는 경우 플랫폼은 장애 조치(failover)를 자동으로 처리합니다. 예를 들어 [Azure SQL Database](../../../azure-sql/database/sql-database-paas-overview.md) 및 [Azure Cosmos DB](../../../cosmos-db/introduction.md)가 있습니다.

### <a name="scalability"></a>확장성

일반적으로 메인프레임은 스케일 업하는 반면, 클라우드 환경 규모는 스케일 아웃됩니다. Azure는 요구 사항에 맞는 다양한 [Linux](../../sizes.md) 및 [Windows](../../sizes.md) 크기를 제공합니다. 또한 클라우드는 정확한 사용자 사양과 일치하도록 스케일 업하거나 스케일 다운합니다. 사용량 기반 청구 모델에서 요청 시 컴퓨팅 능력, 스토리지 및 서비스의 [규모](/azure/architecture/best-practices/auto-scaling)를 계산합니다.

### <a name="storage"></a>스토리지

클라우드에서는 유연하고 확장 가능한 스토리지 옵션을 사용할 수 있으며 필요한 만큼만 요금을 지불하면 됩니다. [Azure Storage](../../../storage/common/storage-introduction.md)는 데이터 개체용으로 대폭 확장 가능한 개체 저장소, 클라우드용 파일 시스템 서비스, 안정적인 메시징 저장소 및 NoSQL 저장소를 제공합니다. VM의 경우에는 관리형 디스크와 비관리형 디스크에서 안전한 영구 디스크 스토리지가 제공됩니다.

[메인프레임 스토리지를 Azure로 이동](./concepts/mainframe-storage-azure.md)하는 방법을 알아봅니다.

### <a name="backup-and-recovery"></a>Backup 및 복구

자체 재해 복구 사이트를 유지 관리하는 것은 비용이 많이 들 수 있습니다. Azure에는 쉽게 구현할 수 있는 경제적인 [백업](../../../backup/backup-overview.md), [복구](../../../site-recovery/site-recovery-overview.md) 및 [중복도](../../../storage/common/storage-redundancy.md) 옵션이 포함되어 있습니다. 로컬/지역 수준에서 또는 지리적 중복 기능을 통해 이러한 옵션을 사용할 수 있습니다.

## <a name="azure-government-for-mainframe-migrations"></a>메인프레임 마이그레이션에 대한 Azure Government

많은 공용 부문 엔터티는 메인프레임 애플리케이션을 보다 최신의 유연한 플랫폼으로 이동하는 것을 선호합니다. Microsoft Azure Government는 연방, 주 및 지방 정부 시스템용으로 패키지된 글로벌 Microsoft Azure 플랫폼의 물리적으로 분리된 인스턴스입니다. 미국 정부 기관 및 해당 파트너를 위한 세계적인 수준의 전용 보안, 보호 및 규정 준수 서비스를 제공합니다.

Azure Government는 이러한 유형의 환경을 필요로 하는 시스템에 대해 FedRAMP 높은 영향 작업을 수행하는 Provisional 권한(P-ATO)을 가집니다.

시작하려면 [메인프레임 애플리케이션에 대한 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)를 다운로드하세요.

## <a name="next-steps"></a>다음 단계

파트너 애플리케이션을 마이그레이션하거나 다시 호스트하는 데 도움이 되도록 [파트너](partner-workloads.md)에게 문의하세요. 

참고 항목:

- [메인프레임 토픽에 대한 백서](mainframe-white-papers.md)
- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [문제 해결](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [메인프레임에서 Azure로 마이그레이션 쉽게 설명하기](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md