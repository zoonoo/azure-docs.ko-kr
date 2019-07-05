---
title: Azure Backup을 사용 하 여 Azure에 SAP HANA 데이터베이스를 백업 | Microsoft Docs
description: 이 자습서에서는 Azure Backup 서비스를 사용 하 여 Azure에 SAP HANA 데이터베이스를 백업 하는 방법에 설명 합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: a16ed7134fc9f3c159715f58f116de3fb30e8aca
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481135"
---
# <a name="back-up-an-sap-hana-database"></a>SAP HANA 데이터베이스 백업

[Azure Backup](backup-overview.md) Azure에 SAP HANA 데이터베이스의 백업을 지원 합니다.

> [!NOTE]
> 이 기능은 현재 공개 미리 보기로 제공됩니다. 현재 프로덕션을 준비, 아니며 보장 된 SLA가 제공 하지 않습니다. 

## <a name="scenario-support"></a>시나리오 지원

**지원** | **세부 정보**
--- | ---
**지원되는 지역** | 오스트레일리아 동남부, 오스트레일리아 동부 <br> 브라질 남부 <br> 캐나다 중부, 캐나다 동부 <br> 동남 아시아에서 동아시아 <br> 미국 동부, 미국 동부 2, 미국 중서부, 미국 서 부, 미국 서 부 2, 미국 중 북부, 미국 중부, 미국 중남부<br> 인도 중부, 인도 남부 <br> 일본 동부, 일본 서부<br> 한국 중부, 한국 남부 <br> 북유럽, 서유럽 <br> 영국 남부, 영국 서 부
**지원 되는 VM 운영 체제** | SLES 12 SP2 또는 SP3를 사용 하 여 합니다.
**지원 되는 HANA 버전** | SDC on HANA 1.x, MDC on HANA 2.x <= SPS03

### <a name="current-limitations"></a>현재 제한 사항

- 만 Azure Vm에서 실행 되는 SAP HANA 데이터베이스를 백업할 수 있습니다.
- Azure portal에서 SAP HANA 백업만 구성할 수 있습니다. PowerShell, CLI 또는 REST API를 사용 하 여 기능을 구성할 수 없습니다.
- 만 확장 모드에서 데이터베이스를 백업할 수 있습니다.
- 15 분 마다 데이터베이스 로그를 백업할 수 있습니다. 로그 백업을 데이터베이스에 대 한 전체 백업을 성공적으로 완료 된 후 흐름에 시작 합니다.
- 전체 및 차등 백업을 수행할 수 있습니다. 증분 백업은 현재 지원 되지 않습니다.
- SAP HANA 백업에 적용 한 후 백업 정책을 수정할 수 없습니다. 다른 설정을 사용 하 여 백업 하려는 경우 새 정책을 만들거나 다른 정책을 할당 합니다.
  - 새 정책을 만들려면 클릭에 자격 증명 모음에서 **정책을** > **Backup 정책** >  **+ 추가** > **에서 SAP HANA Azure VM**, 정책 설정을 지정 합니다.
  - 데이터베이스를 실행 하는 VM의 속성에 다른 정책을 할당 하려면 현재 정책 이름을 클릭 합니다. 그런 다음 합니다 **백업 정책** 페이지는 백업에 사용할 다른 정책을 선택할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

백업을 구성 하기 전에 다음을 수행 해야 합니다.

1. VM에서 공식 Microsoft 설치는 SAP HANA 데이터베이스를 실행 중인 [.NET Core 런타임 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) 패키지 있습니다. 다음 사항에 유의하세요.
    - 해야 합니다 **dotnet 런타임 2.1** 패키지 합니다. 필요가 **aspnetcore 런타임 2.1**합니다.
    - 없으면 VM 인터넷 액세스, 미러 또는 dotnet 런타임 2.1 (및 모든 종속 Rpm) 피드 Microsoft 패키지에서 지정 된 오프 라인-캐시 제공 페이지에 있습니다.
    - 패키지 설치 하는 동안 옵션을 지정 하 라는 메시지가 표시 될 수 있습니다. 그렇다면 지정할 **솔루션 2**합니다.

        ![패키지 설치 옵션](./media/backup-azure-sap-hana-database/hana-package.png)

