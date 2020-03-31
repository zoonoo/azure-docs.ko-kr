---
title: Azure VM에서 백업된 SAP HANA 데이터베이스 관리
description: 이 문서에서는 Azure 가상 컴퓨터에서 실행 중인 SAP HANA 데이터베이스를 관리하고 모니터링하기 위한 일반적인 작업을 알아봅니다.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480065"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>백업한 SAP HANA 데이터베이스 관리 및 모니터링

이 문서에서는 Azure 가상 시스템(VM)에서 실행되고 [Azure 백업](https://docs.microsoft.com/azure/backup/backup-overview) 서비스 에서 Azure 백업 복구 서비스 자격 증명 모음에 백업되는 SAP HANA 데이터베이스를 관리하고 모니터링하는 일반적인 작업에 대해 설명합니다. 작업 및 경고를 모니터링하고, 온디맨드 백업을 트리거하고, 정책을 편집하고, 데이터베이스 보호를 중지 및 다시 시작하고, 백업에서 VM을 등록 취소하는 방법을 알아봅니다.

SAP HANA 데이터베이스에 대해 아직 백업을 구성하지 않은 경우 [Azure VM에서 SAP HANA 데이터베이스 백업을](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)참조하십시오.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>포털에서 수동 백업 작업 모니터링

Azure Backup은 Azure 포털의 **백업 작업** 섹션에서 수동으로 트리거된 모든 작업을 표시합니다.

![백업 작업 섹션](./media/sap-hana-db-manage/backup-jobs.png)

이 포털에 표시되는 작업에는 데이터베이스 검색 및 등록, 백업 및 복원 작업이 포함됩니다. 로그 백업을 포함한 예약된 작업은 이 섹션에 표시되지 않습니다. SAP HANA 네이티브 클라이언트 (스튜디오 / 조종석 / DBA 조종석)에서 수동으로 트리거 된 백업도 여기에 표시되지 않습니다.

![백업 작업 목록](./media/sap-hana-db-manage/backup-jobs-list.png)

모니터링에 대해 자세히 알아보려면 [Azure 포털의 모니터링](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) 및 [Azure 모니터를 사용한 모니터링으로](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)이동하십시오.

## <a name="view-backup-alerts"></a>백업 경고 보기

경고는 SAP HANA 데이터베이스의 백업을 모니터링하는 쉬운 수단입니다. 경고를 사용하면 백업에서 생성하는 수많은 이벤트에서 길을 잃지 않고 가장 관심 있는 이벤트에 집중할 수 있습니다. Azure Backup을 사용하면 경고를 설정할 수 있으며 다음과 같이 모니터링할 수 있습니다.

* [Azure 포털에](https://portal.azure.com/)로그인합니다.
* 볼트 대시보드에서 **백업 경고를 선택합니다.**

  ![볼트 대시보드의 백업 경고](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* 경고를 볼 수 있습니다.

  ![백업 경고 목록](./media/sap-hana-db-manage/backup-alerts-list.png)

* 자세한 내용을 보려면 경고를 클릭하십시오.

  ![경고 세부 정보](./media/sap-hana-db-manage/alert-details.png)

오늘날 Azure Backup은 전자 메일을 통해 경고를 보낼 수 있도록 합니다. 이러한 경고는 다음과 같습니다.

* 모든 백업 오류에 대해 트리거됩니다.
* 오류 코드로 데이터베이스 수준에서 통합됩니다.
* 데이터베이스의 첫 번째 백업 실패에 대해서만 전송됩니다.

To모니터링에 대해 자세히 알아보려면 [Azure 포털의 모니터링](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) 및 [Azure 모니터를 사용하여 모니터링으로](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)이동합니다.

## <a name="management-operations"></a>관리 작업

Azure Backup을 사용하면 지원되는 풍부한 관리 작업을 통해 백업된 SAP HANA 데이터베이스를 쉽게 관리할 수 있습니다. 이러한 작업은 다음 섹션에서 자세히 설명합니다.

### <a name="run-an-on-demand-backup"></a>주문형 백업 실행

백업은 정책 일정에 따라 실행됩니다. 다음과 같이 주문형 백업을 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목에서**SAP HANA 데이터베이스를 실행하는 VM을 선택한 다음 **지금 백업을**클릭합니다.
3. **지금 백업에서**캘린더 컨트롤을 사용하여 복구 지점을 유지해야 하는 마지막 날을 선택합니다. 그런 다음 **확인**을 클릭합니다.
4. 포털 알림을 모니터링합니다. **백업 작업** > 진행 중인 > 볼트 대시보드에서 작업**진행률을 모니터링할**수 있습니다. 데이터베이스 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

### <a name="hana-native-client-integration"></a>HANA 네이티브 클라이언트 통합

이제 HANA 네이티브 클라이언트에서 트리거된 온디맨드 전체 백업이 **백업 항목** 페이지에 전체 백업으로 표시됩니다.

![마지막 백업 실행](./media/sap-hana-db-manage/last-backups.png)

이러한 임시 전체 백업은 복원을 위한 복원 지점 목록에도 표시됩니다.

![복원 점 목록](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Azure 백업을 사용하도록 설정한 데이터베이스에서 SAP HANA 네이티브 클라이언트 백업 실행

Azure Backup으로 백업되는 데이터베이스의 로컬 백업(HANA Studio/Cockpit 사용)을 수행하려면 다음을 수행합니다.

1. 데이터베이스가 완료될 때까지 전체 백업 또는 로그 백업을 기다립니다. SAP HANA 스튜디오/ 조종석에서 상태를 확인합니다.
2. 로그 백업을 사용하지 않도록 설정하고 관련 데이터베이스의 백업 카탈로그를 파일 시스템으로 설정합니다.
3. 이렇게 하려면 **systemdb** > **구성** > **선택 데이터베이스** > **필터(로그)를**두 번 클릭합니다.
4. **enable_auto_log_backup** **아니오로 설정합니다.**
5. **log_backup_using_backint** **false로 설정합니다.**
6. 데이터베이스의 온디맨드 전체 백업을 수행합니다.
7. 전체 백업 및 카탈로그 백업이 완료될 때까지 기다립니다.
8. 이전 설정을 Azure의 설정으로 되돌리.
   * **enable_auto_log_backup** **예로 설정합니다.**
   * **log_backup_using_backint** **true로 설정합니다.**

### <a name="change-policy"></a>정책 변경

SAP HANA 백업 항목에 대한 기본 정책을 변경할 수 있습니다.

* 볼트 대시보드에서 백업 **항목으로**이동합니다.

  ![백업 항목 선택](./media/sap-hana-db-manage/backup-items.png)

* **Azure VM에서 SAP HANA** 선택

  ![Azure VM에서 SAP HANA 선택](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 변경할 기본 정책이 있는 백업 항목을 선택합니다.
* 기존 백업 정책을 클릭합니다.

  ![기존 백업 정책 선택](./media/sap-hana-db-manage/existing-backup-policy.png)

* 목록에서 선택하여 정책을 변경합니다. 필요한 경우 [새 백업 정책을 만듭니다.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)

  ![드롭다운 목록에서 정책 선택](./media/sap-hana-db-manage/choose-backup-policy.png)

* 변경 내용 저장

  ![변경 내용 저장](./media/sap-hana-db-manage/save-changes.png)

* 정책 수정은 관련된 모든 백업 항목에 영향을 미치고 해당 **구성 보호** 작업을 트리거합니다.

>[!NOTE]
> 보존 기간의 모든 변경 사항은 새 복구 지점 외에 모든 이전 복구 지점에 소급 적용됩니다.
>
> SAP HANA 데이터베이스에는 증분 백업 정책을 사용할 수 없습니다. 증분 백업은 현재 이러한 데이터베이스에 대해 지원되지 않습니다.

### <a name="modify-policy"></a>정책 수정

백업 유형, 빈도 및 보존 범위를 변경하려면 정책을 수정합니다.

>[!NOTE]
>보존 기간의 모든 변경 사항은 새 복구 지점 외에 모든 이전 복구 지점에 소급 적용됩니다.

1. 볼트 대시보드에서 > **백업 정책 관리로** 이동하여 편집할 정책을 선택합니다.

   ![편집할 정책을 선택합니다.](./media/sap-hana-db-manage/manage-backup-policies.png)

1. **수정**을 선택합니다.

   ![수정 선택](./media/sap-hana-db-manage/modify-policy.png)

1. 백업 유형에 대한 빈도를 선택합니다.

   ![백업 빈도 선택](./media/sap-hana-db-manage/choose-frequency.png)

정책 수정은 관련된 모든 백업 항목에 영향을 미치고 해당 **구성 보호** 작업을 트리거합니다.

### <a name="inconsistent-policy"></a>일관되지 않은 정책

경우에 따라 수정 정책 작업으로 인해 일부 백업 항목에 대해 정책 버전이 **일치하지 않는** 경우가 있습니다. 이 문제는 수정 정책 작업이 트리거된 후 백업 항목에 대해 해당 **구성 보호** 작업이 실패할 때 발생합니다. 백업 항목 보기에서 다음과 같이 나타납니다.

![일관되지 않은 정책](./media/sap-hana-db-manage/inconsistent-policy.png)

영향을 받은 모든 항목에 대한 정책 버전을 한 번의 클릭으로 수정할 수 있습니다.

![정책 버전 수정](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA 데이터베이스 보호 중지

다음과 같은 몇 가지 방법으로 SAP HANA 데이터베이스 보호를 중지할 수 있습니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
* 미래의 모든 백업 작업을 중지하고 복구 지점을 원래 상태로 유지

복구 지점을 그대로 두기로 선택하는 경우 다음 세부 정보를 염두에 두어야 합니다.

* 모든 복구 지점은 영구적으로 유지되고, 모든 정리는 데이터 보관을 통해 보호 중지에서 중지됩니다.
* 보호된 인스턴스와 사용 된 저장소에 대 한 요금이 청구 됩니다. 자세한 내용은 [Azure 백업 가격 책정을](https://azure.microsoft.com/pricing/details/backup/)참조하십시오.
* 백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 실패합니다.

데이터베이스에 대한 보호를 중지하려면:

* 볼트 대시보드에서 **백업 항목을 선택합니다.**
* **백업 관리 유형에서** **Azure VM에서 SAP HANA를** 선택합니다.

  ![Azure VM에서 SAP HANA 선택](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 보호를 중지할 데이터베이스를 선택합니다.

  ![보호를 중지할 데이터베이스 선택](./media/sap-hana-db-manage/select-database.png)

* 데이터베이스 메뉴에서 **백업 중지**를 선택합니다.

  ![백업 중지 선택](./media/sap-hana-db-manage/stop-backup.png)

* 백업 **중지** 메뉴에서 데이터를 유지하거나 삭제할지 여부를 선택합니다. 원하는 경우 이유와 의견을 제공하십시오.

  ![데이터 보존 또는 삭제 선택](./media/sap-hana-db-manage/retain-backup-data.png)

* **백업 중지를**선택합니다.

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA 데이터베이스에 대한 보호 재개

SAP HANA 데이터베이스에 대한 보호를 중지하면 **백업 데이터 유지** 옵션을 선택하면 나중에 보호를 다시 시작할 수 있습니다. 백업된 데이터를 유지하지 않으면 보호를 다시 시작할 수 없습니다.

SAP HANA 데이터베이스에 대한 보호를 다시 시작하려면 다음을 수행하십시오.

* 백업 항목을 열고 **백업 다시 시작을**선택합니다.

   ![백업 다시 시작](./media/sap-hana-db-manage/resume-backup.png)

* **백업 정책** 메뉴에서 정책을 선택한 다음, **저장**을 클릭합니다.

### <a name="upgrading-from-sap-hana-10-to-20"></a>SAP HANA 1.0에서 2.0으로 업그레이드

[SAP HANA 1.0에서 2.0으로 업그레이드한 후 SAP HANA](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)데이터베이스에 대한 백업을 계속하는 방법을 알아봅니다.

### <a name="upgrading-without-a-sid-change"></a>SID 변경 없이 업그레이드

[업그레이드 후 SID가 변경되지 않은](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)SAP HANA 데이터베이스의 백업을 계속하는 방법에 대해 알아봅니다.

### <a name="unregister-an-sap-hana-instance"></a>SAP HANA 인스턴스 등록 취소

보호를 비활성화한 후 자격 증명 모음을 삭제하기 전에 SAP HANA 인스턴스등록을 취소합니다.

* 볼트 대시보드에서 **관리**에서 **백업 인프라를**선택합니다.

   ![백업 인프라 선택](./media/sap-hana-db-manage/backup-infrastructure.png)

* **Azure VM에서 워크로드로** **백업 관리 유형을 선택합니다.**

   ![Azure VM에서 워크로드로 백업 관리 유형을 선택합니다.](./media/sap-hana-db-manage/backup-management-type.png)

* **보호된 서버에서**등록 취소할 인스턴스를 선택합니다. 볼트를 삭제하려면 모든 서버/인스턴스의 등록을 취소해야 합니다.

* 보호된 인스턴스를 마우스 오른쪽 단추로 클릭하고 **등록 취소를**선택합니다.

   ![등록 취소 선택](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>SAP HANA 서버 VM에서 확장 다시 등록

경우에 따라 VM의 워크로드 확장이 한 가지 이유로 영향을 받을 수 있습니다. 이러한 경우 VM에서 트리거된 모든 작업이 실패하기 시작합니다. 그런 다음 VM에서 확장을 다시 등록해야 할 수 있습니다. 작업을 다시 등록하면 작업을 계속할 수 있도록 VM에 워크로드 백업 확장을 다시 설치합니다.

이 옵션을 신중하게 사용: 이미 정상 확장이 있는 VM에서 트리거될 때 이 작업으로 인해 확장이 다시 시작됩니다. 이로 인해 진행 중인 모든 작업이 실패할 수 있습니다. 다시 등록 작업을 트리거하기 전에 하나 이상의 [증상을](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) 확인합니다.

## <a name="next-steps"></a>다음 단계

* [SAP HANA 데이터베이스를 백업할 때 일반적인 문제를 해결하는](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot) 방법을 알아봅니다.
