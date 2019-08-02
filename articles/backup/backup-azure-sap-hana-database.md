---
title: Azure Backup를 사용 하 여 Azure에 SAP HANA 데이터베이스 백업 Microsoft Docs
description: 이 자습서에서는 Azure Backup 서비스를 사용 하 여 Azure에 SAP HANA 데이터베이스를 백업 하는 방법을 설명 합니다.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dacurwin
ms.openlocfilehash: f88555c6a8b3d4122a1a8ef82f58788a46dd5226
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639833"
---
# <a name="back-up-an-sap-hana-database"></a>SAP HANA 데이터베이스 백업

[Azure Backup](backup-overview.md) 는 Azure에 대 한 SAP HANA 데이터베이스의 백업을 지원 합니다.

> [!NOTE]
> 이 기능은 현재 공개 미리 보기로 제공됩니다. 현재는 프로덕션 준비가 되지 않았으므로 SLA가 보장 되지 않습니다. 

## <a name="scenario-support"></a>시나리오 지원

**지원** | **세부 정보**
--- | ---
**지원되는 지역** | 오스트레일리아 남부 동부, 동부 오스트레일리아 <br> 브라질 남부 <br> 캐나다 중부, 캐나다 동부 <br> 남부 동아시아, 동아시아 <br> 미국 동부, 미국 동부 2, 미국 서 부, 미국 서 부, 미국 서 부 2, 미국 중 북부, 미국 중부, 미국 중 북부<br> 인도 중부, 인도 남부 <br> 일본 동부, 일본 서부<br> 한국 중부, 한국 남부 <br> 북유럽, 서유럽 <br> 영국 남부, 영국 서부
**지원 되는 VM 운영 체제** | SLES 12 SP2 또는 SP3.
**지원 되는 HANA 버전** | SDC on HANA 1.x, MDC on HANA 2.x <= SPS03

### <a name="current-limitations"></a>현재 제한 사항

- Azure Vm에서 실행 되는 SAP HANA 데이터베이스만 백업할 수 있습니다.
- Azure Portal SAP HANA 백업만 구성할 수 있습니다. PowerShell, CLI 또는 REST API를 사용 하 여 기능을 구성할 수 없습니다.
- 수직 확장 모드 에서만 데이터베이스를 백업할 수 있습니다.
- 데이터베이스 로그는 15 분 마다 백업할 수 있습니다. 로그 백업은 데이터베이스에 대 한 전체 백업이 성공적으로 완료 된 후에만 흐름을 시작 합니다.
- 전체 백업과 차등 백업을 수행할 수 있습니다. 증분 백업은 현재 지원 되지 않습니다.
- 백업 정책을 SAP HANA 백업에 적용 한 후에는 수정할 수 없습니다. 다른 설정을 사용 하 여 백업 하려면 새 정책을 만들거나 다른 정책을 할당 합니다.
  - 새 정책을 만들려면 자격 증명 모음에서 **정책** > **백업 정책** >  **+**  > **Azure VM에서 SAP HANA**추가를 클릭 하 고 정책 설정을 지정 합니다.
  - 다른 정책을 할당 하려면 데이터베이스를 실행 하는 VM의 속성에서 현재 정책 이름을 클릭 합니다. 그런 다음 **백업 정책** 페이지에서 백업에 사용할 다른 정책을 선택할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

백업을 구성 하기 전에 다음을 수행 해야 합니다.

1. SAP HANA 데이터베이스를 실행 하는 VM에서 공식 Microsoft [.Net Core 런타임 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) 패키지를 설치 합니다. 다음 사항에 유의합니다.
    - **Dotnet-2.1** 패키지만 필요 합니다. **Aspnetcore-2.1**가 필요 하지 않습니다.
    - VM이 인터넷에 액세스할 수 없는 경우 미러를 사용 하거나 페이지에 지정 된 Microsoft 패키지 피드의 dotnet-runtime-2.1 (및 모든 종속 Rpm)에 대해 오프 라인 캐시를 제공 합니다.
    - 패키지를 설치 하는 동안 옵션을 지정 하 라는 메시지가 표시 될 수 있습니다. 그렇다면 **솔루션 2**를 지정 합니다.

        ![패키지 설치 옵션](./media/backup-azure-sap-hana-database/hana-package.png)

