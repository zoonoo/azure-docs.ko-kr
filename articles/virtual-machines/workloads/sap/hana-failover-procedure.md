---
title: Azure(큰 인스턴스)에서 SAP HANA에 대해 재해 복구 사이트로 장애 조치(failover)를 수행하는 방법 | Microsoft Docs
description: Azure(큰 인스턴스)에서 SAP HANA에 대해 재해 복구 사이트로 장애 조치(failover)를 수행하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48bea302f2f8e878275e6a7c222d703e0f31f239
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492867"
---
# <a name="disaster-recovery-failover-procedure"></a>재해 복구 장애 조치(failover) 프로시저


>[!IMPORTANT]
>이 문서는 SAP HANA 관리 설명서 또는 SAP 노트를 대체하지 않습니다. reader가 특히 백업, 복원, 고가용성 및 재해 복구의 항목에서 SAP HANA 관리 및 작업을 이해하고 전문 지식을 가졌다고 가정하겠습니다. 이 문서에서는 SAP HANA Studio의 스크린샷이 표시됩니다. SAP 관리 도구 스크린의 콘텐츠, 구조 및 특성과 도구 자체는 릴리스할 SAP HANA 릴리스마다 변경될 수 있습니다.

DR 사이트로 장애 조치(failover)할 때 고려할 두 가지 경우가 있습니다.

- SAP HANA 데이터베이스를 최신 상태의 데이터로 되돌려야 합니다. 이 경우에 Microsoft에 문의할 필요 없이 장애 조치(failover)를 수행할 수 있는 셀프 서비스 스크립트가 있습니다. 하지만 장애 복구(failback)의 경우 Microsoft로 작업해야 합니다.
- 최신 복제된 스냅숏이 아닌 저장소 스냅숏으로 복원할 수도 있습니다. 이 경우에 Microsoft로 작업해야 합니다. 

>[!NOTE]
>다음 단계는 DR 단위를 나타내는 HANA 대규모 인스턴스 단위에서 실행되어야 합니다. 
 
최신 복제된 저장소 스냅숏을 복원하려면 다음 단계를 수행합니다. 

1. 실행 중인 HANA 대규모 인스턴스의 재해 복구 단위에서 HANA의 비프로덕션 인스턴스를 종료합니다. 유휴 HANA 프로덕션 인스턴스가 미리 설치되어 있기 때문입니다.
1. 실행 중인 SAP HANA 프로세스가 없는지 확인해야 합니다. 이것을 확인하려면 다음 명령을 사용합니다. `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList` 출력에는 **hdbdaemon** 프로세스가 중지된 상태로 표시되고, 실행 중이거나 시작된 상태의 다른 HANA 프로세스는 표시되지 않습니다.
1. DR 사이트 HANA 대규모 인스턴스 단위에서 *azure_hana_dr_failover.pl* 스크립트를 실행합니다. 스크립트에서 복원할 SAP HANA SID를 묻는 메시지가 표시됩니다. 요청되면 복제되었으며 DR 사이트의 HANA 대규모 인스턴스 단위에 있는 *HANABackupCustomerDetails.txt* 파일에서 유지 관리되는 하나 또는 유일한 SAP HANA SID를 입력합니다. 

      여러 SAP HANA 인스턴스를 장애 조치하려는 경우 스크립트를 여러 번 실행해야 합니다. 요청된 경우 장애 조치하고 복원하려는 SAP HANA SID를 입력합니다. 완료 시 스크립트에서 HANA 대규모 인스턴스 단위에 추가된 볼륨의 탑재 지점 목록을 표시합니다. 이 목록에는 복원된 DR 볼륨도 포함됩니다.

1. Linux 운영 체제 명령을 사용하여 복원된 재해 복구 볼륨을 재해 복구 사이트의 HANA 대규모 인스턴스 단위에 탑재합니다. 
1. 유휴 SAP HANA 프로덕션 인스턴스를 시작합니다.
1. RPO 시간을 줄이기 위해 트랜잭션 로그 백업 로그를 복사하도록 선택한 경우 해당 트랜잭션 로그 백업을 새로 탑재된 DR/hana/logbackups 디렉터리에 병합해야 합니다. 기존 백업을 덮어쓰지 마십시오. 저장소 스냅숏의 최신 복제를 사용하여 복제되지 않은 최신 백업만 복사합니다.
1. DR Azure 지역의 /hana/shared/PRD 볼륨에 복제된 스냅숏에서 단일 파일을 복원할 수도 있습니다. 

