---
title: Azure Lsv2 시리즈 가상 머신에서 성능 최적화 - 스토리지
description: Lsv2 시리즈 가상 머신에서 솔루션의 성능을 최적화하는 방법을 알아보십시오.
services: virtual-machines-windows
author: sasha-melamed
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 57b248908a02327f2521be05920259681a26817a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920232"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Lsv2 시리즈 가상 머신의 성능 최적화

Lsv2 시리즈 가상 머신은 광범위한 응용 프로그램 및 산업 전반에 걸쳐 로컬 스토리지에서 높은 I/O 및 처리량이 필요한 다양한 워크로드를 지원합니다.  Lsv2 시리즈는 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징 및 카산드라, 몽고DB, 클라우데라 및 Redis를 포함한 대규모 트랜잭션 데이터베이스에 이상적입니다.

Lsv2 시리즈 가상 머신(VM)의 설계는 AMD EPYC™ 7551 프로세서를 최대화하여 프로세서, 메모리, NVMe 장치 및 VM 간에 최상의 성능을 제공합니다. Lsv2 시리즈 VM은 하드웨어 성능을 극대화할 뿐만 아니라 Windows 및 Linux 운영 체제의 요구 사항에 따라 하드웨어 및 소프트웨어의 성능을 향상하도록 설계되었습니다.

소프트웨어와 하드웨어를 튜닝하면 2018년 12월 초에 Lsv2 시리즈 VM의 NVMe 장치에서 최대 성능을 지원하는 Azure Marketplace에 릴리스된 [Windows Server 2019 데이터 센터의](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)최적화된 버전이 생성되었습니다.

이 문서에서는 워크로드 및 응용 프로그램이 VM에 디자인된 최대 성능을 달성할 수 있도록 팁과 제안 사항을 제공합니다. 이 페이지의 정보는 Azure 마켓플레이스에 더 많은 Lsv2 최적화된 이미지가 추가됨에 따라 지속적으로 업데이트됩니다.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ 칩셋 아키텍처

Lsv2 시리즈 VM은 Zen 마이크로아키텍처를 기반으로 ™ AMD EYPC 서버 프로세서를 사용합니다. AMD는 EYPC용 인피니티 패브릭(if)을 온다이™, 온패키지 및 멀티 패키지 통신에 사용할 수 있는 NUMA 모델의 확장 가능한 상호 연결로 개발했습니다. 인텔 최신 모놀리식 다이 프로세서에 사용되는 QPI(퀵 패스 인터커넥트) 및 UPI(울트라 패스 인터커넥트)와 비교할 때 AMD의 다이-NUMA 소형 다이 아키텍처는 성능상의 이점뿐만 아니라 도전과제를 모두 가져올 수 있습니다. 메모리 대역폭 및 대기 시간 제약 조건의 실제 영향은 실행 중인 워크로드 유형에 따라 달라질 수 있습니다.

## <a name="tips-for-maximizing-performance"></a>성능 극대화를 위한 팁

* Lsv2 시리즈 VM에 전원을 공급하는 하드웨어는 8개의 I/O 큐 페어(QP)가 있는 NVMe 장치를 사용합니다. 모든 NVMe 장치 I/O 큐는 실제로 제출 큐와 완료 큐의 쌍입니다. NVMe 드라이버는 라운드 로빈 일정에 I/O를 배포하여 이러한 8개의 I/O QP의 활용도를 최적화하도록 설정되어 있습니다. 최대 성능을 얻으려면 장치당 8개의 작업을 실행하여 일치시도록 합니다.

* 활성 워크로드 중에 NVMe 관리자 명령(예: NVMe SMART 정보 쿼리 등)을 NVMe I/O 명령과 혼합하지 마십시오. Lsv2 NVMe 장치는 하이퍼 V NVMe 다이렉트 기술에 의해 지원되며, NVMe 관리자 명령이 보류 중일 때마다 "느린 모드"로 전환됩니다. 이러한 경우 Lsv2 사용자는 NVMe I/O 성능이 크게 저하될 수 있습니다.

* Lsv2 사용자는 데이터 드라이브에 대해 VM 내에서 보고된 장치 NUMA 정보(모두 0)에 의존하여 앱에 대한 NUMA 선호도를 결정해서는 안 됩니다. 더 나은 성능을 위해 권장되는 방법은 가능하면 CPU 전체에 워크로드를 분산하는 것입니다. 

* Lsv2 VM NVMe 장치의 I/O 큐 쌍당 최대 지원되는 큐 깊이는 1024(Amazon i3 QD 32 제한 과 는 대)입니다. Lsv2 사용자는 (합성) 벤치마킹 워크로드를 대기열 깊이 1024 이하로 제한하여 큐 전체 조건을 트리거하지 않도록 해야 성능이 저하될 수 있습니다.

## <a name="utilizing-local-nvme-storage"></a>로컬 NVMe 스토리지 활용

모든 Lsv2 VM의 1.92TB NVMe 디스크의 로컬 저장소는 일시적이다. VM을 성공적으로 재부팅하는 동안 로컬 NVMe 디스크의 데이터가 유지됩니다. VM이 다시 배포, 할당 해제 또는 삭제된 경우 데이터가 NVMe에서 유지되지 않습니다. 다른 문제로 인해 VM 또는 실행 중인 하드웨어가 비정상상태가 되는 경우 데이터가 유지되지 않습니다. 이 경우 이전 호스트의 모든 데이터가 안전하게 지워집니다.

