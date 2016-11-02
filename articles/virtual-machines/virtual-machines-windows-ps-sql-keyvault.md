<properties
	pageTitle="Azure VM에서 SQL Server에 대한 Azure 주요 자격 증명 모음 통합 구성(리소스 관리자)"
	description="Azure 키 자격 증명 모음과 함께 사용하도록 SQL Server 암호화 구성을 자동화하는 방법에 대해 알아보세요. 이 항목에서는 Azure 주요 자격 증명 모음 통합을 리소스 관리자로 만든 SQL Server 가상 컴퓨터와 함께 사용하는 방법에 대해 설명합니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/14/2016"
	ms.author="jroth"/>

# Azure VM에서 SQL Server에 대한 Azure 주요 자격 증명 모음 통합 구성(리소스 관리자)

> [AZURE.SELECTOR]
- [리소스 관리자](virtual-machines-windows-ps-sql-keyvault.md)
- [클래식](virtual-machines-windows-classic-ps-sql-keyvault.md)

## 개요
[TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE(열 수준 암호화)](https://msdn.microsoft.com/library/ms173744.aspx), [백업 암호화](https://msdn.microsoft.com/library/dn449489.aspx) 등 여러 SQL Server 암호화 기능이 있습니다. 이러한 형태의 암호화는 암호화에 사용되는 암호화 키를 관리 및 저장해야 합니다. AKV(Azure 키 자격 증명 모음) 서비스는 안전하고 가용성이 높은 위치에서 이러한 키의 보안 및 관리를 개선하도록 설계되었습니다. [SQL Server 커넥터](http://www.microsoft.com/download/details.aspx?id=45344)는 SQL Server가 Azure 키 자격 증명 모음의 키를 사용할 수 있게 해줍니다.

온-프레미스 컴퓨터로 SQL Server를 실행하는 경우 [온-프레미스 SQL Server 컴퓨터에서 Azure 키 자격 증명 모음에 액세스할 수 있는 단계](https://msdn.microsoft.com/library/dn198405.aspx)가 있습니다. 하지만 Azure VM의 SQL Server에서는 *Azure 주요 자격 증명 모음 통합* 기능을 사용하여 시간을 절약할 수 있습니다.

이 기능은 활성화되면 자동으로 SQL Server 커넥터를 설치하고, Azure 키 자격 증명 모음에 액세스하도록 EKM 공급자를 구성하고, 사용자가 자격 증명 모음에 액세스할 수 있도록 자격 증명을 만듭니다. 앞에서 언급한 온-프레미스 설명서의 단계를 살펴보셨다면 이 기능이 2 및 3단계를 자동화한다는 것을 알 수 있습니다. 사용자가 수동으로 해야 하는 유일한 일은 키 자격 증명 모음 및 키를 만드는 작업입니다. 그 이후에 SQL VM을 설정하는 전체 과정이 자동화됩니다. 이 기능이 설정을 완료하면 사용자는 T-SQL 문을 실행하여 평소와 같이 데이터베이스 암호화 또는 백업을 시작할 수 있습니다.

[AZURE.INCLUDE [AKV 통합 준비](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## AKV 통합 설정 및 구성
기존 VM에 대해 프로비전닝 또는 구성하는 동안 AKV 통합을 설정할 수 있습니다.

### 새 VM
리소스 관리자와 함께 새 SQL Server 가상 컴퓨터를 프로비전하는 경우 Azure 포털은 Azure 키 자격 증명 모음 통합을 사용하도록 단계를 제공합니다.

![SQL Azure 주요 자격 증명 모음 통합](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

프로비전의 자세한 연습은 [Azure 포털에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

### 기존 VM
기존 SQL Server 가상 컴퓨터에 대한 해당 SQL Server 가상 컴퓨터를 선택합니다. 그런 다음 **설정** 블레이드의 **SQL Server 구성** 섹션을 선택합니다.

![기존 VM에 대한 SQL AKV 통합](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

**SQL Server 구성** 블레이드에서 자동화된 주요 자격 증명 모음 통합 섹션의 **편집** 단추를 클릭합니다.

![기존 VM에 대한 SQL AKV 통합 구성](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

완료되면 **SQL Server 구성** 블레이드 아래쪽의 **확인** 단추를 클릭하여 변경 내용을 저장합니다.

>[AZURE.NOTE] 또한 템플릿을 사용하여 AKV 통합을 구성할 수 있습니다. 자세한 내용은 [Azure 주요 자격 증명 모음 통합을 위한 Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)을 참조하세요.

[AZURE.INCLUDE [AKV 통합 다음 단계](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_0720_2016-->