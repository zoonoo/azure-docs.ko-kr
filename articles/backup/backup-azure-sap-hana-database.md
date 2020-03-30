---
title: Azure 백업을 사용하여 SAP HANA 데이터베이스를 Azure에 백업
description: 이 문서에서는 Azure 백업 서비스를 사용하여 SAP HANA 데이터베이스를 Azure 가상 시스템에 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248061"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Azure VM에서 SAP HANA 데이터베이스 백업

SAP HANA 데이터베이스는 낮은 RPO(복구 포인트 목표) 및 장기 보존이 필요한 중요한 워크로드입니다. [Azure 백업을](backup-overview.md)사용하여 Azure 가상 시스템(VM)에서 실행되는 SAP HANA 데이터베이스를 백업할 수 있습니다.

이 문서에서는 Azure VM에서 실행 중인 SAP HANA 데이터베이스를 Azure 백업 복구 서비스 자격 증명 모음에 백업하는 방법을 보여 주며 있습니다.

이 아티클에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 자격 증명 모음 만들기 및 구성
> * 데이터베이스 검색
> * 백업 구성
> * 주문형 백업 작업 실행

>[!NOTE]
>**Azure VM의 SQL 서버에 대한 소프트 삭제 및 Azure VM 워크로드의 SAP HANA에 대한 소프트 삭제를** 미리 보기에서 사용할 수 있습니다.<br>
>미리 보기에 등록하려면AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>사전 요구 사항