2. VM에서 설치 하 고 공식 SLES 패키지/미디어에서 같이 zypper를 사용 하 여 ODBC 드라이버 패키지를 사용 하도록 설정 합니다.

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. 절차에서 설명한 대로 Azure에서 도달할 수 있도록 인터넷에 연결 된 VM에서 허용 [아래](#set-up-network-connectivity)합니다.

4. 루트 사용자로 HANA를 설치한 가상 컴퓨터에서 사전 등록 스크립트를 실행 합니다. 스크립트가 제공 됩니다 [포털에서](#discover-the-databases) 흐름에서 설정 하는 데 필요한 및 합니다 [권한을 마우스 오른쪽 단추로](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions)합니다.

### <a name="set-up-network-connectivity"></a>네트워크 연결 설정

모든 작업에 대 한 SAP HANA VM에 Azure 공용 IP 주소에 연결을 해야합니다. VM 작업 (데이터베이스 검색 백업을 구성, 백업 일정, 복구 지점 복원 및 등)은 연결 없이 작동할 수 없습니다. Azure 데이터 센터 IP 범위에 대 한 액세스를 허용 하 여 연결을 설정 합니다. 

- 다운로드할 수 있습니다 합니다 [IP 주소 범위](https://www.microsoft.com/download/details.aspx?id=41653) Azure 데이터 센터에 대 한 다음 이러한 IP 주소에 대 한 액세스를 허용 합니다.
- 네트워크 보안 그룹 (Nsg)를 사용 하는 경우 사용할 수는 AzureCloud [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) 주소를 모든 Azure 공용 IP를 허용 하려면. 사용할 수는 [Set-azurenetworksecurityrule cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) NSG 규칙을 수정 합니다.

## <a name="onboard-to-the-public-preview"></a>공개 미리 보기 등록

다음과 같은 공개 미리 보기에 등록 합니다.

- 포털에서 등록 하 여 Recovery Services 서비스 공급자에 구독 ID [이 문서에서는 다음](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)합니다. 
- Powershell의 경우이 cmdlet을 실행 합니다. "등록 됨"으로 완료 되어야 합니다.

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>데이터베이스를 검색 합니다.

1. 자격 증명 모음에서에서 **Getting Started**, 클릭 **백업**합니다. **작업이 실행 되는 위치?** 를 선택 **Azure VM에서 SAP HANA**합니다.
2. 클릭 **검색 시작**합니다. 이 자격 증명 모음 지역에서 Linux Vm을 보호 되지 않는의 검색을 시작합니다.

   - 검색 이름 및 리소스 그룹에 의해 나열 보호 되지 않는 Vm이 포털에 표시 합니다.
   - VM을 예상 대로 나열 되지 않으면 있는지 여부를 이미 백업 자격 증명 모음에서 확인 합니다.
   - 여러 Vm에 동일한 이름을 가질 수 있지만 다른 리소스 그룹에 속해 있습니다.

3. **Virtual Machines 선택**, 데이터베이스 검색에 대 한 SAP HANA Vm에 액세스 하려면 Azure Backup 서비스에 대 한 권한을 제공 하는 스크립트를 다운로드 하려면 링크를 클릭 합니다.
4. 백업 하려는 SAP HANA 데이터베이스를 호스팅하는 각 VM에서 스크립트를 실행 합니다.
5. Vm에서 스크립트를 실행 한 후 **Virtual Machines 선택**, Vm을 선택 합니다. 누른 **Db 검색**합니다.
6. Azure Backup은 VM에서 모든 SAP HANA 데이터베이스를 검색합니다. 검색 중에 Azure Backup 자격 증명을 사용 하 여 VM을 등록 하 고 VM에 확장을 설치 합니다. 데이터베이스에서 에이전트가 설치 됩니다.

    ![SAP HANA 데이터베이스 검색](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>백업 구성  

이제 백업을 사용 하도록 설정 합니다.

1. 2 단계에서에서 클릭 **백업 구성**합니다.
2. **백업할 항목 선택**, 보호 하려는 모든 데이터베이스 선택 > **확인**합니다.
3.  **백업 정책** > **백업 정책 선택**, 데이터베이스의 경우 아래 지침에 따라 새 백업 정책을 만듭니다.
4. 정책을 만든 후 합니다 **백업** 메뉴에서 클릭 **백업 사용**합니다.
5. 백업 구성 진행률을 추적 합니다 **알림을** 포털의 영역입니다.

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책에는 백업이 수행 되는 경우 및 얼마나 오래 보존 하는 정의 합니다.

- 정책은 자격 증명 모음 수준에서 만들어집니다.
- 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.

다음과 같이 정책 설정을 지정 합니다.

1. **정책 이름**에 새 정책의 이름을 입력합니다.
2. **전체 백업 정책**에서 **백업 빈도**를 선택하고, **매일** 또는 **매주**를 선택합니다.
   - **매일**: 시간 및 백업 작업이 시작 되는 표준 시간대를 선택 합니다.
   
       - 전체 백업을 실행 해야 합니다. 이 옵션을 해제할 수 없습니다.
       - **전체 백업**을 클릭하여 정책을 확인합니다.
       - 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.
       
   - **매주**: 요일, 시간 및 백업 작업이 실행 되는 표준 시간대의 날짜를 선택 합니다.
3. **보존 범위**, 전체 백업에 대 한 보존 설정을 구성 합니다.
    - 모든 옵션은 기본적으로 선택 됩니다. 보존 범위 제한 하 고 수행 하는 것을 설정 하지 않으려면 선택을 취소 합니다.
    - 백업 (전체/차등/로그)의 모든 형식에 대 한 최소 보존 기간은 7 일입니다.
    - 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    - 백업에 대 한 특정 날짜는 태그가 지정 된 보존 매주 보존 범위 및 설정에 기반 합니다.
    - 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.

4. 에 **전체 Backup 정책** 메뉴에서 클릭 **확인** 설정을 적용 합니다.
5. 선택 **차등 백업** 차등 정책을 추가 합니다.
6. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다.
    - 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.
    - 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 오래 보존해야 하는 경우에는 전체 백업을 사용해야 합니다.

    > [!NOTE]
    > 증분 백업은 현재 지원 되지 않습니다. 

7. 클릭 **확인** 정책을 저장 하 고 기본 돌아갑니다 **Backup 정책** 메뉴.
8. 선택 **로그 백업** 트랜잭션 로그 백업 정책을 추가 하려면
    - **로그 백업이**를 선택 **사용**합니다.
    - 빈도 및 보존 컨트롤을 설정 합니다.

    > [!NOTE]
    > 로그 백업을 전체 백업이 성공적으로 완료 되 면 흐름을 시작 합니다.

9. 클릭 **확인** 정책을 저장 하 고 기본 돌아갑니다 **Backup 정책** 메뉴.
10. 백업 정책 정의 마친 후 클릭 **확인**합니다.


## <a name="run-an-on-demand-backup"></a>요청 시 백업 실행

백업 정책 일정에 따라 실행합니다. 다음과 같이 백업 주문형을 실행할 수 있습니다.


1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **Backup 항목**, SAP HANA 데이터베이스를 실행 하는 VM을 선택 하 고 클릭 **지금 Backup**합니다.
3. **지금 Backup**, 달력 컨트롤을 사용 하 여 복구 지점을 유지 되어야 하는 마지막 날을 선택 합니다. 그런 후 **OK**를 클릭합니다.
4. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드에서 작업 진행 상태를 모니터링할 수 있습니다 > **백업 작업** > 진행에서 합니다. 데이터베이스의 크기에 따라 초기 백업을 만드는 시간이 걸릴 수 있습니다.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Azure backup 사용 하도록 설정 된 데이터베이스에서 SAP HANA Studio 백업 실행

로컬 (HANA Studio를 사용 하 여) 백업 되는 데이터베이스의 Azure Backup으로 백업을 수행 하려는 경우 다음을 수행 합니다.

1. 모든 전체 시간 동안 대기 하거나 완료 하려면 데이터베이스에 대 한 로그 백업입니다. SAP HANA Studio에서 상태를 확인 합니다.
2. 로그 백업을 사용 하지 않도록 설정 하 고 관련 데이터베이스에 대 한 파일 시스템 백업 카탈로그를 설정 합니다.
3. 이 작업을 수행 하려면 두 번 클릭 **아닌 systemdb** > **Configuration** > **데이터베이스 선택** > **필터 (로그)** .
4. 설정할 **enable_auto_log_backup** 하 **No**합니다.
5. 설정할 **log_backup_using_backint** 하 **False**합니다.
6. 임시는 전체 데이터베이스 백업을 수행 합니다.
7. 전체 백업 및 카탈로그 백업 완료 되기를 기다립니다.
8. 이전 설정을 다시 Azure에 대 한 되돌립니다.
    - 설정할 **enable_auto_log_backup** 하 **예**합니다.
    - 설정할 **log_backup_using_backint** 하 **True**합니다.



## <a name="next-steps"></a>다음 단계

[에 대 한 자세한](backup-azure-sap-hana-database-troubleshoot.md) Azure Vm에서 SAP HANA backup을 사용 하는 동안 일반적인 오류를 해결 하는 방법입니다.
[에 대 한 자세한](backup-azure-arm-vms-prepare.md) Azure Vm을 백업 합니다.
