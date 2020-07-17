---
title: Azure virtual machines의 메인프레임 재호스팅
description: Microsoft Azure에서 Vm (가상 머신)을 사용 하 여 IBM Z 기반 시스템과 같은 메인프레임 워크 로드를 Rehost.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76289801"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure virtual machines의 메인프레임 재호스팅

메인프레임 환경에서 클라우드로 워크 로드를 마이그레이션하면 인프라를 현대화 하 고 비용을 절감할 수 있습니다. 대부분의 워크로드는 데이터베이스 이름을 업데이트하는 등 코드를 약간만 변경하면 Azure로 전송할 수 있습니다.

일반적으로 *메인프레임* 이라는 용어는 대형 컴퓨터 시스템을 의미 합니다. 특히 현재 사용 중인 대다수는 IBM System Z 서버 또는 MVS, DOS, VSE, OS/390 또는 Z/OS를 실행 하는 IBM 플러그 호환 시스템입니다.

Azure VM (가상 머신)은 단일 인스턴스의 특정 응용 프로그램에 대 한 리소스를 격리 하 고 관리 하는 데 사용 됩니다. IBM z/OS와 같은 메인프레임은이 용도로 LPARS (논리 파티션)를 사용 합니다. 메인프레임은 연결 된 COBOL 프로그램을 사용 하는 CICS 지역 및 IBM Db2 데이터베이스에 대 한 별도의 lpar을 사용할 수 있습니다. [Azure의 일반적인 n 계층 응용 프로그램](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) 은 azure vm을 각 계층에 대 한 서브넷으로 나눌 수 있는 가상 네트워크에 배포 합니다.

Azure Vm은 리프트 앤 시프트 시나리오를 지 원하는 메인프레임 에뮬레이션 환경 및 컴파일러를 실행할 수 있습니다. 개발 및 테스트는 주로 메인프레임에서 Azure 개발/테스트 환경으로 마이그레이션하는 첫 번째 워크 로드 중 하나입니다. 에뮬레이션할 수 있는 일반 서버 구성 요소에는 다음 그림과 같이 OLTP (온라인 트랜잭션 프로세스), 일괄 처리 및 데이터 수집 시스템이 포함 됩니다.

![Azure의 에뮬레이션 환경을 사용 하면 z/OS 기반 시스템을 실행할 수 있습니다.](media/01-overview.png)