실제 복제 관계에 영향을 주지 않고 DR 장애 조치(failover)를 테스트할 수 있습니다. 테스트 장애 조치를 수행하려면 위의 1, 2단계를 수행한 다음, 3단계를 계속 진행합니다.

>[!IMPORTANT]
>3단계에서 소개된 스크립트를 사용하여 **장애 조치(failover) 테스트** 과정을 통해 DR 사이트에서 만든 인스턴스에서 프로덕션 트랜잭션을 실행하지 *않습니다*. 해당 명령은 기본 사이트와 아무 관계도 없는 볼륨 세트를 만듭니다. 따라서 기본 사이트로 다시 동기화할 수 *없습니다*. 

테스트 장애 조치의 3단계:

DR 사이트 HANA 대규모 인스턴스 단위에서 **azure_hana_test_dr_failover.pl** 스크립트를 실행합니다. 이 스크립트는 기본 사이트와 DR 사이트 간의 복제 관계를 중지하지 *않습니다*. 대신, 이 스크립트는 DR 저장소 볼륨을 복제합니다. 복제 프로세스가 성공하면 복제된 볼륨이 최신 스냅숏 상태로 복원된 후 DR 단위에 탑재됩니다. 스크립트에서 복원할 SAP HANA SID를 묻는 메시지가 표시됩니다. 복제되었으며 DR 사이트의 HANA 대규모 인스턴스 단위에 있는 *HANABackupCustomerDetails.txt* 파일에서 유지 관리되는 하나 또는 유일한 SAP HANA SID를 입력합니다. 

여러 SAP HANA 인스턴스를 테스트하려는 경우 스크립트를 여러 번 실행해야 합니다. 요청된 경우 장애 조치를 위해 테스트하려는 인스턴스의 SAP HANA SID를 입력합니다. 완료 시 스크립트에서 HANA 대규모 인스턴스 단위에 추가된 볼륨의 탑재 지점 목록을 표시합니다. 이 목록에는 복제된 DR 볼륨도 포함됩니다.

4단계로 계속 진행합니다.

   >[!NOTE]
   >몇 시간 전에 삭제한 일부 데이터를 복구하기 위해 장애 조치(failover)하고 DR 볼륨을 이전 스냅숏으로 설정해야 하는 경우 이 프로시저가 적용됩니다. 

1. 실행 중인 HANA 대규모 인스턴스의 재해 복구 단위에서 HANA의 비프로덕션 인스턴스를 종료합니다. 유휴 HANA 프로덕션 인스턴스가 미리 설치되어 있기 때문입니다.
1. 실행 중인 SAP HANA 프로세스가 없는지 확인해야 합니다. 이것을 확인하려면 다음 명령을 사용합니다. `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList` 출력에는 **hdbdaemon** 프로세스가 중지된 상태로 표시되고, 실행 중이거나 시작된 상태의 다른 HANA 프로세스는 표시되지 않습니다.
1. 재해 복구 사이트를 복원하려는 스냅숏 이름 또는 SAP HANA 백업 ID를 결정합니다. 실제 재해 복구의 경우 이 스냅숏이 일반적으로 최신 스냅숏입니다. 손실된 데이터를 복구해야 하는 경우 이전 스냅숏을 선택하십시오.
1. 우선 순위가 높은 지원 요청을 통해 Azure 지원에 문의합니다. DR 사이트에서 해당 스냅숏(스냅숏의 이름 및 날짜를 포함) 또는 HANA 백업 ID의 복원을 요청합니다. 기본값은 작업 쪽에서 /hana/data 볼륨만 복원하는 것입니다. /hana/logbackups 볼륨도 필요하면 구체적으로 명시해야 합니다. */hana/shared 볼륨을 복원하지 마십시오.* 대신 PRD에서 /hana/shared 볼륨을 다시 탑재한 후에 **.snapshot** 디렉터리 및 해당 하위 디렉터리에서 특정 파일(예: global.ini)를 선택해야 합니다. 

   작업 쪽에서 다음 단계가 수행됩니다.

   a. 프로덕션 볼륨에서 재해 복구 볼륨으로의 스냅숏 복제는 중지됩니다. 프로덕션 사이트의 중단으로 인해 재해 복구 절차를 수행해야 하는 경우 이러한 중단이 이미 발생했을 수 있습니다.
   
   b. 선택한 백업 ID에 해당하는 스냅숏 또는 저장소 스냅숏 이름이 재해 복구 볼륨에 복원됩니다.
   
   다. 복원 후에는 재해 복구 볼륨을 재해 복구 지역의 HANA 대규모 인스턴스 단위에 탑재할 수 있습니다.
      
