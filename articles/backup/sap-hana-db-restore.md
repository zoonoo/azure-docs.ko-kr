---
title: Azure VM에서 SAP HANA 데이터베이스 복원
description: 이 문서에서는 Azure 가상 컴퓨터에서 실행 중인 SAP HANA 데이터베이스를 복원하는 방법을 알아보십시오.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287919"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Azure VM에서 SAP HANA 데이터베이스 복원

이 문서에서는 Azure 백업 서비스가 Azure 백업 복구 서비스 자격 증명 모음에 백업한 Azure 가상 시스템(VM)에서 실행 중인 SAP HANA 데이터베이스를 복원하는 방법을 설명합니다. 복원은 dev/test 시나리오에 대한 데이터의 복사본을 만들거나 이전 상태로 돌아가는 데 사용할 수 있습니다.

SAP HANA 데이터베이스를 백업하는 방법에 대한 자세한 내용은 [Azure VM에서 SAP HANA 데이터베이스 백업을](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)참조하십시오.

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>시간 또는 복구 지점으로 복원

Azure Backup에서는 다음과 같이 Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원할 수 있습니다.

* 로그 백업을 사용하여 특정 날짜 또는 시간(초)으로 복원합니다. Azure Backup은 선택한 시간을 기준으로 복원해야 하는 적절한 전체 차등 백업 및 로그 백업 체인을 자동으로 결정합니다.

* 특정 복구 지점으로 복원하려면 특정 전체 또는 차등 백업을 복원합니다.

## <a name="prerequisites"></a>사전 요구 사항

데이터베이스를 복원하기 전에 다음 을 기록합니다.

* 동일한 Azure 지역에 있는 SAP HANA 인스턴스로만 데이터베이스를 복원할 수 있습니다.

* 대상 인스턴스를 원본과 동일한 자격 증명 모음에 등록해야 합니다.

* Azure Backup은 동일한 VM에서 두 개의 서로 다른 SAP HANA 인스턴스를 식별할 수 없습니다. 따라서 동일한 VM에서 한 인스턴스에서 다른 인스턴스로 데이터를 복원할 수 없습니다.

* 대상 SAP HANA 인스턴스를 복원할 준비가 되었는지 확인하려면 **백업 준비** 상태를 확인합니다.

  * 대상 SAP HANA 인스턴스가 등록된 볼트를 엽니다.

  * 볼트 대시보드에서 시작 하기 에서 **백업을** **선택합니다.**

![볼트 대시보드의 백업](media/sap-hana-db-restore/getting-started-backup.png)

* **백업에서** **백업할 내용** 아래에서 Azure **VM에서 SAP HANA를** 선택합니다.

![Azure VM에서 SAP HANA 선택](media/sap-hana-db-restore/sap-hana-backup.png)

* **VM에서 디스커버 DB에서** **세부 정보 보기를** 클릭합니다.

![세부 정보 보기](media/sap-hana-db-restore/view-details.png)

* 대상 VM의 **백업 준비 검토**

![보호되는 서버](media/sap-hana-db-restore/protected-servers.png)

