---
title: 자습서 - Azure VM에서 SAP HANA 데이터베이스 백업
description: 이 자습서에서는 Azure VM에서 실행되는 SAP HANA 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: a622370fca3144aeb6a5d7c071c227b3c21cf135
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287190"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>자습서: Azure VM에서 SAP HANA 데이터베이스 백업

이 자습서에서는 Azure VM에서 실행되는 SAP HANA 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업하는 방법을 보여 줍니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 자격 증명 모음 만들기 및 구성
> * 데이터베이스 검색
> * 백업 구성

현재 지원되는 모든 시나리오는 [여기](sap-hana-backup-support-matrix.md#scenario-support)에 나와 있습니다.

## <a name="onboard-to-the-public-preview"></a>공개 미리 보기에 온보딩

다음과 같이 공개 미리 보기에 온보딩합니다.

* 포털에서 [이 문서에 따라](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal) 구독 ID를 Recovery Services 공급자에 등록합니다.

* PowerShell의 경우 다음 cmdlet을 실행합니다. 그러면 "등록됨"으로 완료됩니다.

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>필수 조건

백업을 구성하기 전에 다음을 수행해야 합니다.

1. SAP HANA 데이터베이스를 실행하는 VM에서 다음과 같이 zypper를 사용하여 공식 SLES 패키지/미디어에서 ODBC 드라이버 패키지를 설치하고 사용하도록 설정합니다.

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> 리포지토리를 업데이트하지 않는 경우 unixODBC의 버전이 2.3.4 이상인지 확인합니다. uniXODBC 버전을 확인하려면 `odbcinst -j`을 루트 사용자 권한으로 실행하세요.
>

2. [아래 절차](#set-up-network-connectivity)에서 설명한 대로 VM에서 인터넷에 연결하도록 허용하여 Azure에 연결할 수 있습니다.

3. HANA가 루트 사용자 권한으로 설치된 가상 머신에서 사전 등록 스크립트를 실행합니다. [이 스크립트](https://aka.ms/scriptforpermsonhana)는 [올바른 권한](#setting-up-permissions)을 설정합니다.

## <a name="set-up-network-connectivity"></a>네트워크 연결 설정

모든 작업에서는 SAP HANA VM이 Azure 공용 IP 주소에 연결되어 있어야 합니다. 연결되어 있지 않으면 VM 작업(데이터베이스 검색, 백업 구성, 백업 예약, 복구 지점 복원 등)이 작동하지 않습니다. 다음과 같이 Azure 데이터 센터 IP 범위에 대한 액세스를 허용하여 연결을 설정합니다.

* Azure 데이터 센터에 대한 [IP 주소 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 다운로드한 다음, 이러한 IP 주소에 대한 액세스를 허용할 수 있습니다.
* NSG(네트워크 보안 그룹)를 사용하는 경우 AzureCloud [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)를 사용하여 모든 Azure 공용 IP 주소를 허용할 수 있습니다. [Set-AzureNetworkSecurityRule cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0)을 사용하여 NSG 규칙을 수정할 수 있습니다.
* HTTPS를 통해 전송되므로 443 포트를 허용 목록에 추가해야 합니다.

## <a name="setting-up-permissions"></a>권한 설정

사전 등록 스크립트는 다음 작업을 수행합니다.

1. HANA 시스템에서 AZUREWLBACKUPHANAUSER를 만들고, 다음과 같은 필수 역할 및 권한을 추가합니다.
   * DATABASE ADMIN: 복원하는 동안 새 DB를 만듭니다.
   * CATALOG READ: 백업 카탈로그를 읽습니다.
   * SAP_INTERNAL_HANA_SUPPORT: 몇 개의 프라이빗 테이블에 액세스합니다.
2. 모든 작업(데이터베이스 쿼리, 복원 작업, 백업 구성 및 실행)을 처리할 수 있도록 HANA 플러그 인용 Hdbuserstore에 키를 추가합니다.

키 생성을 확인하려면 HANA 머신에서 SIDADM 자격 증명을 사용하여 HDBSQL 명령을 실행합니다.

```hdbsql
hdbuserstore list
```

명령 출력에서 사용자가 AZUREWLBACKUPHANAUSER로 표시된 {SID}{DBNAME} 키가 표시됩니다.

>[!NOTE]
> 고유한 SSFS 파일 세트가 /usr/sap/{SID}/home/.hdb/ 아래에 있는지 확인하세요. 이 경로에는 하나의 폴더만 있어야 합니다.
>

## <a name="create-a-recovery-service-vault"></a>Recovery Service 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

Recovery Services 자격 증명 모음을 만들려면:

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

![모든 서비스 선택](./media/tutorial-backup-sap-hana-db/all-services.png)

3. **모든 서비스** 대화 상자에서 **Recovery Services**를 입력합니다. 입력 내용에 따라 리소스 목록이 필터링됩니다. 리소스 목록에서 **Recovery Services 자격 증명 모음**을 선택합니다.

![Recovery Services 자격 증명 모음 선택](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. **Recovery Services** 자격 증명 모음 대시보드에서 **추가**를 선택합니다.

![Recovery Services 자격 증명 모음 추가](./media/tutorial-backup-sap-hana-db/add-vault.png)

**Recovery Services 자격 증명 모음** 대화 상자가 열립니다. **이름, 구독, 리소스 그룹** 및 **위치**에 대한 값을 제공합니다.

![Recovery Services 자격 증명 모음 만들기](./media/tutorial-backup-sap-hana-db/create-vault.png)

* **이름**: Recovery Services 자격 증명 모음을 식별하는 데 사용되며, Azure 구독에서 고유해야 합니다. 2자 이상 50자 이하의 이름을 지정합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 포함할 수 있습니다. 이 자습서에서는 **SAPHanaVault**라는 이름을 사용했습니다.
* **구독**: 사용할 구독을 선택합니다. 단일 구독의 멤버인 경우 해당 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(제안된) 구독을 사용합니다. 회사 또는 학교 계정이 둘 이상의 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다. 여기서는 **SAP HANA 솔루션 랩 구독** 구독을 사용했습니다.
* **리소스 그룹**: 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 여기서는 **SAPHANADemo**를 사용했습니다.<br>
구독에서 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용**을 선택한 다음, 드롭다운 목록 상자에서 리소스를 선택합니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 이름을 입력합니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요.
* **위치**: 자격 증명 모음에 대한 지리적 지역을 선택합니다. 자격 증명 모음은 SAP HANA를 실행하는 Virtual Machine과 동일한 지역에 있어야 합니다. **미국 동부 2**를 사용했습니다.

5. **검토 + 만들기**를 선택합니다.

   ![검토 + 만들기 선택](./media/tutorial-backup-sap-hana-db/review-create.png)

이제 Recovery Services 자격 증명 모음이 만들어집니다.

## <a name="discover-the-databases"></a>데이터베이스 검색

1. 자격 증명 모음의 **시작**에서 **백업**을 클릭합니다. **작업이 실행되는 위치**에서 **Azure VM의 SAP HANA**를 선택합니다.
2. **검색 시작**을 클릭합니다. 그러면 자격 증명 모음 지역에서 보호되지 않는 Linux VM의 검색이 시작됩니다. 보호하려는 Azure VM이 표시됩니다.
3. **Virtual Machines 선택**에서 링크를 클릭하여 데이터베이스 검색을 위해 SAP HANA VM에 액세스할 수 있는 권한을 Azure Backup 서비스에 제공하는 스크립트를 다운로드합니다.
4. 백업하려는 SAP HANA 데이터베이스를 호스팅하는 VM에서 스크립트를 실행합니다.
5. VM에서 스크립트가 실행되면 **Virtual Machines 선택**에서 해당 VM을 선택합니다. 그런 다음, **DB 검색**을 클릭합니다.
6. Azure Backup에서 VM의 모든 SAP HANA 데이터베이스를 검색합니다. 검색하는 동안 Azure Backup은 VM을 자격 증명 모음에 등록하고 확장을 해당 VM에 설치합니다. 에이전트는 데이터베이스에 설치되지 않습니다.

   ![데이터베이스 검색](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>백업 구성

이제 백업하려는 데이터베이스가 검색되었으므로 백업을 사용하도록 설정해 보겠습니다.

1. **백업 구성**을 클릭합니다.

![백업 구성](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. **백업할 항목 선택**에서 보호하려는 데이터베이스를 하나 이상 선택한 다음, **확인**을 클릭합니다.

![백업할 항목 선택](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. **백업 정책 > 백업 정책 선택**에서 다음 섹션의 지침에 따라 데이터베이스에 대한 새 백업 정책을 만듭니다.

![백업 정책 선택](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. 정책이 만들어지면 **백업 메뉴**에서 **백업 사용**을 클릭합니다.

   ![백업 사용 클릭](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. 포털의 **알림** 영역에서 백업 구성 진행률을 추적합니다.

## <a name="creating-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행되는 시기와 보존 기간을 정의합니다.

* 정책은 자격 증명 모음 수준에서 만들어집니다.
* 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.

정책 설정을 다음과 같이 지정합니다.

1. **정책 이름**에 새 정책의 이름을 입력합니다. 여기서는 **SAPHANA**를 입력 합니다.

![새 정책 이름 입력](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. **전체 백업 정책**에서 **백업 빈도**를 선택합니다. **매일** 또는 **매주**를 선택할 수 있습니다. 이 자습서에서는 **매일** 백업을 선택했습니다.

![백업 빈도 선택](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. **보존 범위**에서 전체 백업에 대한 보존 설정을 구성합니다.
   * 기본적으로 모든 옵션이 선택되어 있습니다. 사용하지 않으려는 보존 범위 제한을 지우고, 사용할 제한을 설정합니다.
   * 모든 백업 유형(전체/차등/로그)의 최소 보존 기간은 7일입니다.
   * 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
   * 특정 일의 백업은 매주 보존 범위 및 설정에 기반한 태그로 지정되어 보존됩니다.
   * 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.
4. **전체 백업 정책** 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.
5. 그런 다음, **차등 백업**을 선택하여 차등 정책을 추가합니다.
6. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다. **30일** 동안 보존되는 차등 백업을 매주 **일요일** **오전 2시**에 수행하도록 설정했습니다.

   ![차등 백업 정책](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

>[!NOTE]
>증분 백업은 현재 지원되지 않습니다.
>

7. **확인**을 클릭하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.
8. **로그 백업**을 선택하여 트랜잭션 로그 백업 정책을 추가합니다.
   * **로그 백업**은 기본적으로 **사용**으로 설정됩니다. SAP HANA에서 모든 로그 백업을 관리하므로 이 기능을 사용하지 않도록 설정할 수 없습니다.
   * 백업 일정은 **2시간**으로 설정하고, 보존 기간은 **15일**로 설정했습니다.

    ![로그 백업 정책](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

>[!NOTE]
> 하나의 전체 백업이 성공적으로 완료된 후에만 로그 백업을 수행합니다.
>

9. **확인**을 클릭하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.
10. 백업 정책 정의가 완료되면 **확인**을 클릭합니다.

이제 SAP HANA 데이터베이스에 대한 백업이 성공적으로 구성되었습니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM에서 실행되는 SAP HANA 데이터베이스에서 요청 시 백업을 실행하는 방법](backup-azure-sap-hana-database.md#run-an-on-demand-backup)을 알아봅니다.
* [Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법](sap-hana-db-restore.md)을 알아봅니다.
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](sap-hana-db-manage.md)을 알아봅니다.
* [SAP HANA 데이터베이스를 백업할 때 발생하는 일반적인 문제를 해결하는 방법](backup-azure-sap-hana-database-troubleshoot.md)을 알아봅니다.
