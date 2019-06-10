---
title: SAP HANA on Azure (큰 인스턴스)에 대 한 재해 사이트로 장애 조치 절차 HANA | Microsoft Docs
description: SAP HANA on Azure (큰 인스턴스)에 대 한 재해 복구 사이트로 장애 조치를 수행 하는 방법
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
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7d4f6216b4a57796ab5c0296713316dd97c47a8
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987882"
---
# <a name="disaster-recovery-failover-procedure"></a>재해 복구 장애 조치(failover) 프로시저


>[!IMPORTANT]
>이 문서에서는 SAP HANA 관리 설명서 또는 SAP 노트를 대체 하지 않습니다. SAP HANA 관리 및 작업, 특히 백업, 복원, 고가용성 및 재해 복구 (DR)에 대 한 전문을 확실 하 게 이해 해야 하는 것이 예정입니다. 이 문서에서는 SAP HANA Studio의 스크린샷이 나와 있습니다. SAP 관리 도구 스크린의 콘텐츠, 구조 및 특성과 도구 자체는 릴리스할 SAP HANA 릴리스마다 변경될 수 있습니다.

두 경우에 DR 사이트로 조치할 있습니다 때 고려해 야 합니다.

- SAP HANA 데이터베이스를 최신 상태의 데이터로 되돌려야 합니다. 이 경우 Microsoft에 문의할 필요 없이 장애 조치를 수행할 수 있는 셀프 서비스 스크립트가 있습니다. 장애 복구에 대해 Microsoft와 협력 해야 합니다.
- 최신 복제 된 스냅숏이 아닌 저장소 스냅숏으로 복원 하려고 합니다. 이 경우에 Microsoft로 작업해야 합니다. 

>[!NOTE]
>다음 단계는 DR 단위를 나타내는 HANA 큰 인스턴스 단위에서 수행 되어야 합니다. 
 
최신 복제 된 저장소 스냅숏을 복원 하려면 "수행 DR 장애 조치-azure_hana_dr_failover를 전체"의 단계에 따라 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 

여러 SAP HANA 인스턴스를 장애 조치 하려는 경우 여러 번 azure_hana_dr_failover 명령을 실행 합니다. 요청 하는 경우 장애 조치 하 고 복원 하려는 SAP HANA SID를 입력 합니다. 


실제 복제 관계에 영향을 주지 않고 DR 장애 조치에도 테스트할 수 있습니다. 테스트 장애 조치를 수행 하려면 "수행 된 테스트 DR 장애 조치-azure_hana_test_dr_failover"의 단계에 따라 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 

>[!IMPORTANT]
>수행할 *되지* 과정을 통해 DR 사이트에서 만든 인스턴스에서 프로덕션 트랜잭션을 실행할 **테스트 장애 조치**합니다. 명령 azure_hana_test_dr_failover 기본 사이트에 아무 관계도 없는 볼륨 세트를 만듭니다. 따라서 기본 사이트로 다시 동기화할 수 *없습니다*. 

여러 SAP HANA 인스턴스를 테스트 하려는 경우 스크립트를 여러 번 실행 합니다. 요청 하는 경우 장애 조치에 대 한 테스트 하려는 인스턴스의 SAP HANA SID를 입력 합니다. 

>[!NOTE]
>시간 전에 삭제 된 일부 데이터를 복구 하 고 DR 볼륨을 이전 스냅숏으로 설정 해야 하는 DR 사이트로 장애 조치 해야 할 경우이 절차에 적용 됩니다. 

