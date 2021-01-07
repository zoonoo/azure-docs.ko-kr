---
title: 복구 지점 관리
description: Azure Backup 서비스에서 가상 컴퓨터의 복구 위치를 관리 하는 방법 알아보기
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428707"
---
# <a name="manage-recovery-points"></a>복구 지점 관리

이 문서에서는 가상 컴퓨터에 대 한 보존이 어떻게 작동 하는지 설명 합니다. 백업이 발생할 때마다 복원 작업을 수행할 수 있는 복구 지점이 생성 됩니다.

가상 컴퓨터의 경우 초기 백업은 전체 백업 이며 후속 백업은 증분 백업입니다.

## <a name="recovery-points-and-retention"></a>복구 지점과 보존

### <a name="scheduled-initial-and-incremental-backup"></a>예약 된 초기 및 증분 백업

블록 1, 블록 2, 블록 3 및 블록 4로 구성 된 데이터 디스크를 사용 하 여 가상 컴퓨터 *V1* 의 간단한 예를 살펴보겠습니다. 각 블록의 크기는 16kb입니다.

![4 개의 블록을 포함 하는 가상 머신](./media/manage-recovery-points/four-blocks.png)

**1 단계-초기 백업:** 초기 백업은 전체 백업입니다. 후속 증분 백업이 적용 되는 기준으로 작동 합니다. 원본 VM의 블록 1과 블록 2에 기록 된 데이터가 있다고 가정 합니다. 동일한 데이터가 Recovery Services 자격 증명 모음 저장소에서 D1 및 D2로 복제 됩니다.

![초기 백업이 복제 됩니다.](./media/manage-recovery-points/initial-backup.png)

**2 단계-증분 백업 1:** VM의 블록 3에 새로운 데이터가 추가 된 것을 고려 합니다. 동일한 데이터가 다음 증분 백업에서 복제 되 고 변경 된 블록만 D3로 저장 됩니다.  각 단계에서 블록의 1kb가 변경 되더라도 전체 16kb 블록이 복구 지점에 업로드 됩니다.

![첫 번째 증분 백업](./media/manage-recovery-points/first-incremental-backup.png)

**3 단계-증분 백업 2:**  이제 원본 VM의 블록 3 및 블록 2에 대 한 데이터 변경 내용이 있다고 간주 합니다. 이러한 변경 내용은 다음 증분 백업에서 D3 ' 및 D2 '로 복제 됩니다.