[백업을](tutorial-backup-sap-hana-db.md#prerequisites) 위해 데이터베이스를 설정하는 필수 구성 조건 및 [사전 등록 스크립트가 수행하는 내용](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 섹션을 참조하십시오.

### <a name="set-up-network-connectivity"></a>네트워크 연결 설정

모든 작업에서는 SAP HANA VM이 Azure 공용 IP 주소에 연결되어 있어야 합니다. Azure 공용 IP 주소에 연결되지 않으면 VM 작업(데이터베이스 검색, 백업 구성, 백업 예약, 복구 지점 복원 등)이 실패합니다.

다음 옵션 중 하나를 사용하여 연결을 설정합니다.

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Azure 데이터 센터 IP 범위 허용

이 옵션은 다운로드한 파일의 [IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 허용합니다. NSG(네트워크 보안 그룹)에 액세스하려면 Set-AzureNetworkSecurityRule cmdlet을 사용합니다. 안전한 수신자 목록에 지역별 IP만 포함되어 있는 경우 인증을 사용하도록 설정하려면 안전한 수신자 목록에 Azure AD(Azure Active Directory) 서비스 태그를 업데이트해야 합니다.

#### <a name="allow-access-using-nsg-tags"></a>NSG 태그를 사용하여 액세스 허용

NSG를 사용하여 연결을 제한하는 경우 AzureBackup 서비스 태그를 사용하여 Azure Backup에 대한 아웃바운드 액세스를 허용해야 합니다. 또한 Azure AD 및 Azure Storage에 대한 [규칙](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)을 사용하여 인증 및 데이터 전송에 대한 연결을 허용해야 합니다. Azure Portal 또는 PowerShell에서 수행할 수 있습니다.

포털을 사용하여 규칙을 만들려면 다음을 수행합니다.

  1. **모든 서비스**에서 **네트워크 보안 그룹**으로 이동하여 네트워크 보안 그룹을 선택합니다.
  2. **설정** 아래에서 **아웃바운드 보안 규칙**을 선택합니다.
  3. **추가**를 선택합니다. [보안 규칙 설정](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)에 설명된 대로 새 규칙을 만드는 데 필요한 세부 정보를 모두 입력합니다. **대상** 옵션이 **서비스 태그**로 설정되고 **대상 서비스 태그**가 **AzureBackup**으로 설정되어 있는지 확인합니다.
  4. **추가**를 클릭하여 새로 만든 아웃바운드 보안 규칙을 저장합니다.

PowerShell을 사용하여 규칙을 만들려면 다음을 수행합니다.

 1. Azure 계정 자격 증명을 추가하고 국가를 업데이트합니다.<br/>
      `Add-AzureRmAccount`<br/>

 2. NSG 구독을 선택합니다.<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. NSG를 선택합니다.<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Azure Backup 서비스 태그에 대한 아웃바운드 허용 규칙을 추가합니다.<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Storage 서비스 태그에 대한 아웃바운드 허용 규칙을 추가합니다.<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. AzureActiveDirectory 서비스 태그에 대한 아웃바운드 허용 규칙을 추가합니다.<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. NSG를 저장합니다.<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Azure Firewall 태그를 사용하여 액세스를 허용합니다.** Azure Firewall을 사용하는 경우 AzureBackup [FQDN 태그](https://docs.microsoft.com/azure/firewall/fqdn-tags)를 사용하여 애플리케이션 규칙을 만듭니다. 이는 Azure Backup에 대한 아웃바운드 액세스를 허용합니다.

**트래픽을 라우팅하는 HTTP 프록시 서버를 배포합니다**. Azure VM에서 SAP HANA 데이터베이스를 백업하는 경우 VM의 백업 확장에서 HTTPS API를 사용하여 관리 명령을 Azure Backup에 보내고 데이터를 Azure Storage에 보냅니다. 백업 확장도 인증에 Azure AD를 사용합니다. HTTP 프록시를 통해 이 세 가지 서비스에 대한 백업 확장 트래픽을 라우팅합니다. 확장의 공용 인터넷에 액세스하도록 구성된 유일한 구성 요소입니다.

연결 옵션에는 다음과 같은 장점과 단점이 있습니다.

**옵션** | **장점** | **단점**
--- | --- | ---
IP 범위 허용 | 추가 비용 없음 | 시간이 지남에 따라 IP 주소 범위가 변경되므로 관리가 복잡함 <br/><br/> Azure Storage뿐만 아니라 Azure 전체에 대한 액세스 제공
NSG 서비스 태그 사용 | 범위 변경이 자동으로 병합되어 관리가 더 쉬움 <br/><br/> 추가 비용 없음 <br/><br/> | NSG에만 사용할 수 있음 <br/><br/> 전체 서비스에 대한 액세스 제공
Azure Firewall FQDN 태그 사용 | 필요한 FQDN이 자동으로 관리되어 관리가 더 쉬움 | Azure Firewall하고만 함께 사용할 수 있음
HTTP 프록시 사용 | 스토리지 URL에 대한 프록시의 세부적인 제어가 허용됨 <br/><br/> VM에 대한 인터넷 액세스의 단일 지점 <br/><br/> Azure IP 주소 변경이 적용되지 않음 | 프록시 소프트웨어로 VM을 실행하기 위해 추가 비용이 있음

#### <a name="private-endpoints"></a>프라이빗 엔드포인트

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>데이터베이스 검색

1. 자격 증명 모음의 **시작**에서 **백업**을 클릭합니다. **작업이 실행되는 위치**에서 **Azure VM의 SAP HANA**를 선택합니다.
2. **검색 시작**을 클릭합니다. 그러면 자격 증명 모음 지역에서 보호되지 않는 Linux VM의 검색이 시작됩니다.

   * 검색 후 보호되지 않은 VM이 포털에 나타나고 이름과 리소스 그룹으로 나열됩니다.
   * VM이 예상대로 나열되지 않은 경우 VM이 볼트에 이미 백업되어 있는지 확인합니다.
   * 여러 VM의 이름은 같을 수 있지만 서로 다른 리소스 그룹에 속합니다.

3. **Virtual Machines 선택**에서 링크를 클릭하여 데이터베이스 검색을 위해 SAP HANA VM에 액세스할 수 있는 권한을 Azure Backup 서비스에 제공하는 스크립트를 다운로드합니다.
4. 백업하려는 SAP HANA 데이터베이스를 호스팅하는 각 VM에서 스크립트를 실행합니다.
5. VM에서 스크립트를 실행한 후 **가상 컴퓨터 선택에서**VM을 선택합니다. 그런 다음, **DB 검색**을 클릭합니다.
6. Azure Backup에서 VM의 모든 SAP HANA 데이터베이스를 검색합니다. 검색하는 동안 Azure Backup은 VM을 자격 증명 모음에 등록하고 확장을 해당 VM에 설치합니다. 에이전트는 데이터베이스에 설치되지 않습니다.

    ![SAP HANA 데이터베이스 살펴보기](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>백업 구성  

이제 백업을 활성화합니다.

1. 2단계에서 백업 **구성을**클릭합니다.

    ![백업 구성](./media/backup-azure-sap-hana-database/configure-backup.png)
2. **백업할 항목 선택에서**보호하려는 모든 데이터베이스를 선택합니다> **확인합니다.**

    ![백업할 항목 선택](./media/backup-azure-sap-hana-database/select-items.png)
3. **백업 정책** > 백업 정책 선택 백업**정책에서**아래 지침에 따라 데이터베이스에 대한 새 백업 정책을 만듭니다.

    ![백업 정책 선택](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 정책을 만든 후 **백업** 메뉴에서 **백업 활성화를**클릭합니다.

    ![백업 사용](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 포털의 **알림** 영역에서 백업 구성 진행률을 추적합니다.

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행되는 시기와 보존 기간을 정의합니다.

* 정책은 자격 증명 모음 수준에서 만들어집니다.
* 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.

정책 설정을 다음과 같이 지정합니다.

1. **정책 이름**에 새 정책의 이름을 입력합니다.

   ![정책 이름 입력](./media/backup-azure-sap-hana-database/policy-name.png)
2. **전체 백업 정책**에서 **백업 빈도**를 선택하고, **매일** 또는 **매주**를 선택합니다.
   * **매일**: 백업 작업이 시작되는 시간 및 표준 시간대를 선택합니다.
       * 전체 백업을 실행해야 합니다. 이 옵션을 끌 수 없습니다.
       * **전체 백업**을 클릭하여 정책을 확인합니다.
       * 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.
   * **매주**: 백업 작업이 실행되는 요일, 시간 및 표준 시간대를 선택합니다.

   ![백업 빈도 선택](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. **보존 범위**에서 전체 백업에 대한 보존 설정을 구성합니다.
    * 기본적으로 모든 옵션이 선택됩니다. 사용하지 않으려는 보존 범위 제한을 지우고 원하는 보존 범위를 설정합니다.
    * 모든 백업 유형(전체/차등/로그)의 최소 보존 기간은 7일입니다.
    * 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    * 특정 일의 백업은 매주 보존 범위 및 설정에 기반한 태그로 지정되어 보존됩니다.
    * 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.

4. **전체 백업 정책** 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.
5. **차동 백업을** 선택하여 차동 정책을 추가합니다.
6. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다.
    * 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.
    * 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 오래 보존해야 하는 경우에는 전체 백업을 사용해야 합니다.

    ![차등 백업 정책](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 증분 백업은 현재 지원되지 않습니다.

7. **확인**을 클릭하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.
8. **로그 백업**을 선택하여 트랜잭션 로그 백업 정책을 추가합니다.
    * **로그 백업에서** **를 선택합니다.**  SAP HANA에서 모든 로그 백업을 관리하므로 이 기능을 사용하지 않도록 설정할 수 없습니다.
    * 주파수 및 보존 컨트롤을 설정합니다.

    > [!NOTE]
    > 로그 백업은 전체 백업이 완료된 후에만 흐르기 시작합니다.

9. **확인**을 클릭하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.
10. 백업 정책 정의가 완료되면 **확인**을 클릭합니다.

> [!NOTE]
> 각 로그 백업은 이전 전체 백업에 연결되어 복구 체인을 형성합니다. 이 전체 백업은 마지막 로그 백업의 보존이 만료될 때까지 유지됩니다. 이는 모든 로그를 복구할 수 있도록 전체 백업이 추가 기간 동안 유지됨을 의미할 수 있습니다. 사용자가 주간 전체 백업, 일일 차동 및 2 시간 로그를 가지고 있다고 가정 해 봅시다. 그들 모두는 30 일 동안 유지됩니다. 그러나, 주간 전체 정말 정리 / 다음 전체 백업을 사용할 수있는 경우에만 삭제 할 수 있습니다, 즉, 후 30 + 7 일. 11월 16일에 는 매주 전체 백업이 수행됩니다. 보존 정책에 따라 12월 16일까지 보존해야 합니다. 이 전체의 마지막 로그 백업은 11월 22일에 예정된 다음 전체 로그 전에 수행됩니다. 이 로그는 12월 22일까지 사용할 수 있을 때까지 11월 16일 전체를 삭제할 수 없습니다. 그래서, 11 월 16 전체 12 월 22 일까지 유지 됩니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

백업은 정책 일정에 따라 실행됩니다. 다음과 같이 주문형 백업을 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목에서**SAP HANA 데이터베이스를 실행하는 VM을 선택한 다음 **지금 백업을**클릭합니다.
3. **지금 백업에서**캘린더 컨트롤을 사용하여 복구 지점을 유지해야 하는 마지막 날을 선택합니다. 그런 다음 **확인**을 클릭합니다.
4. 포털 알림을 모니터링합니다. **백업 작업** > 진행 중인 > 볼트 대시보드에서 작업**진행률을 모니터링할**수 있습니다. 데이터베이스 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Azure 백업을 사용하도록 설정한 데이터베이스에서 SAP HANA Studio 백업 실행

Azure Backup으로 백업되는 데이터베이스의 로컬 백업(HANA Studio 사용)을 수행하려면 다음을 수행합니다.

1. 데이터베이스가 완료될 때까지 전체 백업 또는 로그 백업을 기다립니다. SAP HANA 스튜디오 / 조종석에서 상태를 확인합니다.
2. 로그 백업을 사용하지 않도록 설정하고 관련 데이터베이스의 백업 카탈로그를 파일 시스템으로 설정합니다.
3. 이렇게 하려면 **systemdb** > **구성** > **선택 데이터베이스** > **필터(로그)를**두 번 클릭합니다.
4. **enable_auto_log_backup** **아니오로 설정합니다.**
5. **log_backup_using_backint** **false로 설정합니다.**
6. 데이터베이스의 온디맨드 전체 백업을 수행합니다.
7. 전체 백업 및 카탈로그 백업이 완료될 때까지 기다립니다.
8. 이전 설정을 Azure의 설정으로 되돌리.
    * **enable_auto_log_backup** **예로 설정합니다.**
    * **log_backup_using_backint** **true로 설정합니다.**

## <a name="next-steps"></a>다음 단계

* [Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)을 알아봅니다.
* [Azure 백업을 사용하여 백업되는 SAP HANA 데이터베이스를 관리하는](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) 방법에 대해 알아봅니다.