1. 실행 중인 HANA 큰 인스턴스의 재해 복구 단위에서 HANA의 비프로덕션 인스턴스를 종료 합니다. 유휴 HANA 프로덕션 인스턴스가 미리 설치 됩니다.
1. 실행 중인 SAP HANA 프로세스가 없는지 확인해야 합니다. 이 확인을 위해 다음 명령을 사용 합니다.

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      출력에는 **hdbdaemon** 프로세스가 중지된 상태로 표시되고, 실행 중이거나 시작된 상태의 다른 HANA 프로세스는 표시되지 않습니다.
1. 재해 복구 사이트를 복원하려는 스냅샷 이름 또는 SAP HANA 백업 ID를 결정합니다. 실제 재해 복구의 경우 이 스냅샷이 일반적으로 최신 스냅샷입니다. 손실된 데이터를 복구해야 하는 경우 이전 스냅샷을 선택하십시오.
1. 우선 순위가 높은 지원 요청을 통해 Azure 지원에 문의합니다. 스냅숏 또는 DR 사이트에서 HANA 백업 ID의 날짜 및 이름 사용 하 여 해당 스냅숏의 복원에 대 한 요청 합니다. 기본값은 작업 쪽에서 /hana/data 볼륨만 복원하는 것입니다. Hana/logbackups 볼륨 너무 하도록 하려는 경우에 구체적으로 명시 해야 합니다. */hana/shared 볼륨을 복원하지 마십시오.* 대신 global.ini 특정 파일을 선택 합니다 **.snapshot** PRD 용 /hana/shared 볼륨 디렉터리 및 하위 디렉터리를 다시 탑재/hana/후 합니다. 

   작업 쪽에서 다음 단계가 수행됩니다.

   a. 프로덕션 볼륨에서 재해 복구 볼륨으로의 스냅샷 복제는 중지됩니다. 프로덕션 사이트의 중단으로 인해 재해 복구 절차를 수행해야 하는 경우 이러한 중단이 이미 발생했을 수 있습니다.
   
   b. 선택한 백업 ID에 해당하는 스냅샷 또는 저장소 스냅샷 이름이 재해 복구 볼륨에 복원됩니다.
   
   다. 복원 후에는 재해 복구 볼륨을 재해 복구 지역의 HANA 대규모 인스턴스 단위에 탑재할 수 있습니다.
      
1. 재해 복구 볼륨을 재해 복구 사이트의 HANA 대규모 인스턴스 단위에 탑재하십시오. 
1. 유휴 SAP HANA 프로덕션 인스턴스를 시작합니다.
1. RPO 시간을 줄이기 위해 트랜잭션 로그 백업 로그를 복사 하기로 선택한 경우 트랜잭션 로그 백업을 새로 탑재 한 DR/hana/logbackups 디렉터리에 병합 합니다. 기존 백업을 덮어쓰지 마십시오. 저장소 스냅숏의 최신 복제로 복제 되지 않은 최신 백업만 복사 합니다.
1. 또한 DR Azure 지역의 /hana/shared/PRD 볼륨에 복제 되지 않은 단일 파일을 복원할 수 있습니다.

다음 단계를 기반으로 복원 된 저장소 스냅숏 및 트랜잭션 로그 백업을 사용할 수 있는 SAP HANA 프로덕션 인스턴스가 복구 하는 방법을 보여 줍니다.