예를 들어 계획된 유지 관리 작업 중에 VM을 다른 호스트 컴퓨터로 이동해야 하는 경우도 있습니다. 계획된 유지 관리 작업 및 일부 하드웨어 오류는 [예약된 이벤트로](scheduled-events.md)예상할 수 있습니다. 예약된 이벤트는 예측된 유지 관리 및 복구 작업에 대한 최신 상태를 유지하는 데 사용해야 합니다.

계획된 유지 관리 이벤트에서 빈 로컬 디스크가 있는 새 호스트에서 VM을 다시 만들어야 하는 경우 이전 호스트의 모든 데이터가 안전하게 지워지는 경우 데이터를 다시 동기화해야 합니다. 이는 Lsv2 시리즈 VM이 현재 로컬 NVMe 디스크에서 라이브 마이그레이션을 지원하지 않기 때문에 발생합니다.

계획된 유지 관리를 위한 두 가지 모드가 있습니다.

### <a name="standard-vm-customer-controlled-maintenance"></a>표준 VM 고객 제어 유지 보수

- VM은 30일 기간 동안 업데이트된 호스트로 이동됩니다.
- Lsv2 로컬 저장소 데이터가 손실될 수 있으므로 이벤트 전에 백업 데이터를 백업하는 것이 좋습니다.

### <a name="automatic-maintenance"></a>자동 유지보수

- 고객이 고객 제어 유지 관리를 실행하지 않거나 보안 제로 데이 이벤트와 같은 비상 절차가 발생하는 경우에 발생합니다.
- 고객 데이터를 보존하기 위한 것이지만 VM이 멈추거나 재부팅될 위험이 적습니다.
- Lsv2 로컬 저장소 데이터가 손실될 수 있으므로 이벤트 전에 백업 데이터를 백업하는 것이 좋습니다.

예정된 서비스 이벤트의 경우 제어된 유지 관리 프로세스를 사용하여 업데이트에 가장 편리한 시간을 선택합니다. 이벤트 전에 프리미엄 저장소에 데이터를 백업할 수 있습니다. 유지 관리 이벤트가 완료되면 새로 고친 Lsv2 VM 로컬 NVMe 저장소로 데이터를 반환할 수 있습니다.

로컬 NVMe 디스크의 데이터를 유지하는 시나리오는 다음과 같습니다.

- VM이 실행되고 정상입니다.
- VM은 사용자 또는 Azure에 의해 제자리에서 재부팅됩니다.
- VM이 일시 중지됩니다(할당 해제 없이 중지됨).
- 계획된 유지 관리 서비스 작업의 대부분입니다.

고객을 보호하기 위해 데이터를 안전하게 지우는 시나리오는 다음과 같습니다.

- VM이 재배포, 중지(할당 해제) 또는 삭제됩니다(본인이).
- VM이 비정상상태가 되어 하드웨어 문제로 인해 다른 노드에 복구서비스를 서비스를 해야 합니다.
- VM을 서비스를 위해 다른 호스트에 다시 할당해야 하는 계획된 유지 관리 서비스 작업의 소수입니다.

로컬 저장소에서 데이터를 백업하는 옵션에 대한 자세한 내용은 [Azure IaaS 디스크에 대한 백업 및 재해 복구를](backup-and-disaster-recovery-for-azure-iaas-disks.md)참조하십시오.

## <a name="frequently-asked-questions"></a>질문과 대답

* **Lsv2 시리즈 VM 배포를 시작하려면 어떻게 해야 합니까?**  
   다른 VM과 마찬가지로 [포털,](quick-create-portal.md) [Azure CLI](quick-create-cli.md)또는 [PowerShell을](quick-create-powershell.md) 사용하여 VM을 만듭니다.

* **단일 NVMe 디스크 오류로 인해 호스트의 모든 VM이 실패합니까?**  
   하드웨어 노드에서 디스크 오류가 감지되면 하드웨어가 실패한 상태입니다. 이 경우 노드의 모든 VM이 자동으로 할당 해제되고 정상 노드로 이동됩니다. Lsv2 시리즈 VM의 경우 이는 실패한 노드에 대한 고객의 데이터도 안전하게 지워지고 새 노드에서 고객이 다시 만들어야 한다는 것을 의미합니다. 앞서 언급했듯이 Lsv2에서 라이브 마이그레이션을 사용할 수 있게 되기 전에 실패한 노드의 데이터는 VM이 다른 노드로 전송될 때 VM과 함께 사전에 이동됩니다.

* **Windows 서버 2012 또는 Windows 서버 2016에서 Windows에서 폴링 조정을 수행해야 합니까?**  
   NVMe 폴링은 Azure의 Windows 서버 2019에서만 사용할 수 있습니다.  

* **기존 인터럽트 서비스 루틴(ISR) 모델로 다시 전환할 수 있습니까?**  
   Lsv2 시리즈 VM은 NVMe 폴링에 최적화되어 있습니다. 폴링 성능을 향상시키기 위해 업데이트가 지속적으로 제공됩니다.

* **Windows Server 2019에서 폴링 설정을 조정할 수 있습니까?**  
   폴링 설정은 사용자가 조정할 수 없습니다.
   
## <a name="next-steps"></a>다음 단계

* Azure의 스토리지 [성능에 최적화된 모든 VM에](sizes-storage.md) 대한 사양 보기