일부 메인프레임 워크 로드는 상대적으로 쉽게 Azure로 마이그레이션할 수 있는 반면, 다른 일부 작업은 파트너 솔루션을 사용 하 여 Azure에서 다시 호스팅할 수 있습니다. 파트너 솔루션을 선택 하는 방법에 대 한 자세한 지침은 [Azure 메인프레임 마이그레이션 센터가](https://azure.microsoft.com/migration/mainframe/) 도움이 될 수 있습니다.

## <a name="mainframe-migration"></a>메인프레임 마이그레이션

Rehost, rebuild, replace 또는 사용 중지 하 시겠습니까? IaaS 또는 PaaS? 메인프레임 응용 프로그램에 적합 한 마이그레이션 전략을 결정 하려면 Azure 아키텍처 센터의 [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) 가이드를 참조 하세요.

## <a name="micro-focus-rehosting-platform"></a>마이크로 포커스 재호스팅 플랫폼

마이크로 Focus Enterprise Server는 사용할 수 있는 가장 큰 메인프레임 재호스팅 플랫폼 중 하나입니다. Azure에서 더 저렴 한 x86 플랫폼에서 z/OS 워크 로드를 실행 하는 데 사용할 수 있습니다.

시작하기:

- [Azure에 Enterprise Server 및 Enterprise Developer 설치](./microfocus/set-up-micro-focus-azure.md)
- [Azure에서 엔터프라이즈 개발자를 위한 CICS BankDemo 설정](./microfocus/demo.md)
- [Azure의 Docker 컨테이너에서 엔터프라이즈 서버 실행](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure의 TmaxSoft OpenFrame

TmaxSoft OpenFrame은 리프트 앤 시프트 시나리오에서 사용 되는 인기 있는 메인프레임 재호스팅 솔루션입니다. Azure의 OpenFrame 환경은 개발, 데모, 테스트 또는 프로덕션 워크 로드에 적합 합니다.

시작하기:

- [TmaxSoft OpenFrame 시작](./tmaxsoft/get-started.md)
- [전자책 다운로드](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

Ibm Z 개발 및 테스트 환경 (IBM zD&T)은 Z/OS 기반 응용 프로그램의 개발, 테스트 및 데모에 사용할 수 있는 비프로덕션 환경을 Azure에 설정 합니다.

Azure의 에뮬레이션 환경에서는 ADCDs (응용 프로그램 개발자 제어 배포)를 통해 다양 한 종류의 Z 인스턴스를 호스트할 수 있습니다. Azure에서 zD&T Personal Edition, zD&T Parallel Sysplex 및 zD&T Enterprise Edition을 실행 하 고 Azure Stack 수 있습니다.

시작하기:

- [Azure에서 IBM zD&T 12.0 설정](./ibm/install-ibm-z-environment.md)
- [ZD&T에서 ADCD를 설정 합니다.](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure의 IBM DB2 pureScale

IBM DB2 pureScale 환경은 Azure에 대 한 데이터베이스 클러스터를 제공 합니다. 원래 환경과 동일 하지는 않지만 병렬 Sysplex 설정에서 실행 되는 z/OS 용 IBM d b 2로 유사한 가용성 및 규모를 제공 합니다.

시작 하려면 [Azure에서 IBM DB2 pureScale](/azure/virtual-machines/linux/ibm-db2-purescale-azure)를 참조 하세요.

## <a name="considerations"></a>고려 사항

메인프레임 워크 로드를 Azure IaaS (infrastructure as a service)로 마이그레이션하는 경우 Azure Vm을 포함 하 여 여러 유형의 주문형 확장 컴퓨팅 리소스에서 선택할 수 있습니다. Azure는 다양 한 [Linux](/azure/virtual-machines/linux/overview) 및 [Windows](/azure/virtual-machines/windows/overview) vm을 제공 합니다.

### <a name="compute"></a>Compute

Azure 계산 전원은 잘를 메인프레임 용량과 비교 합니다. 메인프레임 워크 로드를 Azure로 이동 하는 것을 고려 하는 경우 100만 명령 (MIPS)의 메인프레임 메트릭을 가상 Cpu와 비교 합니다. 

[메인프레임 계산을 Azure로 이동](./concepts/mainframe-compute-azure.md)하는 방법을 알아봅니다.

### <a name="high-availability-and-failover"></a>고가용성 및 장애 조치(failover)

Azure는 약정 기반 Sla (서비스 수준 계약)를 제공 합니다. 다중 9 가용성은 기본값이 며 Sla는 로컬 또는 지역 기반 서비스 복제를 사용 하 여 최적화할 수 있습니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

VM과 같은 Azure IaaS를 사용 하 여 특정 시스템 함수는 장애 조치 (failover) 클러스터링 인스턴스 및 가용성 집합과 같은 장애 조치 (failover) 지원을 제공 합니다. Azure PaaS (platform as a service) 리소스를 사용 하는 경우 플랫폼은 장애 조치 (failover)를 자동으로 처리 합니다. 예를 들어 [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) 및 [Azure Cosmos DB](/azure/cosmos-db/introduction)있습니다.

### <a name="scalability"></a>확장성

일반적으로 메인프레임은 규모를 강화 하는 반면, 클라우드 환경 규모는 확장 됩니다. Azure는 요구 사항에 맞는 다양 한 [Linux](/azure/virtual-machines/linux/sizes) 및 [Windows](/azure/virtual-machines/windows/sizes) 크기를 제공 합니다. 또한 클라우드는 정확한 사용자 사양과 일치 하도록 확장 하거나 축소 합니다. 사용량 기반 청구 모델에서 요청 시 전원, 저장소 및 서비스의 [규모](/azure/architecture/best-practices/auto-scaling) 를 계산 합니다.

### <a name="storage"></a>스토리지

클라우드에서는 유연 하 고 확장 가능한 저장소 옵션을 사용할 수 있으며 필요한 만큼만 요금을 지불 하면 됩니다. [Azure Storage](/azure/storage/common/storage-introduction)는 데이터 개체용으로 대폭 확장 가능한 개체 저장소, 클라우드용 파일 시스템 서비스, 안정적인 메시징 저장소 및 NoSQL 저장소를 제공합니다. VM의 경우에는 관리형 디스크와 비관리형 디스크에서 안전한 영구 디스크 스토리지가 제공됩니다.

[메인프레임 저장소를 Azure로 이동](./concepts/mainframe-storage-azure.md)하는 방법을 알아봅니다.

### <a name="backup-and-recovery"></a>Backup 및 복구

자체 재해 복구 사이트를 유지 관리 하는 것은 비용이 많이 들 수 있습니다. Azure에는 로컬 또는 지역 수준에서 또는 지역 중복을 통해 [백업](/azure/backup/backup-introduction-to-azure-backup), [복구](/azure/site-recovery/site-recovery-overview)및 [중복성](/azure/storage/common/storage-redundancy) 을 위한 구현 하기 쉽고 비용 효율적인 옵션이 있습니다.

## <a name="azure-government-for-mainframe-migrations"></a>메인프레임 마이그레이션에 대 한 Azure Government

많은 공용 섹터 엔터티는 메인프레임 응용 프로그램을 보다 최신의 유연한 플랫폼으로 이동 하는 것을 선호 합니다. Microsoft Azure Government는 연방, 주 및 지방 정부 시스템용으로 패키지 된 글로벌 Microsoft Azure 플랫폼의 물리적으로 분리 된 인스턴스입니다. 미국 정부 기관 및 해당 파트너를 위한 세계적인 수준의 보안, 보호 및 규정 준수 서비스를 제공 합니다.

이러한 유형의 환경을 필요로 하는 시스템에 대해 FedRAMP 높은 영향을 Provisional 하는 작업 (P-ATO)에 대 한 작업을 수행할 수 있습니다. Azure Government

시작 하려면 [메인프레임 응용 프로그램에 대 한 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)를 다운로드 하세요.

## <a name="next-steps"></a>다음 단계

파트너 응용 프로그램을 마이그레이션하거나 rehost 하는 데 도움이 되도록 [파트너](partner-workloads.md) 에 게 문의 하세요. 

참고 항목:

- [메인프레임 항목에 대 한 백서](mainframe-white-papers.md)
- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [문제 해결](/azure/virtual-machines/troubleshooting/)
- [전문가가 제공 자세히 메인프레임에서 Azure로 마이그레이션](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