1. SAP HANA Studio를 사용하여 백업 위치를 **/hana/logbackups**로 변경합니다.

   ![DR 복구를 위한 백업 위치 변경](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA는 백업 파일 위치를 검색하고, 가장 최근 트랜잭션 로그 백업을 복원할 것을 제안합니다. 검사는 다음이 표시와 같은 화면이 될 때까지 몇 분을 사용할 수 있습니다.

   ![DR 복구를 위한 트랜잭션 로그 백업 목록](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. 일부 기본 설정을 조정합니다.

      - **델타 백업 사용** 선택을 취소합니다.
      - **로그 영역 초기화**를 선택합니다.

   ![로그 영역 초기화 설정](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. **마침**을 선택합니다.

   ![DR 복원 완료](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

여기에 표시된 것처럼 진행률 창이 표시됩니다. 해당 예제는 3노드 스케일 아웃 SAP HANA 구성의 재해 복구 복원에 대한 것입니다.

![복원 진행률](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

복원에서 응답을 중지 하는 경우는 **완료** 화면 하지 진행률 화면이 표시를 작업자 노드의 모든 SAP HANA 인스턴스가 실행 되 고 있는지 확인 합니다. 필요한 경우 SAP HANA 인스턴스를 수동으로 시작합니다.


## <a name="failback-from-a-dr-to-a-production-site"></a>DR에서 프로덕션 사이트로 장애 복구(Failback)
DR에서 프로덕션 사이트로 장애 복구(Failback)할 수 있습니다. 재해 복구 사이트로의 장애 조치가 손실된 데이터를 복구할 필요가 아닌 프로덕션 Azure 지역의 문제로 인해 발생한 시나리오를 살펴보겠습니다. 

실행 했습니다 SAP 프로덕션 워크 로드 동안 재해 복구 사이트에서. 프로덕션 사이트의 문제가 해결되면 프로덕션 사이트로 장애 복구(Failback)하려고 할 수 있습니다. 데이터가 손실되도록 할 수는 없으므로 프로덕션 사이트로 돌아가는 단계는 Azure 운영 팀의 SAP HANA와의 몇 가지 단계 및 긴밀한 협조가 필요합니다. 문제가 해결되었을 때 운영 팀이 프로덕션 사이트로 다시 동기화를 시작하도록 트리거하는 것은 사용자의 몫입니다.

다음 단계를 수행하세요.

1. Azure 운영 팀의 SAP HANA는 프로덕션 상태를 나타내는 재해 복구 저장소 볼륨에서 프로덕션 저장소 볼륨을 동기화하는 트리거를 가져옵니다. 이 상태에서 프로덕션 사이트에 있는 HANA 큰 인스턴스 단위가 종료됩니다.
1. Azure 운영 팀의 SAP HANA는 복제를 모니터링 하 고 알리는 이러한 전에 포착은 확인 합니다.
1. 재해 복구 사이트에서 프로덕션 HANA 인스턴스를 사용하는 애플리케이션을 종료합니다. 그런 다음, HANA 트랜잭션 로그 백업을 수행합니다. 다음으로 재해 복구 사이트의 HANA 큰 인스턴스 단위에서 실행 중인 HANA 인스턴스를 중지 합니다.
1. 재해 복구 사이트의 HANA 큰 인스턴스 단위에서 실행 중인 HANA 인스턴스가 종료 된 후 운영 팀은 수동으로 디스크 볼륨을 다시 동기화.
1. Azure 운영 팀의 SAP HANA를 HANA 큰 인스턴스 단위는 프로덕션 사이트에서 다시 시작 됩니다. 이러한 사용자에 게 전달 합니다. SAP HANA 인스턴스가 종료 된 상태에서 HANA 큰 인스턴스 단위의 시작 시간에 수 있도록 합니다.
1. 이전에 재해 복구 사이트로 조치할 때 했던 동일한 데이터베이스 복원 단계를 수행 하 합니다.

## <a name="monitor-disaster-recovery-replication"></a>재해 복구 복제 모니터링

저장소 복제 진행 상태를 모니터링 하려면 스크립트를 실행 `azure_hana_replication_status`합니다. 이 명령은 예상 대로 작동 하는 재해 복구 위치에서 실행 되는 단위에서 실행 되어야 합니다. 이 명령은 복제 활성 상태 인지 여부에 관계 없이 작동 합니다. 재해 복구 위치에서 테 넌 트의 모든 HANA 큰 인스턴스 단위에 대해 명령을 실행할 수 있습니다. 부팅 볼륨에 대 한 정보를 가져오는 데 사용할 수 없습니다. 

이 명령은 해당 출력에 대 한 자세한 내용은 "DR 복제 상태-azure_hana_replication_status 가져오기"에서 참조 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.


## <a name="next-steps"></a>다음 단계
- 참조 [모니터 HANA 쪽에서 문제를 해결 하 고](hana-monitor-troubleshoot.md)입니다.
