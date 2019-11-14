---
title: Azure Lsv2 시리즈 가상 머신에서 성능 최적화-저장소
description: Lsv2 시리즈 가상 머신에서 솔루션의 성능을 최적화 하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 8d99f63ae084b4f1dae3c0125420eaecf5655e2d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034762"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Lsv2 시리즈 가상 머신에서 성능 최적화

Lsv2 시리즈 가상 머신은 광범위 한 응용 프로그램 및 업계에서 로컬 저장소에 대 한 높은 i/o 및 처리량을 필요로 하는 다양 한 워크 로드를 지원 합니다.  Lsv2 시리즈는 Cassandra, MongoDB, Cloudera 및 Redis를 포함 하 여 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징 및 대규모 트랜잭션 데이터베이스에 적합 합니다.

Lsv2 시리즈 Virtual Machines (Vm)의 디자인은 AMD EPYC™ 7551 프로세서를 최대화 하 여 프로세서, 메모리, NVMe 장치 및 Vm 간에 최상의 성능을 제공 합니다. Linux에서 파트너를 사용 하는 경우 Lsv2 시리즈 성능에 최적화 되어 있고 현재 다음을 포함 하는 Azure Marketplace 몇 가지 빌드를 사용할 수 있습니다.

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debian 9
- Debian 10

이 문서에서는 워크 로드 및 응용 프로그램이 Vm에서 설계 된 최대 성능을 달성할 수 있도록 팁과 제안을 제공 합니다. Lsv2 최적화 이미지가 Azure Marketplace에 추가 되 면이 페이지의 정보는 지속적으로 업데이트 됩니다.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ 칩셋 아키텍처

Lsv2 시리즈 Vm은 Zen 마이크로 아키텍처를 기반으로 하는 AMD EYPC™ 서버 프로세서를 사용 합니다. EYPC c에 대해 개발 된 Infinity Infinity (인 경우)는 해당 NUMA 모델에 대 한 확장 가능한 상호 연결로, 즉, 주문형, 패키지 및 다중 패키지 통신에 사용할 수 있습니다. Intel 최신 모놀리식 프로세서에서 사용 되는 QPI (빠른 경로 상호 연결) 및 UPI (Ultra Path Interconnect)와 비교 했을 때 AMD의 많은 NUMA 사소한 주사위 아키텍처는 성능 이점과 챌린지를 모두 가져올 수 있습니다. 메모리 대역폭과 대기 시간 제약 조건의 실제 영향은 실행 중인 작업의 유형에 따라 달라질 수 있습니다.

## <a name="tips-to-maximize-performance"></a>성능 최대화 팁

* 워크 로드에 대 한 사용자 지정 Linux GuestOS을 업로드 하는 경우 가속화 된 네트워킹은 기본적으로 **해제** 됩니다. 가속화 된 네트워킹을 사용 하도록 설정 하려는 경우 최적의 성능을 위해 VM을 만들 때 사용 하도록 설정 합니다.

* Lsv2 시리즈 Vm을 구동 하는 하드웨어는 8 개의 i/o 큐 쌍 (QP)을 사용 하는 NVMe 장치를 활용 합니다. 모든 NVMe 장치 i/o 큐는 실제로 전송 큐와 완료 큐와 같은 쌍입니다. NVMe 드라이버는 i/o를 라운드 로빈 일정으로 배포 하 여 이러한 8 i/o QPs의 사용률을 최적화 하도록 설정 되어 있습니다. 최대 성능을 얻으려면 일치 하는 장치 마다 8 개의 작업을 실행 합니다.

* 활성 작업 중에 nvme i/o 명령을 사용 하 여 nvme admin 명령 (예: NVMe 스마트 정보 쿼리 등)을 혼합 하지 마세요. Lsv2 NVMe 장치는 NVMe 관리자 명령이 보류 될 때마다 "느림 모드"로 전환 되는 Hyper-v NVMe Direct 기술로 지원 됩니다. Lsv2 사용자가 해당 하는 경우에는 NVMe i/o 성능에 극적인 성능 저하를 볼 수 있습니다.

