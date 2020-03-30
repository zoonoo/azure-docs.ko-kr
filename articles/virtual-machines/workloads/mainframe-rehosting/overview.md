---
title: Azure 가상 컴퓨터에서 메인프레임 다시 호스팅
description: Microsoft Azure에서 가상 시스템(VM)을 사용하여 IBM Z 기반 시스템과 같은 메인프레임 워크로드를 다시 호스팅합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289801"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure 가상 컴퓨터에서 메인프레임 다시 호스팅

메인프레임 환경에서 클라우드로 워크로드를 마이그레이션하면 인프라를 현대화하고 비용을 절감할 수 있습니다. 대부분의 워크로드는 데이터베이스 이름을 업데이트하는 등 코드를 약간만 변경하면 Azure로 전송할 수 있습니다.

일반적으로 *메인프레임이라는* 용어는 대형 컴퓨터 시스템을 의미한다. 특히 현재 사용 중인 대부분은 MVS, DOS, VSE, OS/390 또는 z/OS를 실행하는 IBM System Z 서버 또는 IBM 플러그 호환 시스템입니다.

Azure 가상 시스템(VM)은 단일 인스턴스에서 특정 응용 프로그램의 리소스를 격리하고 관리하는 데 사용됩니다. IBM z/OS와 같은 메인프레임은 이를 위해 논리 파티션(LPARS)을 사용합니다. 메인프레임은 연결된 코볼 프로그램이 있는 CICS 영역에 하나의 LPAR을 사용하고 IBM Db2 데이터베이스에 대해 별도의 LPAR를 사용할 수 있습니다. [Azure의](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) 일반적인 n 계층 응용 프로그램은 Azure VM을 가상 네트워크에 배포하여 각 계층의 서브넷으로 분할할 수 있습니다.

Azure VM은 리프트 앤 시프트 시나리오를 지원하는 메인프레임 에뮬레이션 환경 및 컴파일러를 실행할 수 있습니다. 개발 및 테스트는 종종 메인프레임에서 Azure 개발/테스트 환경으로 마이그레이션하는 첫 번째 워크로드 중 하나입니다. 에뮬레이트할 수 있는 일반적인 서버 구성 요소에는 다음 그림과 같이 온라인 트랜잭션 프로세스(OLTP), 일괄 처리 및 데이터 수집 시스템이 포함됩니다.

![Azure의 에뮬레이션 환경을 사용하면 z/OS 기반 시스템을 실행할 수 있습니다.](media/01-overview.png)