1. 재해 복구 볼륨을 재해 복구 사이트의 HANA 대규모 인스턴스 단위에 탑재하십시오. 
1. 유휴 SAP HANA 프로덕션 인스턴스를 시작합니다.
1. RPO 시간을 줄이기 위해 트랜잭션 로그 백업 로그를 복사하도록 선택한 경우 해당 트랜잭션 로그 백업을 새로 탑재된 DR/hana/logbackups 디렉터리에 병합해야 합니다. 기존 백업을 덮어쓰지 마십시오. 저장소 스냅숏의 최신 복제를 사용하여 복제되지 않은 최신 백업만 복사합니다.
1. DR Azure 지역의 /hana/shared/PRD 볼륨에 복제된 스냅숏에서 단일 파일을 복원할 수도 있습니다.

단계의 다음 시퀀스에서는 복원된 저장소 스냅숏 및 사용 가능한 트랜잭션 로그 백업에 따라 SAP HANA 프로덕션 인스턴스가 복구됩니다.

1. SAP HANA Studio를 사용하여 백업 위치를 **/hana/logbackups**로 변경합니다.
   ![DR 복구를 위한 백업 위치 변경](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA는 백업 파일 위치를 검색하고, 가장 최근 트랜잭션 로그 백업을 복원할 것을 제안합니다. 아래와 같은 화면이 표시될 때까지 검사하는 데 몇 분 정도가 소요될 수 있습니다. ![DR 복구를 위한 트랜잭션 로그 백업 목록](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 일부 기본 설정을 조정합니다.

      - **델타 백업 사용** 선택을 취소합니다.
      - **로그 영역 초기화**를 선택합니다.

   ![로그 영역 초기화 설정](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. **마침**을 선택합니다.

   ![DR 복원 완료](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

여기에 표시된 것처럼 진행률 창이 표시됩니다. 해당 예제는 3노드 스케일 아웃 SAP HANA 구성의 재해 복구 복원에 대한 것입니다.

![복원 진행률](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

복원이 **마침** 화면에서 중단된 것처럼 보이고 진행률 화면이 표시되지 않는 경우 작업자 노드의 모든 SAP HANA 인스턴스가 실행되고 있는지 확인합니다. 필요한 경우 SAP HANA 인스턴스를 수동으로 시작합니다.


## <a name="failback-from-a-dr-to-a-production-site"></a>DR에서 프로덕션 사이트로 장애 복구(Failback)
DR에서 프로덕션 사이트로 장애 복구(Failback)할 수 있습니다. 재해 복구 사이트로의 장애 조치가 손실된 데이터를 복구할 필요가 아닌 프로덕션 Azure 지역의 문제로 인해 발생한 시나리오를 살펴보겠습니다. 재해 복구 사이트에서 한동안 SAP 프로덕션 워크로드를 실행했습니다. 프로덕션 사이트의 문제가 해결되면 프로덕션 사이트로 장애 복구(Failback)하려고 할 수 있습니다. 데이터가 손실되도록 할 수는 없으므로 프로덕션 사이트로 돌아가는 단계는 Azure 운영 팀의 SAP HANA와의 몇 가지 단계 및 긴밀한 협조가 필요합니다. 문제가 해결되었을 때 운영 팀이 프로덕션 사이트로 다시 동기화를 시작하도록 트리거하는 것은 사용자의 몫입니다.

수행할 단계의 시퀀스입니다.

1. Azure 운영 팀의 SAP HANA는 프로덕션 상태를 나타내는 재해 복구 저장소 볼륨에서 프로덕션 저장소 볼륨을 동기화하는 트리거를 가져옵니다. 이 상태에서 프로덕션 사이트에 있는 HANA 큰 인스턴스 단위가 종료됩니다.
1. Azure 운영 팀의 SAP HANA는 복제를 모니터링하고 사용자에게 알리기 전에 인식해야 합니다.
1. 재해 복구 사이트에서 프로덕션 HANA 인스턴스를 사용하는 응용 프로그램을 종료합니다. 그런 다음, HANA 트랜잭션 로그 백업을 수행합니다. 다음으로 재해 복구 사이트의 HANA 대규모 인스턴스 단위에서 실행되는 HANA 인스턴스를 중지합니다.
1. 재해 복구 사이트의 HANA 대규모 인스턴스 단위에서 실행 중인 HANA 인스턴스가 종료된 후에 운영 팀은 수동으로 디스크 볼륨을 다시 동기화합니다.
1. Azure 운영 팀의 SAP HANA가 프로덕션 사이트에서 HANA 큰 인스턴스 단위를 다시 시작한 후 사용자에게 전달합니다. HANA 대규모 인스턴스 단위의 시작 시간에 SAP HANA 인스턴스가 종료된 상태인지 확인합니다.
1. 이전에 재해 복구 사이트로 장애 조치(failover)를 수행할 때와 동일한 데이터베이스 복원 단계를 수행합니다.

## <a name="monitor-disaster-recovery-replication"></a>재해 복구 복제 모니터링

`azure_hana_replication_status.pl` 스크립트를 실행하여 저장소 복제 진행 상태를 모니터링할 수 있습니다. 이 스크립트를 재해 복구 위치에서 실행되는 단위에서 실행하여 예상되는 대로 작동시켜야 합니다. 이 스크립트는 복제가 활성 상태인지 여부에 관계없이 작동합니다. 이 스크립트는 재해 복구 위치에서 테넌트의 모든 HANA 큰 인스턴스 단위에 대해 실행할 수 있습니다. 부팅 볼륨에 대한 세부 정보를 가져오는 데는 사용할 수 없습니다.

이 명령을 사용하여 스크립트를 호출합니다.
```
./azure_hana_replication_status.pl
```

출력은 볼륨별로 다음 섹션으로 세분화됩니다.  

- 연결 상태
- 현재 복제 작업
- 마지막에 복제된 스냅숏 
- 최신 스냅숏의 크기
- 스냅숏 간 현재 지연 시간(마지막으로 완료된 스냅숏 복제와 현재 간)

위치 간의 연결이 끊어지지 않고 현재 장애 조치(failover) 이벤트가 진행 중인 경우 연결 상태는 **활성**으로 표시됩니다. 복제 작업은 현재 복제 중이거나 유휴 상태인 데이터가 있는지 여부 또는 현재 연결에서 수행되고 있는 다른 작업이 있는지를 확인합니다. 마지막에 복제된 스냅숏은 `snapmirror…`로만 표시됩니다. 그런 후 마지막 스냅숏의 크기가 표시됩니다. 마지막으로 지연 시간이 표시됩니다. 지연 시간은 예약된 복제부터 복제가 완료될 때까지의 시간을 나타냅니다. 복제가 시작된 경우에도 데이터 복제의 지연 시간은 1시간을 초과할 수 있습니다(특히 초기 복제에서). 지연 시간은 진행 중인 복제가 완료될 때까지 계속 증가합니다.

출력의 예제는 다음과 같습니다.

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```

**다음 단계**
- [HANA 쪽에서 모니터링 및 문제 해결](hana-monitor-troubleshoot.md)을 참조하세요.