* Lsv2 사용자는 응용 프로그램에 대 한 NUMA 선호도를 결정 하기 위해 데이터 드라이브에 대 한 VM 내에서 보고 되는 장치 NUMA 정보 (모두 0)를 사용 하면 안 됩니다. 성능 향상을 위해 가능한 경우 Cpu 간에 워크 로드를 분산 하는 것이 좋습니다.

* Lsv2 VM NVMe 장치에 대 한 i/o 큐 쌍 당 지원 되는 최대 큐 깊이는 1024 (Amazon i3 QD 32 제한)입니다. Lsv2 사용자는 큐의 전체 상태가 트리거되지 않도록 (가상) 벤치마킹 워크 로드를 큐 깊이 1024 이하로 제한 하 여 성능을 저하 시킬 수 있습니다.

## <a name="utilizing-local-nvme-storage"></a>로컬 NVMe 저장소 활용

모든 Lsv2 Vm의 1.92 TB NVMe 디스크에 있는 로컬 저장소는 삭제 됩니다. VM의 표준 다시 부팅이 완료 되 면 로컬 NVMe 디스크의 데이터가 유지 됩니다. VM이 다시 배포, 할당 취소 또는 삭제 된 경우에는 NVMe에 데이터가 유지 되지 않습니다. 다른 문제로 인해 VM 또는 VM이 실행 중인 하드웨어가 비정상 상태가 되는 경우 데이터가 유지 되지 않습니다. 이 경우 이전 호스트의 모든 데이터가 안전 하 게 지워집니다.

또한 계획 된 유지 관리 작업을 수행 하는 경우와 같이 VM을 다른 호스트 컴퓨터로 이동 해야 하는 경우도 있습니다. 계획 된 유지 관리 작업 및 일부 하드웨어 오류는 [Scheduled Events](scheduled-events.md)으로 예상할 수 있습니다. 예측 된 유지 관리 및 복구 작업에 대 한 업데이트를 유지 하려면 Scheduled Events를 사용 해야 합니다.

계획 된 유지 관리 이벤트에서 로컬 디스크가 비어 있는 새 호스트에 VM을 다시 만들어야 하는 경우에는 데이터를 다시 동기화 해야 합니다 (이전 호스트의 모든 데이터가 안전 하 게 삭제 됨). 이는 Lsv2 시리즈 Vm이 현재 로컬 NVMe 디스크에서 실시간 마이그레이션을 지원 하지 않기 때문에 발생 합니다.

계획 된 유지 관리에는 두 가지 모드가 있습니다.

### <a name="standard-vm-customer-controlled-maintenance"></a>표준 VM 고객 제어 유지 관리

- 30 일 기간 동안 VM이 업데이트 된 호스트로 이동 됩니다.
- Lsv2 로컬 저장소 데이터가 손실 될 수 있으므로 이벤트 이전의 데이터를 백업 하는 것이 좋습니다.

### <a name="automatic-maintenance"></a>자동 유지 관리

- 고객이 제어 하는 유지 관리 작업을 실행 하지 않는 경우 또는 보안 제로 일 이벤트와 같은 응급 절차의 경우에 발생 합니다.
- 고객 데이터를 유지 하기 위한 것 이지만 VM이 중지 되거나 다시 부팅 되는 경우에는 약간의 위험이 있습니다.
- Lsv2 로컬 저장소 데이터가 손실 될 수 있으므로 이벤트 이전의 데이터를 백업 하는 것이 좋습니다.

예정 된 서비스 이벤트의 경우 제어 된 유지 관리 프로세스를 사용 하 여 업데이트에 가장 편리한 시간을 선택 합니다. 이벤트 이전에는 premium storage에서 데이터를 백업할 수 있습니다. 유지 관리 이벤트가 완료 된 후 데이터를 새로 고친 Lsv2 Vm 로컬 NVMe 저장소로 되돌릴 수 있습니다.

로컬 NVMe 디스크에서 데이터를 유지 관리 하는 시나리오는 다음과 같습니다.