![두 번째 증분 백업](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>주문형 백업

보호를 설정한 후 언제 든 지 VM의 주문형 백업을 실행 하도록 선택할 수 있습니다.

- 요청 시 백업은 첫 번째 예약 된 초기 백업 이전에 트리거된 경우 전체 백업이 됩니다.
- 초기 백업이 완료 되 고 요청 시 백업이 트리거되면 증분 백업입니다.
- 주문형 백업에 대해 만들어진 복구 지점의 보존 시간은 백업을 트리거할 때 지정 하는 보존 기간입니다.

### <a name="storage-cost"></a>스토리지 비용

초기 백업에 대해 생성 된 **복구 지점** 에는 데이터를 포함 하는 모든 블록이 포함 됩니다. 후속 증분 복구 지점은 데이터를 변경 하는 블록만 구성 됩니다. 저장소 비용은 모든 복구 지점이 걸쳐 있는 모든 블록의 합계에 해당 합니다.

위의 예제를 사용 하 여 각 단계 후의 저장소 비용을 이해 하겠습니다.

|단계  |백업 유형  |블록 변경  |스토리지 유형 |
|------|---------|---------|---------|
|1     |     초기 백업    | 블록 1, 블록 2        |    복구 지점 1 (D1 + D2)에 해당 합니다.     |
|2     |  증분 백업 1       |  블록 3       |   복구 지점 1 (D1 + D2) + 복구 지점 2 (D3)에 해당 합니다.      |
|3     |    증분 백업 2     |    블록 2, 블록 3     |   복구 지점 1 (D1 + D2) + 복구 지점 2 (D3) + 복구 지점 3 (D2 ' + D3 ')에 해당 합니다.      |

### <a name="recovery-point-expiration"></a>복구 지점 만료

각 복구 지점에는 백업 정책에 지정 된 대로 보존 기간이 있습니다. 정리는 일정 한 간격으로 수행 되며 만료 된 모든 복구 지점은 정리 됩니다.

복구 지점이 만료 되 면 삭제 되거나 병합 됩니다.

### <a name="case-1-initial-recovery-point-expires"></a>사례 1: 초기 복구 지점이 만료 됩니다.

초기 복구 지점이 만료 되 면 다음 증분 복구 지점과 병합 됩니다. 증분 복구 지점에서 덮어쓴 모든 데이터 블록이 삭제 되 고 나머지는 병합 됩니다. 그러면 증분 백업이 초기 전체 백업이 됩니다. 예제를 검토해 보겠습니다.

- 초기 백업 중에 생성 된 *복구 지점 1* 에는 VM의 전체 백업이 있습니다.
- *복구 지점 1* 이 만료 되 면 *복구 지점 2* 는 다음 전체 백업입니다.
- 블록 2의 데이터는 *복구 지점 2* 에서 덮어쓰므로 블록 2는 *복구 지점 2* 에 병합 되 고 D2는 삭제 됩니다. 이 변경은 블록 D2로 캡처됩니다.
- 다음 백업 전에 변경 내용이 적용 될 때까지 D1 블록은 연속 복구 시점에 그대로 유지 됩니다.

![첫 번째 사례](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>사례 2: 증분 복구 지점이 만료 되는 경우

- 복구 지점 *2* 가 복구 지점 *1* 이전에 만료 되 면 복구 지점 *2* 의 데이터가 사용 가능한 다음 복구 지점 ( *복구 지점 3* )과 병합 됩니다. 따라서 D3 블록이 *복구 지점 3* 과 병합 됩니다.
- *복구 지점 1* 은 여전히 D1 및 D2 블록의 전체 백업입니다.

![두 번째 사례](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>사례 3: 주문형 복구 지점 만료

이 예에서는 일정 (일별 백업) 정책이 *n* 일의 보존 기간으로 실행 되도록 예약 됩니다.  다음 예약 된 백업 및 보존 기간을 10 일로 지정 하기 전에 4 일에 요청 시 백업이 트리거되면 증분 백업이 됩니다. 복구 지점 *3* 이전 및 *복구 지점 4* 이전에 복구 지점 ( *주문형 RP1* )이 생성 됩니다.  14 일이 끝나면 주문형 복구 지점 ( *주문형 RP1* )이 만료 되 고 사용 가능한 다음 복구 지점과 병합 됩니다. 서버에 아직 있는 데이터 블록은 병합 되지만 변경 (덮어쓰기 또는 삭제) 된 데이터 블록은 만료 된 복구 지점에서 삭제 됩니다.

![세 번째 사례](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>복구 지점의 정책 변경의 영향

정책이 수정 되 면 새 복구 지점과 기존 복구 지점의 정책이 모두 적용 됩니다. 자세한 내용은 [복구 지점의 정책 변경의 영향](backup-architecture.md#impact-of-policy-change-on-recovery-points)을 참조 하세요.

### <a name="impact-of-stop-protection-on-recovery-points"></a>복구 지점의 보호 중지의 영향

VM 보호를 중지 하는 방법에는 다음 두 가지가 있습니다.

- **보호를 중지 하 고 백업 데이터를 삭제 합니다.** 이 옵션을 선택 하면 이후의 모든 백업 작업이 VM을 보호 하지 않으며 모든 복구 지점이 삭제 됩니다. [일시 삭제](backup-azure-security-feature-cloud.md) 를 사용 하는 경우 삭제 된 데이터는 14 일 동안 보존 됩니다. 일시 삭제 된 상태의 항목에 대 한 요금은 발생 하지 않습니다. 데이터는 14 일 기간 내에 삭제 취소할 수 있습니다. 일시 삭제를 사용 하도록 설정 하지 않으면 데이터가 즉시 정리 되며 VM을 복원 하거나 **백업 다시 시작** 옵션을 사용할 수 없습니다.
- **보호를 중지 하 고 백업 데이터를 보존 합니다.** 이 옵션은 VM을 보호 하는 이후 모든 백업 작업을 중지 합니다. 그러나 Azure Backup 서비스는 백업 된 복구 지점은 영구적으로 유지 됩니다. 자격 증명 모음에서 복구 지점이 유지 되도록 요금을 지불 해야 합니다 (자세한 내용은 [Azure Backup 가격](https://azure.microsoft.com/pricing/details/backup/) 정보 참조). 필요한 경우 VM을 복원할 수 있습니다. VM 보호를 다시 시작 하기로 결정 한 경우 **백업 다시 시작** 옵션을 사용할 수 있습니다. 백업을 다시 시작한 후에는 보존 규칙이 만료 점에 적용 됩니다. **백업 데이터 삭제** 옵션을 사용 하 여 백업 된 데이터를 삭제할 수도 있습니다.

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>보호를 중지 하지 않고 VM을 삭제할 경우의 영향

보호를 중지 하지 않고 VM을 삭제 하면 복구 지점이 영향을 받게 되며 바람직하지 않은 시나리오입니다. 가상 컴퓨터를 삭제 하기 전에 이상적인 백업을 중지 해야 합니다. 리소스가 존재 하지 않으므로 예약 된 백업은 [VMNotFoundV2 오류로](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found)인해 실패 합니다. 복구 지점은 보존 정책에 따라 주기적으로 정리 되지만 가상 머신의 마지막 복사본은 영구적으로 유지 되 고 그에 따라 요금이 청구 됩니다. 시나리오에 따라 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

- **옵션 1:** 복구 위치 중 하나를 사용 하 여 VM을 복원 합니다. 삭제 된 VM을 복구 하려면 동일한 리소스 그룹에서 동일한 이름 및를 사용 하 여 복원 합니다. 복원 된 VM을 동일한 자격 증명 모음으로 보호 하는 경우 기존 복구 지점이 자동으로 연결 됩니다.
- **옵션 2:** Recovery Services 자격 증명 모음으로 이동 하 여 데이터 삭제로 보호를 중지 합니다.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>일시 삭제 된 상태의 항목에 대해 만료 된 복구 지점의 영향

Recovery services 자격 증명 모음에 대해 [일시 삭제](backup-azure-security-feature-cloud.md) 를 사용 하도록 설정 하면 만료 된 복구 지점은 일시 삭제 된 상태로 유지 되 고 정리 되지 않습니다. 복구 지점이 일시 삭제 된 상태인 경우에는 요금이 부과 되지 않습니다.

### <a name="impact-of-churn-on-backup-performance"></a>백업 성능에 대 한 변동의 영향

VM의 총 저장소가 8TB이 고 변동이 5% 라고 가정 합니다. 그런 다음 해당 증분 백업 저장소는 0.4 tb의 5%입니다. 변동 증가는 후속 증분 백업의 경우 더 높은 백엔드 저장소에 해당 합니다. 변동은 백업 성능에 영향을 줍니다. 변동 수준이 높을수록 백업 프로세스가 느려지고 백 엔드 저장소 소비가 증가 합니다.

변동 (code churn)이 백업 성능에 미치는 영향을 이해 하려면 다음 시나리오를 살펴보세요.

|가상 머신  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|데이터 디스크 수    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|각 디스크의 크기   |      4 TB   | 4 TB        |  4 TB       |
|백업 데이터 변동    |   A1-4TB      | B1-1TB; B2-1TB <br> B3-1TB; B4-1TB  |   C1-2TB; C4-2TB      |

백업 성능은 order V M 2>V M 3>VM1에 있습니다. 그 이유는 변동 데이터가 다양 한 디스크에 분산 되기 때문입니다. 디스크의 백업이 병렬로 발생 하므로 V M 2는 최상의 성능을 보여 줍니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup 아키텍처 및 구성 요소](backup-architecture.md)