* SAP HANA가 지원하는 복원 유형에 대한 자세한 내용은 SAP HANA Note [1642148을](https://launchpad.support.sap.com/#/notes/1642148) 참조하십시오.

## <a name="restore-a-database"></a>데이터베이스 복원

* 복원할 SAP HANA 데이터베이스가 등록된 볼트 를 엽니다.

* 볼트 대시보드에서 **보호된 항목**아래에서 **백업 항목을 선택합니다.**

![Backup 항목](media/sap-hana-db-restore/backup-items.png)

* **백업 항목에서** **백업 관리 유형에서** **Azure VM에서 SAP HANA를** 선택합니다.

![백업 관리 유형](media/sap-hana-db-restore/backup-management-type.png)

* 복원할 데이터베이스를 선택합니다.

 ![복원할 데이터베이스](media/sap-hana-db-restore/database-to-restore.png)

* 데이터베이스 메뉴를 검토합니다. 다음과 같은 데이터베이스 백업에 대한 정보를 제공합니다.

  * 가장 오래되고 최신 복원 지점

  * 데이터베이스의 마지막 24시간 및 72시간 동안의 로그 백업 상태

![데이터베이스 메뉴](media/sap-hana-db-restore/database-menu.png)

* **DB 복원** 선택

* **복원 구성에서**데이터를 복원할 위치(또는 방법)를 지정합니다.

  * **대체 위치**: 데이터베이스를 대체 위치로 복원하고 원래 원본 데이터베이스를 유지합니다.

  * **덮어쓰기 DB**: 원본 원본과 동일한 SAP HANA 인스턴스로 데이터를 복원합니다. 이 옵션은 원래 데이터베이스를 덮어씁니다.

![복원 구성](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>대체 위치에 복원

* 구성 **복원** 메뉴에서 **복원 할 위치**아래에서 **대체 위치를**선택합니다.

![대체 위치에 복원](media/sap-hana-db-restore/restore-alternate-location.png)

* 데이터베이스를 복원할 SAP HANA 호스트 이름과 인스턴스 이름을 선택합니다.
* **백업 준비 상태를** 확인하여 대상 SAP HANA 인스턴스를 복원할 준비가 되었는지 확인합니다. 자세한 내용은 [필수 구성 조건 섹션을](#prerequisites) 참조하십시오.
* **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.

> [!NOTE]
> 단일 데이터베이스 컨테이너(SDC) 복원은 이러한 [검사를](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)따라야 합니다.

* 해당하는 경우 **선택한 HANA 인스턴스에 이름이 같은 DB가 이미 있는 경우 덮어쓰기를**선택합니다.
* **확인**을 선택합니다.

![복원 구성 - 최종 화면](media/sap-hana-db-restore/restore-configuration-last.png)

* **복원 지점 선택에서** **로그(시간 시점)를** 선택하여 [특정 시점으로 복원합니다.](#restore-to-a-specific-point-in-time) 또는 [특정 복구 지점으로 복원하려면](#restore-to-a-specific-recovery-point) **전체 & 차동을** 선택합니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

* 구성 **복원** 메뉴에서 **복원 할 위치**아래에서 DB >  **덮어쓰기****확인을**선택합니다.

![DB 덮어쓰기](media/sap-hana-db-restore/overwrite-db.png)

* **복원 지점 선택에서** **로그(시간 시점)를** 선택하여 [특정 시점으로 복원합니다.](#restore-to-a-specific-point-in-time) 또는 [특정 복구 지점으로 복원하려면](#restore-to-a-specific-recovery-point) **전체 & 차동을** 선택합니다.

### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

* 로그 그래프에서 복구 지점을 선택하고 **확인을** 선택하여 복원 지점을 선택합니다.

![복원 지점](media/sap-hana-db-restore/restore-point.png)

* **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.

![복원 선택](media/sap-hana-db-restore/restore-restore.png)

* **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **작업 복원을** 선택하여 복원진행상황을 추적합니다.

![성공적으로 트리거된 복원](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>특정 복구 지점으로 복원

복원 유형으로 **전체 및 차등**을 선택한 경우 다음을 수행합니다.

* 목록에서 복구 지점을 선택하고 **확인을** 선택하여 복원 지점을 선택합니다.

![특정 복구 지점 복원](media/sap-hana-db-restore/specific-recovery-point.png)

* **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.

![작업 복원 시작](media/sap-hana-db-restore/restore-specific.png)

* **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **작업 복원을** 선택하여 복원진행상황을 추적합니다.

![복원 진행률](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> 시스템 DB가 대상 인스턴스로 복원된 후 여러 데이터베이스 컨테이너(MDC)가 복원되면 사전 등록 스크립트를 다시 실행해야 합니다. 그런 다음에만 후속 테넌트 DB 복원이 성공합니다. 자세한 내용은 [문제 해결 - MDC 복원](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)을 참조하십시오.

## <a name="next-steps"></a>다음 단계

* Azure 백업을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 [방법에 대해 알아봅니다.](sap-hana-db-manage.md)