- VM이 실행 중이 고 정상입니다.
- VM은 사용자 또는 Azure에서 적절히 다시 부팅 됩니다.
- VM이 일시 중지 됩니다 (할당 취소 없이 중지 됨).
- 대부분의 계획 된 유지 관리 서비스 작업

고객을 보호 하기 위해 데이터를 안전 하 게 지우는 시나리오는 다음과 같습니다.

- VM이 다시 배포 되거나 중지 (할당 취소) 되거나 삭제 됩니다 (사용자가).
- VM이 비정상 상태가 되 고 하드웨어 문제로 인해 다른 노드로 서비스를 치료 해야 합니다.
- 서비스를 위해 다른 호스트에 VM을 다시 할당 해야 하는 계획 된 유지 관리 서비스 작업의 몇 가지입니다.

로컬 저장소에서 데이터를 백업 하는 옵션에 대 한 자세한 내용은 [Azure IaaS 디스크에 대 한 백업 및 재해 복구](backup-and-disaster-recovery-for-azure-iaas-disks.md)를 참조 하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

* **Lsv2 시리즈 Vm 배포를 시작할 어떻게 할까요? 있나요?**  
   다른 VM과 마찬가지로 [포털](quick-create-portal.md), [Azure CLI](quick-create-cli.md)또는 [POWERSHELL](quick-create-powershell.md) 을 사용 하 여 vm을 만듭니다.

* **단일 NVMe 디스크 오류로 인해 호스트의 모든 Vm이 실패 하나요?**  
   하드웨어 노드에서 디스크 오류가 검색 되 면 하드웨어가 실패 상태에 있습니다. 이 경우 노드의 모든 Vm이 자동으로 할당 취소 되 고 정상 노드로 이동 합니다. Lsv2 시리즈 Vm의 경우 오류가 발생 한 노드의 고객 데이터도 안전 하 게 삭제 되며 고객이 새 노드에 대해 다시 만들어야 합니다. 앞서 언급 했 듯이 Lsv2에서 실시간 마이그레이션을 사용할 수 있게 되기 전에 오류가 발생 한 노드의 데이터는 다른 노드로 전송 될 때 Vm과 사전에 이동 합니다.

* **성능을 위해 rq_affinity를 조정 해야 하나요?**  
   Rq_affinity 설정은 최대 IOPS (초당 입력/출력 작업 수)를 사용 하는 경우 약간 조정 됩니다. 다른 모든 항목이 제대로 작동 하는 경우에는 rq_affinity를 0으로 설정 하 여 차이가 있는지 확인 하십시오.

* **Blk_mq 설정을 변경 해야 하나요?**  
   RHEL/CentOS 7.x는 NVMe 장치에 대해 blk-mq를 자동으로 사용 합니다. 구성 변경 이나 설정은 필요 하지 않습니다. Scsi_mod. use_blk_mq 설정은 vm에만 사용 되며 NVMe 장치가 게스트 Vm에 SCSI 장치로 표시 되기 때문에 Lsv2 Preview 중에 사용 되었습니다. 현재 NVMe 장치는 NVMe 장치로 표시 되므로 SCSI blk-mq 설정은 관련이 없습니다.

* **"Fio"를 변경 해야 하나요?**  
   L64v2 및 L80v2 VM 크기에서 ' fio '와 같은 성능 측정 도구를 사용 하 여 최대 IOPS를 얻으려면 각 NVMe 장치에서 "rq_affinity"을 0으로 설정 합니다.  예를 들어 다음 명령줄은 L80v2 VM의 모든 10 개 NVMe 장치에 대해 "rq_affinity"을 0으로 설정 합니다.

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   또한 i/o가 분할 안 함, 파일 시스템, RAID 0 구성 등을 포함 하지 않는 각 원시 NVMe 장치에 직접 수행 되는 경우 최상의 성능을 얻을 수 있습니다. 테스트 세션을 시작 하기 전에 각 NVMe 장치에서 `blkdiscard`를 실행 하 여 구성이 알려진 최신/정리 상태 인지 확인 합니다.
   
## <a name="next-steps"></a>다음 단계

* Azure에서 [저장소 성능에 최적화](sizes-storage.md) 된 모든 vm에 대 한 사양 참조
