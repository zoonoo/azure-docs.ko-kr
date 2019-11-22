---
title: Azure Backup를 사용 하 여 Azure에 SAP HANA 데이터베이스 백업
description: 이 문서에서는 Azure Backup 서비스를 사용 하 여 Azure virtual machines에 SAP HANA 데이터베이스를 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ed47f18c9dabc685d6fbe02804562ef86a93190a
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285897"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Azure Vm에서 SAP HANA 데이터베이스 백업

SAP HANA 데이터베이스는 낮은 RPO (복구 지점 목표) 및 장기 보존이 필요한 중요 한 워크 로드입니다. [Azure Backup](backup-overview.md)를 사용 하 여 Azure vm (가상 머신)에서 실행 되는 SAP HANA 데이터베이스를 백업할 수 있습니다.

이 문서에서는 Azure Vm에서 실행 되는 SAP HANA 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업 하는 방법을 보여 줍니다.

이 아티클에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 자격 증명 모음 만들기 및 구성
> * 데이터베이스 검색
> * 백업 구성
> * 주문형 백업 작업 실행

## <a name="prerequisites"></a>선행 조건

백업할 데이터베이스를 설정 하려면 [필수 구성 요소](tutorial-backup-sap-hana-db.md#prerequisites) 및 [권한 설정](tutorial-backup-sap-hana-db.md#setting-up-permissions) 섹션을 참조 하세요.

### <a name="set-up-network-connectivity"></a>네트워크 연결 설정

모든 작업에 대해 SAP HANA VM에는 Azure 공용 IP 주소에 대 한 연결이 필요 합니다. VM 작업 (데이터베이스 검색, 백업 구성, 백업 예약, 복구 지점의 복원 등)은 연결 없이 작동할 수 없습니다. Azure 데이터 센터 IP 범위에 대 한 액세스를 허용 하 여 연결을 설정 합니다.

* Azure 데이터 센터의 [ip 주소 범위](https://www.microsoft.com/download/details.aspx?id=41653) 를 다운로드 한 다음 이러한 ip 주소에 대 한 액세스를 허용할 수 있습니다.
* NSGs (네트워크 보안 그룹)를 사용 하는 경우 AzureCloud [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 를 사용 하 여 모든 AZURE 공용 IP 주소를 허용할 수 있습니다. [AzureNetworkSecurityRule cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) 을 사용 하 여 nsg 규칙을 수정할 수 있습니다.
* 전송에서 HTTPS를 통해 포트 443을 허용 목록에 추가 해야 합니다.

## <a name="onboard-to-the-public-preview"></a>공개 미리 보기에 등록

다음과 같이 공개 미리 보기로 등록 합니다.

* 포털에서 [이 문서](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)에 따라 Recovery Services 서비스 공급자에 구독 ID를 등록 합니다.
* PowerShell의 경우이 cmdlet을 실행 합니다. "등록 됨"으로 완료 되어야 합니다.

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>데이터베이스 검색

1. 자격 증명 모음의 **시작**에서 **백업**을 클릭 합니다. **워크 로드가 실행 되는 위치**에서 **Azure VM의 SAP HANA**을 선택 합니다.
2. **검색 시작**을 클릭 합니다. 자격 증명 모음 지역에서 보호 되지 않는 Linux Vm의 검색을 시작 합니다.

   * 검색 후 보호 되지 않는 Vm은 이름 및 리소스 그룹 별로 나열 된 포털에 표시 됩니다.
   * VM이 예상 대로 표시 되지 않으면 이미 자격 증명 모음에 백업 되어 있는지 확인 합니다.
   * 여러 Vm은 동일한 이름을 가질 수 있지만 다른 리소스 그룹에 속해 있습니다.

3. **Virtual Machines 선택**에서 링크를 클릭 하 여 Azure Backup 서비스에서 데이터베이스 검색에 대 한 SAP HANA vm에 액세스할 수 있는 권한을 제공 하는 스크립트를 다운로드 합니다.
4. 백업 하려는 SAP HANA 데이터베이스를 호스트 하는 각 VM에 대해 스크립트를 실행 합니다.
5. Vm에서 스크립트를 실행 한 후 **Virtual Machines 선택**에서 vm을 선택 합니다. 그런 다음 **Db 검색**을 클릭 합니다.
6. Azure Backup VM의 모든 SAP HANA 데이터베이스를 검색 합니다. 검색 하는 동안 Azure Backup는 자격 증명 모음으로 VM을 등록 하 고 VM에 확장을 설치 합니다. 데이터베이스에 에이전트가 설치 되어 있지 않습니다.

    ![SAP HANA 데이터베이스 검색](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>백업 구성  

이제 백업을 사용 하도록 설정 합니다.

1. 2 단계에서 **백업 구성**을 클릭 합니다.

    ![백업 구성](./media/backup-azure-sap-hana-database/configure-backup.png)
2. **백업할 항목 선택**에서 보호 하려는 모든 데이터베이스 > **확인**을 선택 합니다.

    ![백업할 항목 선택](./media/backup-azure-sap-hana-database/select-items.png)
3. 백업 **정책** > 에서 **백업 정책을 선택**하 고 아래 지침에 따라 데이터베이스에 대 한 새 백업 정책을 만듭니다.

    ![백업 정책 선택](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 정책을 만든 후 **백업** 메뉴에서 **백업 사용**을 클릭 합니다.

    ![백업 사용](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 포털의 **알림** 영역에서 백업 구성 진행률을 추적 합니다.

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행 되는 시기와 보존 기간을 정의 합니다.

* 정책은 자격 증명 모음 수준에서 만들어집니다.
* 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.

정책 설정을 다음과 같이 지정 합니다.

1. **정책 이름**에 새 정책의 이름을 입력합니다.

   ![정책 이름 입력](./media/backup-azure-sap-hana-database/policy-name.png)
2. **전체 백업 정책**에서 **백업 빈도**를 선택하고, **매일** 또는 **매주**를 선택합니다.
   * **매일**: 백업 작업이 시작 되는 시간 및 표준 시간대를 선택 합니다.
       * 전체 백업을 실행 해야 합니다. 이 옵션은 해제할 수 없습니다.
       * **전체 백업**을 클릭하여 정책을 확인합니다.
       * 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.
   * **매주**: 백업 작업이 실행 되는 요일, 시간 및 표준 시간대를 선택 합니다.

   ![백업 빈도 선택](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. **보존 범위**에서 전체 백업에 대 한 보존 설정을 구성 합니다.
    * 기본적으로 모든 옵션이 선택 되어 있습니다. 사용 하지 않을 보존 범위 제한을 모두 선택 취소 하 고 수행 하는 범위를 설정 합니다.
    * 모든 백업 유형에 대 한 최소 보존 기간은 7 일입니다.
    * 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    * 특정 날짜에 대 한 백업은 주간 보존 범위 및 설정에 따라 태그가 지정 되 고 보존 됩니다.
    * 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.

4. **전체 백업 정책** 메뉴에서 **확인** 을 클릭 하 여 설정을 적용 합니다.
5. 차등 **백업** 을 선택 하 여 차등 정책을 추가 합니다.
6. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다.
    * 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.
    * 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 오래 보존해야 하는 경우에는 전체 백업을 사용해야 합니다.

    ![차등 백업 정책](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 증분 백업은 현재 지원 되지 않습니다.

7. **확인** 을 클릭 하 여 정책을 저장 하 고 주 **백업 정책** 메뉴로 돌아갑니다.
8. **로그 백업** 을 선택 하 여 트랜잭션 로그 백업 정책 추가
    * **로그 백업**에서 **사용**을 선택 합니다.  SAP HANA 모든 로그 백업을 관리 하므로이 기능을 사용 하지 않도록 설정할 수 없습니다.
    * 빈도 및 보존 제어를 설정 합니다.

    > [!NOTE]
    > 로그 백업은 전체 백업이 성공적으로 완료 된 후에만 flow를 시작 합니다.

9. **확인** 을 클릭 하 여 정책을 저장 하 고 주 **백업 정책** 메뉴로 돌아갑니다.
10. 백업 정책 정의를 완료 한 후 **확인**을 클릭 합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

백업은 정책 일정에 따라 실행 됩니다. 다음과 같이 요청 시 백업을 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목**에서 SAP HANA 데이터베이스를 실행 하는 VM을 선택 하 고 **지금 Backup**을 클릭 합니다.
3. **지금 백업**에서 달력 컨트롤을 사용 하 여 복구 지점을 유지할 마지막 날을 선택 합니다. 그런 후 **OK**를 클릭합니다.
4. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드 > **백업 작업** > **진행 중**에서 작업 진행률을 모니터링할 수 있습니다. 데이터베이스의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Azure Backup 사용 하도록 설정 된 데이터베이스에서 SAP HANA Studio 백업 실행

Azure Backup를 사용 하 여 백업 되는 데이터베이스의 로컬 백업 (HANA Studio 사용)을 수행 하려면 다음을 수행 합니다.

1. 데이터베이스에 대 한 전체 또는 로그 백업이 완료 될 때까지 기다립니다. SAP HANA Studio/환경에서 상태를 확인 합니다.
2. 로그 백업을 사용 하지 않도록 설정 하 고, 관련 데이터베이스에 대 한 백업 카탈로그를 파일 시스템으로 설정 합니다.
3. 이렇게 하려면 **systemdb** > **구성** >  > **데이터베이스 선택** **필터 (로그)** 를 차례로 선택 합니다.
4. **Enable_auto_log_backup** 를 **아니요**로 설정 합니다.
5. **Log_backup_using_backint** 을 **False**로 설정 합니다.
6. 데이터베이스의 주문형 전체 백업을 수행 합니다.
7. 전체 백업 및 카탈로그 백업이 완료 될 때까지 기다립니다.
8. 이전 설정을 Azure에 대 한 설정을 다시 되돌립니다.
    * **Enable_auto_log_backup** 를 **예**로 설정 합니다.
    * **Log_backup_using_backint** 를 **True**로 설정 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure vm에서 실행 되는 SAP HANA 데이터베이스를 복원](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) 하는 방법 알아보기
* 을 [사용 하 여 백업 되는 SAP HANA 데이터베이스를 관리](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) 하는 방법을 알아봅니다 Azure Backup
