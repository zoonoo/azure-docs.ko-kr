---
title: MARS 에이전트를 사용하여 Windows 컴퓨터 백업
description: Microsoft Azure 복구 서비스(MARS) 에이전트를 사용하여 Windows 컴퓨터를 백업합니다.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408915"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Azure 백업 MARS 에이전트를 사용하여 Windows 컴퓨터 백업

이 문서에서는 [Azure 백업](backup-overview.md) 서비스 및 Microsoft Azure 복구 서비스(MARS) 에이전트를 사용하여 Windows 컴퓨터를 백업하는 방법을 설명합니다. MARS는 Azure 백업 에이전트라고도 합니다.

이 아티클에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 필수 구성 요소 확인
> * 백업 정책 및 일정을 만듭니다.
> * 주문형 백업을 수행합니다.

## <a name="before-you-start"></a>시작하기 전에

* Azure [Backup에서 MARS 에이전트를 사용하여 Windows 컴퓨터를 백업하는](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)방법에 대해 알아봅니다.
* 보조 MABS 또는 데이터 보호 관리자 서버에서 MARS 에이전트를 실행하는 [백업 아키텍처에](backup-architecture.md#architecture-back-up-to-dpmmabs) 대해 알아봅니다.
* 지원되는 내용과 MARS 에이전트가 [백업할 수](backup-support-matrix-mars-agent.md) 있는 내용을 검토합니다.
* 백업하려는 컴퓨터에서 [인터넷 액세스를 확인합니다.](install-mars-agent.md#verify-internet-access)
* MARS 에이전트가 설치되지 않은 경우 [여기에서](install-mars-agent.md)설치하는 방법을 알아보십시오.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 복구 지점을 만들기 위해 데이터의 스냅숏을 생성할 시기를 지정합니다. 또한 복구 지점을 유지하는 기간도 지정합니다. MARS 에이전트를 사용하여 백업 정책을 구성합니다.

Azure 백업은 일광 절약 시간제(DST)를 자동으로 고려하지 않습니다. 이 기본값으로 인해 실제 시간과 예약된 백업 시간 사이에 약간의 불일치가 발생할 수 있습니다.

백업 정책을 만들려면:

1. MARS 에이전트를 다운로드하고 등록한 후 에이전트 콘솔을 엽니다. **Microsoft Azure Backup**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.  

1. **작업**에서 **백업 예약을**선택합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault/schedule-first-backup.png)
1. 백업 예약 마법사에서 **Getting started** > **다음**시작 을 선택합니다.
1. **백업할 항목 선택에서** **항목 추가를**선택합니다.

    ![백업할 항목 추가](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. 항목 **선택** 상자에서 백업할 항목을 선택한 다음 **확인을**선택합니다.

    ![백업할 항목 선택](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. **백업할 항목 선택** 페이지에서 **다음**을 선택합니다.
1. 백업 **일정 지정** 페이지에서 일별 또는 주간 백업을 수행할 시기를 지정합니다. 그런 다음 을 **선택합니다.**

    * 백업을 수행하면 복구 지점이 만들어집니다.
    * 환경에서 생성된 복구 지점의 수는 백업 일정에 따라 다릅니다.
    * 하루에 최대 3개의 백업을 예약할 수 있습니다. 다음 예제에서는 자정과 6:00 PM에 하나씩 매일 두 번의 백업이 발생합니다.

        ![일일 백업 일정 설정](./media/backup-configure-vault/day-schedule.png)

    * 주간 백업도 실행할 수 있습니다. 다음 예제에서는 매주 일요일과 수요일 오전 9:30 및 오전 1:00에 백업을 수행합니다.

        ![주간 백업 일정 설정](./media/backup-configure-vault/week-schedule.png)

1. 보존 **정책 선택** 페이지에서 데이터의 기록 복사본을 저장하는 방법을 지정합니다. 그런 다음 을 **선택합니다.**

    * 보존 설정은 저장할 복구 지점과 저장할 기간을 지정합니다.
    * 일별 보존 설정의 경우 일별 보존에 대해 지정된 시간에 지정된 일 수에 대한 최신 복구 지점이 유지됨을 나타냅니다. 또는 매월 30일에 생성된 복구 지점을 12개월 동안 저장해야 함을 나타내기 위해 월별 보존 정책을 지정할 수 있습니다.
    * 일별 및 주간 복구 지점에 대한 보존은 일반적으로 백업 일정과 일치합니다. 따라서 일정이 백업을 트리거하면 백업이 생성하는 복구 지점은 일별 또는 주간 보존 정책이 지정한 기간 동안 저장됩니다.
    * 다음 예제에서,

        * 매일 자정과 6:00 PM의 백업은 7일 동안 유지됩니다.
        * 토요일 자정과 오후 6:00에 백업은 4주 동안 보관됩니다.
        * 매월 마지막 토요일에 자정과 오후 6:00에 백업한 백업은 12개월 동안 보관됩니다.
        * 3월 마지막 토요일에 수행된 백업은 10년 동안 유지됩니다.

        ![보존 정책의 예](./media/backup-configure-vault/retention-example.png)

1. 초기 **백업 유형 선택** 페이지에서 네트워크를 통해 초기 백업을 수행할지 또는 오프라인 백업을 사용할지 결정합니다. 네트워크를 통해 초기 백업을 수행하려면**다음** **을 통해 자동으로** > 선택합니다.

    오프라인 백업에 대한 자세한 내용은 [오프라인 백업에 Azure 데이터 상자 사용을](offline-backup-azure-data-box.md)참조하십시오.

    ![초기 백업 유형 선택](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. **확인** 페이지에서 정보를 검토한 다음 **완료를**선택합니다.

    ![백업 유형 확인](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. 마법사가 백업 일정 만들기를 완료한 후 **닫기**를 선택합니다.

    ![백업 일정 진행 상황 보기](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

에이전트가 설치된 각 컴퓨터에 정책을 만듭니다.

### <a name="do-the-initial-backup-offline"></a>초기 백업을 오프라인으로 수행

네트워크를 통해 초기 백업을 자동으로 실행하거나 오프라인으로 백업할 수 있습니다. 초기 백업을 위한 오프라인 시드는 전송하기 위해 많은 네트워크 대역폭이 필요한 많은 양의 데이터가 있는 경우에 유용합니다.

오프라인 전송을 수행하려면 다음을 수행합니다.

1. 백업 데이터를 스테이징 위치에 씁니다.
1. AzureOfflineBackupDiskPrep 도구를 사용하여 스테이징 위치에서 하나 이상의 SATA 디스크로 데이터를 복사합니다.

    이 도구는 Azure 가져오기 작업을 만듭니다. 자세한 내용은 [Azure 가져오기/내보내기 서비스입니다.](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)
1. SATA 디스크를 Azure 데이터 센터로 보냅니다.

    데이터 센터에서 디스크 데이터가 Azure 저장소 계정으로 복사됩니다. Azure Backup은 저장소 계정에서 볼트로 데이터를 복사하고 증분 백업이 예약됩니다.

오프라인 시드에 대한 자세한 내용은 [오프라인 백업에 Azure 데이터 상자 사용을](offline-backup-azure-data-box.md)참조하십시오.

### <a name="enable-network-throttling"></a>네트워크 제한 사용

MARS 에이전트가 네트워크 제한을 사용하도록 설정하여 네트워크 대역폭을 사용하는 방식을 제어할 수 있습니다. 제한은 근무 시간 동안 데이터를 백업해야 하지만 백업 및 복원 활동이 사용하는 대역폭을 제어하려는 경우에 유용합니다.

Azure 백업의 네트워크 제한은 로컬 운영 체제에서 [QoS(서비스 품질)를](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) 사용합니다.

백업을 위한 네트워크 제한은 Windows Server 2012 이상 및 Windows 8 이상에서 사용할 수 있습니다. 운영 체제는 최신 서비스 팩을 실행해야 합니다.

네트워크 제한을 사용하려면 다음을 수행하십시오.

1. MARS 에이전트에서 **속성 변경을**선택합니다.
1. **제한** 탭에서 **백업 작업에 대한 인터넷 대역폭 사용 제한 을 선택합니다.**

    ![백업 작업에 대한 네트워크 제한 설정](./media/backup-configure-vault/throttling-dialog.png)
1. 근무 시간 및 업무 외 시간 동안 허용되는 대역폭을 지정합니다. 대역폭 값은 512Kbps에서 시작하여 최대 1,023MBps까지 올라갑니다. 그런 다음 **확인을**선택합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

1. MARS 에이전트에서 **지금 백업을 선택합니다.**

    ![Windows 서버에서 지금 백업](./media/backup-configure-vault/backup-now.png)

1. MARS 에이전트 버전이 2.0.9169.0 이상인 경우 사용자 지정 보존 날짜를 설정할 수 있습니다. 백업 **유지 섹션에서** 달력에서 날짜를 선택합니다.

   ![캘린더를 사용하여 보존 날짜를 사용자 지정](./media/backup-configure-vault/mars-ondemand.png)

1. **확인** 페이지에서 설정을 검토하고 **백업**을 선택합니다.
1. **닫기를** 선택하여 마법사를 닫습니다. 백업이 완료되기 전에 마법사를 닫으면 마법사가 백그라운드에서 계속 실행됩니다.

초기 백업이 완료되면 **작업 완료** 상태가 백업 콘솔에 나타납니다.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>온디맨드 백업 정책 보존 동작 설정

> [!NOTE]
> 이 정보는 2.0.9169.0보다 오래된 MARS 에이전트 버전에만 적용됩니다.
>

| 백업 일정 옵션 | 데이터 보존 기간
| -- | --
| 일 | **기본 보존**: "일별 백업의 일 수"에 해당합니다. <br/><br/> **예외:** 장기 보존(주, 월 또는 년)으로 설정된 일일 예약 백업이 실패하는 경우 장애 직후 트리거되는 온디맨드 백업이 장기 보존으로 간주됩니다. 그렇지 않으면 다음 예약된 백업이 장기 보존으로 간주됩니다.<br/><br/> **예제 시나리오:** 목요일 오전 8:00에 예약된 백업이 실패했습니다. 이 백업은 주간, 월별 또는 연간 보존에 대해 고려되어야 했습니다. 따라서 금요일 오전 8:00에 다음 예약된 백업 전에 트리거된 첫 번째 주문형 백업은 주간, 월별 또는 연간 보존에 대해 자동으로 태그가 지정됩니다. 이 백업은 목요일 8:00 AM 백업을 대신합니다.
| Week | **기본 보존**: 1일. 주간 백업 정책이 있는 데이터 원본에 대해 수행되는 주문형 백업은 다음 날 삭제됩니다. 데이터 원본에 대한 최신 백업인 경우에도 삭제됩니다. <br/><br/> **예외:** 장기 보존(주, 월 또는 년)으로 설정된 주간 예약 백업이 실패하는 경우 장애 직후트리거되는 온디맨드 백업이 장기 보존으로 간주됩니다. 그렇지 않으면 다음 예약된 백업이 장기 보존으로 간주됩니다. <br/><br/> **예제 시나리오:** 목요일 오전 8:00에 예약된 백업이 실패했습니다. 이 백업은 월별 또는 연간 보존에 대해 고려되어야 했습니다. 따라서 목요일 오전 8:00에 다음 예약된 백업 전에 트리거되는 첫 번째 주문형 백업은 월별 또는 연간 보존에 대해 자동으로 태그가 지정됩니다. 이 백업은 목요일 8:00 AM 백업을 대신합니다.

자세한 내용은 [백업 정책 만들기를](#create-a-backup-policy)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 에서 파일을 복원하는](backup-azure-restore-windows-server.md)방법에 대해 알아봅니다.
* [파일 및 폴더 백업에 대한 일반적인 질문](backup-azure-file-folder-backup-faq.md) 찾기

