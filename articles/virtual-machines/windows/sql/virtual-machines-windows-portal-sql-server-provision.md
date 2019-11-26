---
title: Provision virtual machine with Azure portal
description: 이 방법 가이드에서는 Azure Portal에서 Windows SQL Server 2017 가상 머신을 만드는 데 사용되는 옵션에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 28f00db3b604534be5ff9cee79c0aacc41f066b5
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464155"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Azure Portal에서 Windows SQL Server 가상 머신 프로비전하는 방법

이 가이드에서는 Azure Portal에서 Windows SQL Server 가상 머신을 만드는 경우 사용할 수 있는 다양한 옵션에 대한 상세 정보를 제공합니다. 이 아티클에서는 가능한 하나의 프로비전 작업을 살펴보며 [SQL Server VM 빠른 시작](quickstart-sql-vm-create-portal.md)보다 많은 구성 옵션에 대해 설명합니다. 

이 가이드를 사용하여 고유한 SQL Server VM을 만듭니다. 또는 그것을 Azure Portal에서 사용 가능한 옵션에 대한 참조로 사용합니다.

> [!TIP]
> SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](virtual-machines-windows-sql-server-iaas-faq.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a id="select"></a>SQL Server 가상 머신 갤러리 이미지

SQL Server 가상 머신을 만들 때 가상 머신 갤러리에서 미리 구성된 여러 이미지 중 하나를 선택할 수 있습니다. 다음 단계에서는 SQL Server 2017 이미지 중 하나를 선택하는 방법을 보여줍니다.

1. Azure Portal의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. If **Azure SQL** is not in the list, select **All services**, then type Azure SQL in the search box. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. **+ 추가**를 선택하여 **SQL 배포 옵션 선택** 페이지를 엽니다. You can view additional information by selecting **Show details**. 
1. Type `2017` in the SQL Server image search box on the **SQL virtual machines** tile, and then select **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** from the drop-down. 


   ![Select SQL VM image](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > 개발자 버전은 개발 테스트 목적으로 무료로 제공되는 SQL Server의 모든 기능을 갖춘 버전이므로 이 연습에서 사용됩니다. VM 실행 비용에 대해서만 비용을 지불합니다. 그러나 이 연습에 사용할 이미지를 자유롭게 선택할 수 있습니다. 사용 가능한 이미지에 대한 설명은 [SQL Server Windows Virtual Machines 개요](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)를 참조하세요.

   > [!TIP]
   > SQL Server의 라이선스 비용은 사용자가 만드는 VM의 초당 가격에 통합되며 에디션과 코어에 따라 다릅니다. 그러나 SQL Server 개발자 에디션은 개발/테스트(비 프로덕션)를 위한 평가판이며, SQL Express는 간단한 워크로드(1GB 미만 메모리, 10GB 미만 스토리지)를 위한 평가판입니다. 또한 BYOL(사용자 라이선스 필요)할 수 있으며 VM에 대해서만 지불에 대해서만 지불합니다. 이러한 이미지 이름에는 접두사 {BYOL}이 붙습니다. 
   >
   > 이러한 옵션에 대한 자세한 내용은 [SQL Server Azure VM에 대한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)을 참조하세요.


1. **만들기**를 선택합니다.


## <a name="1-configure-basic-settings"></a>1. Configure basic settings


**기본** 탭에서 다음 정보를 제공합니다.

* Under **Project Details**, make sure the correct subscription is selected. 
*  In the **Resource group** section, either select an existing resource group from the list or choose **Create new** to create a new resource group. 리소스 그룹은 Azure 내 관련 리소스의 컬렉션입니다(가상 머신, 스토리지 계정, 가상 네트워크 등). 

    ![Subscription](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > 새 리소스 그룹을 사용하면 Azure에서 SQL Server 배포를 테스트하거나 알아보는 경우에 유용합니다. 테스트를 완료한 후 리소스 그룹을 삭제하면 VM과 해당 리소스 그룹과 연결된 모든 리소스가 자동으로 삭제됩니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../../azure-resource-manager/resource-group-overview.md)를 참조하세요.


* **인스턴스 세부 정보**에서
    1. Enter a unique **Virtual machine name**.  
    1. **영역**에 위치를 선택합니다. 
    1. For the purpose of this guide, leave **Availability options** set to _No infrastructure redundancy required_. 가용성 옵션에 대한 자세한 내용을 알아보려면 [가용성](../../windows/availability.md)을 참조하세요. 
    1. In the **Image** list, select _Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016_.  
    1. 가상 머신의 **크기**에서 **크기 변경**을 선택하고 **A2 Basic** 제품을 선택합니다. 리소스를 다 사용했으면 예기치 않은 요금이 청구되지 않도록 리소스를 삭제하세요. 프로덕션 워크로드의 경우 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)에서 권장하는 컴퓨터 크기 및 구성을 참조하세요.

    ![인스턴스 세부 정보](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> **크기 선택** 창에 표시된 월별 예상 비용에는 SQL Server 라이선스 비용이 포함되지 않습니다. 이 예상 비용은 VM만의 비용입니다. SQL Server의 Express 및 개발자 버전의 경우, 이 예상 비용은 총 예상 비용입니다. 다른 버전의 경우 [Windows Virtual Machines 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)를 참조하여 SQL Server의 대상 버전을 선택하세요. Also see the [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) and [Sizes for virtual machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Under **Administrator account**, provide a username and a password. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.

   ![관리자 계정](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* **인바운드 포트 규칙**에서 **선택한 포트 허용**을 선택한 다음, 드롭다운에서 **RDP(3389)** 를 선택합니다. 

   ![인바운드 포트 규칙](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configure optional features

### <a name="disks"></a>디스크

On the **Disks** tab, configure your disk options. 

* Under **OS disk type**, select the type of disk you want for your OS from the drop-down. Premium is recommended for production systems but is not available for a Basic VM. To utilize Premium SSD, change the virtual machine size. 
* Under **Advanced**, select **Yes** under use **Managed Disks**.

   > [!NOTE]
   > SQL Server에 Managed Disks를 사용하는 것이 좋습니다. Managed Disks는 배후에서 스토리지를 처리해줍니다. 또한 Managed Disks가 있는 가상 머신이 동일한 가용성 집합에 속할 경우 Azure는 스토리지 리소스를 배포하여 적절한 중복성을 제공합니다. For more information, see [Azure Managed Disks Overview](../managed-disks-overview.md). 가용성 집합의 Managed Disks에 대한 구체적인 내용을 보려면 [가용성 집합에서 VM에 Managed Disks 사용](../manage-availability.md)을 참조하세요.

![SQL VM Disk settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>네트워킹

On the **Networking** tab, configure your networking options. 

* Create a new **virtual network**, or use an existing vNet for your SQL Server VM. Designate a **Subnet** as well. 

* Under **NIC network security group**, select either a basic security group, or the advanced security group. Choosing the basic option allows you to select inbound ports for the SQL Server VM (the same values that were configured on the **Basic** tab). Selecting the advanced option allows you to choose an existing network security group, or create a new one. 

* 네트웨크 설정을 변경해도 되고 기본 값을 그대로 유지해도 됩니다.

![SQL VM Networking settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>모니터링

On the **Monitoring** tab, configure monitoring and autoshutdown. 

* Azure enables **Boot diagnostics** by default with the same storage account designated for the VM. You can change these settings here, as well as enabling **OS guest diagnostics**. 
* You can enable **System assigned managed identity** and **autoshutdown** on this tab as well. 

![SQL VM management settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configure SQL Server settings

On the **SQL Server settings** tab, configure specific settings and optimizations for SQL Server. The settings that you can configure for SQL Server include the following:

| 설정 |
| --- |
| [연결](#connectivity) |
| [인증](#authentication) |
| [Azure Key Vault 통합](#azure-key-vault-integration) |
| [스토리지 구성](#storage-configuration) |
| [자동화된 패치](#automated-patching) |
| [자동화된 Backup](#automated-backup) |
| [Machine Learning Services](#machine-learning-services) |


### <a name="connectivity"></a>연결

**SQL 연결**에서 VM의 SQL Server 인스턴스에 대해 원하는 액세스 유형을 지정합니다. 이 연습에서는 **공개(인터넷)** 를 지정하여 인터넷 상의 컴퓨터 또는 서비스에서 SQL Server로의 연결을 허용합니다. With this option selected, Azure automatically configures the firewall and the network security group to allow traffic on the port selected.

> [!TIP]
> 기본적으로 SQL Server는 잘 알려진 포트 **1433**에서 수신 대기합니다. 보안 향상을 위해 이전 대화 상자의 포트를 기본 이외 포트(예: 1401)를 수신하도록 변경하세요. 포트를 변경할 경우 SSMS와 같이 모든 클라이언트 도구의 해당 포트를 사용하여 연결해야 합니다.

![SQL VM Security](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

인터넷을 통해 SQL Server에 연결하려면 SQL Server 인증을 사용하도록 설정해야 합니다. 이 내용은 다음 섹션에 설명되어 있습니다.

인터넷을 통해 데이터베이스 엔진에 대한 연결을 사용하도록 설정하지 않으려면 다음 옵션 중 하나를 선택합니다.

* **로컬(VM 내부만)** 을 선택합니다.
* **프라이빗(Virtual Network 내부)** 은 동일한 가상 네트워크의 머신 또는 서비스에서 SQL Server로의 연결을 허용합니다.

일반적으로, 시나리오에 허용되는 가장 제한적인 연결을 선택하여 보안을 개선합니다. 하지만 모든 옵션은 네트워크 보안 그룹 및 SQL/Windows 인증을 통해 보안을 설정할 수 있습니다. VM이 만들어진 후에 네트워크 보안 그룹을 편집할 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 보안 고려 사항](virtual-machines-windows-sql-security.md)을 참조하세요.



### <a name="authentication"></a>Authentication

If you require SQL Server Authentication, click **Enable** under **SQL authentication** on the **SQL Server settings** tab.

![공개](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> 인터넷(공용 연결 옵션)을 통해 SQL Server에 액세스하려는 경우 여기에서 SQL 인증을 사용해야 합니다. SQL Server에 대한 공용 액세스를 위해서는 SQL 인증을 사용해야 합니다.

SQL Server 인증을 사용하도록 설정하는 경우 **로그인 이름** 및 **암호**를 지정합니다. This login name is configured as a SQL Server Authentication login and member of the **sysadmin** fixed server role. 인증 모드에 대한 자세한 내용은 [인증 모드 선택](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) 을 참조하세요.

SQL Server 인증을 사용하도록 설정하지 않으면, VM의 로컬 관리자 계정을 사용하여 SQL Server 인스턴스에 연결할 수 있습니다.


### <a name="azure-key-vault-integration"></a>Azure Key Vault 통합

To store security secrets in Azure for encryption, select **SQL Server settings**, and scroll down to  **Azure key vault integration**. Select **Enable** and fill in the requested information. 

![Azure Key Vault 통합](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

다음 표에서는 Azure Key Vault 통합을 구성하는 데 필요한 매개 변수를 나열합니다.

| 매개 변수 | 설명 | 예 |
| --- | --- | --- |
| **주요 자격 증명 모음 URL** |주요 자격 증명 모음의 위치입니다. |https:\//contosokeyvault.vault.azure.net/ |
| **주체 이름** |Azure Active Directory 서비스 주체 이름. 이 이름을 클라이언트 ID라고도 합니다. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **주체 암호** |Azure Active Directory 서비스 주체 암호입니다. 이 암호를 클라이언트 암호라고도 합니다. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **자격 증명 이름** |**자격 증명 이름**: AKV 통합은 VM이 주요 자격 증명 모음에 액세스할 수 있도록 SQL Server 내에 자격 증명을 만듭니다. 이 자격 증명의 이름을 선택하세요. |mycred1 |

자세한 내용은 [Azure VM에서 SQL Server에 대한 Azure Key Vault 통합 구성](virtual-machines-windows-ps-sql-keyvault.md)을 참조하세요.

### <a name="storage-configuration"></a>Storage 구성

On the **SQL Server settings** tab, under **Storage configuration**, select **Change configuration** to open the Performance Optimized Storage Configuration page and specify the storage requirements.

![SQL VM Storage configuration](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

**다음에 대해 Storage 최적화**에서 다음 옵션 중 하나를 선택합니다.

* **일반** 은 기본 설정이며 대부분의 워크로드를 지원합니다.
* **Transactional processing** optimizes the storage for traditional database OLTP workloads.
* **데이터 웨어하우징**은 분석 및 보고 워크로드용으로 스토리지를 최적화합니다.

![SQL VM Storage configuration](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

You can choose to leave the values at default, or you can manually change the storage topology to suit your IOPS needs. For more information, see [storage configuration](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server License
If you're a Software Assurance customer, you can utilize the [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) to bring your own SQL Server license and save on resources. 

![SQL VM License](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>자동화된 패치

**Automated patching** 가 사용됩니다. Azure에서는 자동화된 패치를 통해 SQL Server와 운영 체제를 자동으로 패치합니다. 요일, 시간 및 유지 관리 기간에 대한 날짜를 지정합니다. Azure에서 유지 관리 기간에 패치를 수행합니다. 유지 관리 기간 일정에서는 VM 로캘 시간을 사용합니다. Azure에서 SQL Server와 운영 체제를 자동으로 패치하지 않으려면 **사용 안 함**을 클릭합니다.  

![SQL VM automated patching](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

자세한 내용은 [Azure Virtual Machines에서 SQL Server의 자동화된 패치](virtual-machines-windows-sql-automated-patching.md)를 참조하세요.

### <a name="automated-backup"></a>자동화된 백업

**자동화된 백업**에서 모든 데이터베이스에 대해 자동 데이터베이스 백업을 사용하도록 설정합니다. 자동화된 백업은 기본적으로 사용하지 않도록 설정됩니다.

SQL 자동화된 백업을 사용하도록 설정하면 다음 설정을 구성할 수 있습니다.

* 백업에 대한 보존 기간(일)
* 백업에 사용할 Storage 계정
* 백업을 위한 암호화 옵션 및 암호
* 시스템 데이터베이스 Backup
* 백업 일정 구성

백업을 암호화하려면 **사용**을 클릭합니다. 그 다음 **암호**를 지정합니다. Azure에서는 백업을 암호화할 인증서를 만들고 지정된 암호를 사용하여 인증서를 보호합니다. By default the schedule is set automatically, but you can create a manual schedule by selecting **Manual**. 

![SQL VM automated backups](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 백업](virtual-machines-windows-sql-automated-backup.md)을 참조하세요.


### <a name="machine-learning-services"></a>Machine Learning 서비스

You have the option to enable [Machine Learning Services](/sql/advanced-analytics/). This option enables you to use machine learning with Python and R in SQL Server 2017. Select **Enable** on the **SQL Server Settings** window.


## <a name="4-review--create"></a>4. Review + create

**검토 + 만들기** 탭에서 요약을 검토하고 **만들기**를 선택하여 이 VM에 지정된 SQL Server, 리소스 그룹 및 리소스를 만듭니다.

Azure Portal에서 배포를 모니터링할 수 있습니다. 화면 맨 위에 있는 **알림** 단추는 배포의 기본 상태를 표시합니다.

> [!NOTE]
> 배포 시간에 대한 정보를 제공하기 위해 SQL VM을 미국 동부 지역에 기본 설정을 사용하여 배포해 두었습니다. 이 테스트 배포를 완료하는 데 약 12분이 걸렸습니다. 사용자의 지역 및 선택한 설정에 따라서 배포 시간이 더 빠르거나 늦을 수 있습니다.

## <a id="remotedesktop"></a> 원격 데스크톱을 사용하여 VM 열기

다음 단계를 사용하여 원격 데스크톱으로 SQL Server 가상 머신에 연결합니다.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

SQL Server 가상 머신에 연결된 후에, SQL Server Management Studio를 시작하고 로컬 관리자 자격 증명을 사용하여 Windows 인증으로 연결할 수 있습니다. SQL Server 인증을 사용하도록 설정한 경우에는, 프로비전 중에 구성해 놓은 SQL 로그인 및 암호를 사용하여 SQL 인증에 연결할 수 있습니다.

컴퓨터에 연결하면 요구 사항에 따라 컴퓨터와 SQL Server 설정을 직접 변경할 수 있습니다. 예를 들어, 방화벽 설정을 구성하거나 SQL Server 구성 설정을 변경할 수 있습니다.

## <a id="connect"></a> 원격으로 SQL Server에 연결

이 연습에서는 가상 머신과 **SQL Server 인증**에 대해 **공용** 액세스를 선택했습니다. 이러한 설정은 가상 머신이 인터넷을 통한 모든 클라이언트의 SQL Server 연결을 허용하도록 자동으로 구성합니다(올바른 SQL 로그인이 있다는 가정 하에).

> [!NOTE]
> 프로비전 중에 공용을 선택하지 않은 경우 프로비전 후 포털을 통해 SQL 연결 설정을 변경할 수 있습니다. 자세한 내용은 [SQL 연결 설정 변경](virtual-machines-windows-sql-connect.md#change)을 참조하세요.

다음 섹션은 인터넷을 통해 SQL Server VM 인스턴스에 연결하는 방법을 보여줍니다.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > This example uses the common port 1433. However, this value will need to be modified if a different port (such as 1401) was specified during the deployment of the SQL Server VM. 


## <a name="next-steps"></a>다음 단계

Azure에서 SQL Server를 사용하는 방법에 대한 기타 정보는 [Azure Virtual Machines의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) 및 [질문과 대답](virtual-machines-windows-sql-server-iaas-faq.md)을 참조하세요.