2. VM에서 다음과 같이 zypper를 사용 하 여 공식 SLES 패키지/미디어에서 ODBC 드라이버 패키지를 설치 하 고 사용 하도록 설정 합니다.

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. [아래](#set-up-network-connectivity)절차에 설명 된 대로 Azure에 연결할 수 있도록 VM에서 인터넷으로의 연결을 허용 합니다.

4. HANA가 루트 사용자로 설치 된 가상 머신에서 등록 전 스크립트를 실행 합니다. 이 스크립트는 [포털](#discover-the-databases) 에서 흐름으로 제공 되며 [적절 한 사용 권한을](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions)설정 하는 데 필요 합니다.

### <a name="set-up-network-connectivity"></a>네트워크 연결 설정

모든 작업에 대해 SAP HANA VM에는 Azure 공용 IP 주소에 대 한 연결이 필요 합니다. VM 작업 (데이터베이스 검색, 백업 구성, 백업 예약, 복구 지점의 복원 등)은 연결 없이 작동할 수 없습니다. Azure 데이터 센터 IP 범위에 대 한 액세스를 허용 하 여 연결을 설정 합니다. 

- Azure 데이터 센터의 [ip 주소 범위](https://www.microsoft.com/download/details.aspx?id=41653) 를 다운로드 한 다음 이러한 ip 주소에 대 한 액세스를 허용할 수 있습니다.
- NSGs (네트워크 보안 그룹)를 사용 하는 경우 AzureCloud [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 를 사용 하 여 모든 AZURE 공용 IP 주소를 허용할 수 있습니다. [AzureNetworkSecurityRule cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) 을 사용 하 여 nsg 규칙을 수정할 수 있습니다.

## <a name="onboard-to-the-public-preview"></a>공개 미리 보기에 등록

다음과 같이 공개 미리 보기로 등록 합니다.

- 포털에서 [이 문서](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)에 따라 Recovery Services 서비스 공급자에 구독 ID를 등록 합니다. 
- PowerShell의 경우이 cmdlet을 실행 합니다. "등록 됨"으로 완료 되어야 합니다.

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>데이터베이스 검색

1. 자격 증명 모음의 **시작**에서 **백업**을 클릭 합니다. **워크 로드가 실행 되는 위치**에서 **Azure VM의 SAP HANA**을 선택 합니다.
2. **검색 시작**을 클릭 합니다. 자격 증명 모음 지역에서 보호 되지 않는 Linux Vm의 검색을 시작 합니다.

   - 검색 후 보호 되지 않는 Vm은 이름 및 리소스 그룹 별로 나열 된 포털에 표시 됩니다.
   - VM이 예상 대로 표시 되지 않으면 이미 자격 증명 모음에 백업 되어 있는지 확인 합니다.
   - 여러 Vm은 동일한 이름을 가질 수 있지만 다른 리소스 그룹에 속해 있습니다.

3. **Virtual Machines 선택**에서 링크를 클릭 하 여 Azure Backup 서비스에서 데이터베이스 검색에 대 한 SAP HANA vm에 액세스할 수 있는 권한을 제공 하는 스크립트를 다운로드 합니다.
4. 백업 하려는 SAP HANA 데이터베이스를 호스트 하는 각 VM에 대해 스크립트를 실행 합니다.
5. Vm에서 스크립트를 실행 한 후 **Virtual Machines 선택**에서 vm을 선택 합니다. 그런 다음 **Db 검색**을 클릭 합니다.
6. Azure Backup VM의 모든 SAP HANA 데이터베이스를 검색 합니다. 검색 하는 동안 Azure Backup는 자격 증명 모음으로 VM을 등록 하 고 VM에 확장을 설치 합니다. 데이터베이스에 에이전트가 설치 되어 있지 않습니다.

    ![SAP HANA 데이터베이스 검색](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>백업 구성  

이제 백업을 사용 하도록 설정 합니다.

1. 2 단계에서 **백업 구성**을 클릭 합니다.
2. **백업할 항목 선택**에서 보호 하려는 모든 데이터베이스 > **확인**을 선택 합니다.
3.  **백업 정책** > 에서**백업 정책을 선택**하 고 아래 지침에 따라 데이터베이스에 대 한 새 백업 정책을 만듭니다.
4. 정책을 만든 후 **백업** 메뉴에서 **백업 사용**을 클릭 합니다.
5. 포털의 **알림** 영역에서 백업 구성 진행률을 추적 합니다.

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행 되는 시기와 보존 기간을 정의 합니다.

- 정책은 자격 증명 모음 수준에서 만들어집니다.
- 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.

정책 설정을 다음과 같이 지정 합니다.

1. **정책 이름**에 새 정책의 이름을 입력합니다.
2. **전체 백업 정책**에서 **백업 빈도**를 선택하고, **매일** 또는 **매주**를 선택합니다.
   - **매일**: 백업 작업이 시작 되는 시간 및 표준 시간대를 선택 합니다.
   
       - 전체 백업을 실행 해야 합니다. 이 옵션은 해제할 수 없습니다.
       - **전체 백업**을 클릭하여 정책을 확인합니다.
       - 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.
       
   - **매주**: 백업 작업이 실행 되는 요일, 시간 및 표준 시간대를 선택 합니다.
3. **보존 범위**에서 전체 백업에 대 한 보존 설정을 구성 합니다.
    - 기본적으로 모든 옵션이 선택 되어 있습니다. 사용 하지 않을 보존 범위 제한을 모두 선택 취소 하 고 수행 하는 범위를 설정 합니다.
    - 모든 백업 유형에 대 한 최소 보존 기간은 7 일입니다.
    - 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    - 특정 날짜에 대 한 백업은 주간 보존 범위 및 설정에 따라 태그가 지정 되 고 보존 됩니다.
    - 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.

4. **전체 백업 정책** 메뉴에서 **확인** 을 클릭 하 여 설정을 적용 합니다.
5. 차등 **백업** 을 선택 하 여 차등 정책을 추가 합니다.
6. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다.
    - 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.
    - 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 오래 보존해야 하는 경우에는 전체 백업을 사용해야 합니다.

    > [!NOTE]
    > 증분 백업은 현재 지원 되지 않습니다. 

7. **확인** 을 클릭 하 여 정책을 저장 하 고 주 **백업 정책** 메뉴로 돌아갑니다.
8. **로그 백업** 을 선택 하 여 트랜잭션 로그 백업 정책 추가
    - **로그 백업**에서 **사용**을 선택 합니다.
    - 빈도 및 보존 제어를 설정 합니다.

    > [!NOTE]
    > 로그 백업은 전체 백업이 성공적으로 완료 된 후에만 flow를 시작 합니다.

9. **확인** 을 클릭 하 여 정책을 저장 하 고 주 **백업 정책** 메뉴로 돌아갑니다.
10. 백업 정책 정의를 완료 한 후 **확인**을 클릭 합니다.


## <a name="run-an-on-demand-backup"></a>요청 시 백업 실행

백업은 정책 일정에 따라 실행 됩니다. 다음과 같이 요청 시 백업을 실행할 수 있습니다.


1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목**에서 SAP HANA 데이터베이스를 실행 하는 VM을 선택 하 고 **지금 Backup**을 클릭 합니다.
3. **지금 백업**에서 달력 컨트롤을 사용 하 여 복구 지점을 유지할 마지막 날을 선택 합니다. 그런 다음 **확인**을 클릭합니다.
4. 포털 알림을 모니터링합니다. **백업 작업** > 진행 중인 > 자격 증명 모음 대시보드에서 작업 진행률을 모니터링할 수 있습니다. 데이터베이스의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Azure Backup 사용 하도록 설정 된 데이터베이스에서 SAP HANA Studio 백업 실행

Azure Backup를 사용 하 여 백업 되는 데이터베이스의 로컬 백업 (HANA Studio 사용)을 수행 하려면 다음을 수행 합니다.

1. 데이터베이스에 대 한 전체 또는 로그 백업이 완료 될 때까지 기다립니다. SAP HANA Studio에서 상태를 확인 합니다.
2. 로그 백업을 사용 하지 않도록 설정 하 고, 관련 데이터베이스에 대 한 백업 카탈로그를 파일 시스템으로 설정 합니다.
3. 이렇게 하려면 **systemdb** > **구성** > 을 두 번 클릭 하 고**데이터베이스** > **필터 (로그)** 를 선택 합니다.
4. **Enable_auto_log_backup** 을 **No**로 설정 합니다.
5. **Log_backup_using_backint** 을 **False**로 설정 합니다.
6. 데이터베이스의 임시 전체 백업을 수행 합니다.
7. 전체 백업 및 카탈로그 백업이 완료 될 때까지 기다립니다.
8. 이전 설정을 Azure에 대 한 설정을 다시 되돌립니다.
    - **Enable_auto_log_backup** 을 **예**로 설정 합니다.
    - **Log_backup_using_backint** 를 **True**로 설정 합니다.



## <a name="next-steps"></a>다음 단계

Azure Vm에서 SAP HANA 백업을 사용 하는 동안 일반적인 오류를 해결 하는 방법 [에 대해 알아봅니다](backup-azure-sap-hana-database-troubleshoot.md) .
Azure Vm 백업 [에 대해 알아봅니다](backup-azure-arm-vms-prepare.md) .