일부 메인프레임 워크로드는 비교적 쉽게 Azure로 마이그레이션할 수 있으며 다른 워크로드는 파트너 솔루션을 사용하여 Azure에서 다시 호스트할 수 있습니다. 파트너 솔루션 선택에 대한 자세한 지침은 [Azure Mainframe 마이그레이션 센터에서](https://azure.microsoft.com/migration/mainframe/) 도움을 줄 수 있습니다.

## <a name="mainframe-migration"></a>메인프레임 마이그레이션

다시 호스트, 다시 빌드, 교체, 또는 은퇴? IaaS 또는 PaaS? 메인프레임 응용 프로그램에 적합한 마이그레이션 전략을 결정하려면 Azure 아키텍처 센터의 [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) 가이드를 참조하세요.

## <a name="micro-focus-rehosting-platform"></a>마이크로 포커스 리호스팅 플랫폼

마이크로 포커스 엔터프라이즈 서버는 사용 가능한 가장 큰 메인 프레임 리호스팅 플랫폼 중 하나입니다. Azure에서 저렴한 x86 플랫폼에서 z/OS 워크로드를 실행하는 데 사용할 수 있습니다.

시작하기:

- [Azure에 엔터프라이즈 서버 및 엔터프라이즈 개발자 설치](./microfocus/set-up-micro-focus-azure.md)
- [Azure에서 엔터프라이즈 개발자를 위한 CICS BankDemo 설정](./microfocus/demo.md)
- [Azure에서 Docker 컨테이너에서 엔터프라이즈 서버 실행](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure에서 TmaxSoft 오픈프레임

TmaxSoft OpenFrame은 리프트 앤 시프트 시나리오에 사용되는 인기있는 메인프레임 리호스팅 솔루션입니다. Azure의 OpenFrame 환경은 개발, 데모, 테스트 또는 프로덕션 워크로드에 적합합니다.

시작하기:

- [티맥스소프트 오픈프레임 시작](./tmaxsoft/get-started.md)
- [전자책 다운로드](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z 개발 및 테스트 환경(IBM zD&T)은 z/OS 기반 응용 프로그램의 개발, 테스트 및 데모에 사용할 수 있는 Azure에서 비프로덕션 환경을 설정합니다.

Azure의 에뮬레이션 환경은 응용 프로그램 개발자 제어 배포(ADC)를 통해 다양한 종류의 Z 인스턴스를 호스트할 수 있습니다. azure 및 Azure 스택에서 zD&T 개인 버전, zD&T 병렬 Sysplex 및 zD&T 엔터프라이즈 에디션을 실행할 수 있습니다.

시작하기:

- [Azure에서 T&IBM zD 설정](./ibm/install-ibm-z-environment.md)
- [zD&T에 ADCD 설정](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure의 IBM DB2 pureScale

IBM DB2 pureScale 환경은 Azure용 데이터베이스 클러스터를 제공합니다. 원래 환경과 동일하지는 않지만 병렬 Sysplex 설정에서 실행되는 z/OS에 대해 IBM DB2와 유사한 가용성및 규모를 제공합니다.

시작하려면 [Azure에서 IBM DB2 pureScale](/azure/virtual-machines/linux/ibm-db2-purescale-azure)을 참조하십시오.

## <a name="considerations"></a>고려 사항

메인프레임 워크로드를 서비스(IaaS)로 Azure 인프라로 마이그레이션할 때 Azure VM을 비롯한 여러 유형의 온디맨드 확장 가능한 컴퓨팅 리소스 중에서 선택할 수 있습니다. Azure는 다양한 [Linux](/azure/virtual-machines/linux/overview) 및 [Windows](/azure/virtual-machines/windows/overview) VM을 제공합니다.

### <a name="compute"></a>컴퓨팅

Azure 컴퓨팅 성능은 메인프레임의 용량과 유리하게 비교됩니다. 메인프레임 워크로드를 Azure로 이동하려는 경우 MIPS(초당 100만 개 명령)의 메인프레임 메트릭을 가상 CPU와 비교합니다. 

[메인프레임 계산을 Azure로 이동하는](./concepts/mainframe-compute-azure.md)방법에 대해 알아봅니다.

### <a name="high-availability-and-failover"></a>고가용성 및 장애 조치(failover)

Azure는 약정 기반 서비스 수준 계약(SLA)을 제공합니다. 다중 9회 가용성이 기본값이며 SLA는 로컬 또는 지역 기반 서비스 복제로 최적화할 수 있습니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

VM과 같은 Azure IaaS를 사용하면 특정 시스템 함수가 장애 조치(예: 장애 조치 클러스터링 인스턴스 및 가용성 집합)를 제공합니다. Azure 플랫폼을 PaaS(서비스) 리소스로 사용하면 플랫폼이 장애 조치(failover)를 자동으로 처리합니다. 예를 들어 [Azure SQL 데이터베이스](/azure/sql-database/sql-database-technical-overview) 및 Azure [코스모스 DB.](/azure/cosmos-db/introduction)

### <a name="scalability"></a>확장성

일반적으로 메인프레임은 확장되지만 클라우드 환경은 확장됩니다. Azure는 사용자의 요구에 맞게 다양한 [Linux](/azure/virtual-machines/linux/sizes) 및 [Windows](/azure/virtual-machines/windows/sizes) 크기를 제공합니다. 또한 클라우드는 정확한 사용자 사양에 맞게 확장 또는 축소됩니다. 사용량 기반 청구 모델에서 필요에 따라 전력, 스토리지 및 서비스를 [계산할](/azure/architecture/best-practices/auto-scaling) 수 있습니다.

### <a name="storage"></a>스토리지

클라우드에는 유연하고 확장 가능한 다양한 스토리지 옵션이 있으며 필요한 것에 대해서만 비용을 지불합니다. [Azure Storage](/azure/storage/common/storage-introduction)는 데이터 개체용으로 대폭 확장 가능한 개체 저장소, 클라우드용 파일 시스템 서비스, 안정적인 메시징 저장소 및 NoSQL 저장소를 제공합니다. VM의 경우에는 관리형 디스크와 비관리형 디스크에서 안전한 영구 디스크 스토리지가 제공됩니다.

[메인프레임 저장소를 Azure로 이동하는](./concepts/mainframe-storage-azure.md)방법에 대해 알아봅니다.

### <a name="backup-and-recovery"></a>Backup 및 복구

자체 재해 복구 사이트를 유지 관리하는 것은 비용이 많이 드는 제안이 될 수 있습니다. Azure에는 로컬 또는 지역 수준에서 또는 지리적 중복성을 통해 [백업,](/azure/backup/backup-introduction-to-azure-backup) [복구](/azure/site-recovery/site-recovery-overview)및 [중복성을](/azure/storage/common/storage-redundancy) 구현하기 쉽고 비용 효율적인 옵션이 있습니다.

## <a name="azure-government-for-mainframe-migrations"></a>메인프레임 마이그레이션을 위한 Azure 정부

많은 공공 부문 기관은 메인프레임 애플리케이션을 보다 현대적이고 유연한 플랫폼으로 이전하기를 원할 것입니다. Microsoft Azure 정부는 연방, 주 및 지방 정부 시스템에 대해 패키지된 글로벌 Microsoft Azure 플랫폼의 물리적으로 분리된 인스턴스입니다. 미국 정부 기관 및 파트너를 위해 세계 적 수준의 보안, 보호 및 규정 준수 서비스를 제공합니다.

Azure 정부는 이러한 유형의 환경이 필요한 시스템에 대해 FedRAMP 고영향에 대해 P-ATO(임시 권한)를 획득했습니다.

시작하려면 [메인프레임 응용 프로그램에 대한 Microsoft Azure 정부 클라우드를 다운로드합니다.](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)

## <a name="next-steps"></a>다음 단계

[파트너에게](partner-workloads.md) 메인프레임 응용 프로그램을 마이그레이션하거나 다시 호스트할 수 있도록 요청하십시오. 

참고 항목:

- [메인프레임 주제에 대한 백서](mainframe-white-papers.md)
- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [문제 해결](/azure/virtual-machines/troubleshooting/)
- [메인프레임을 Azure 마이그레이션으로 신비화](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
