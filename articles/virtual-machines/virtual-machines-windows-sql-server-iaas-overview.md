---
title: "Azure 가상 컴퓨터에서 SQL Server 개요 | Microsoft Docs"
description: "Azure 가상 컴퓨터에서 전체 SQL Server 버전을 실행하는 방법을 알아봅니다. 모든 SQL Server VM 이미지 및 관련된 내용에 대한 직접 링크를 가져옵니다."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/01/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: d5169c25d09966ca72ee49ca995a8453ad764177
ms.openlocfilehash: 0e136d81ee3dbe57785d136755d3e800041aca1f


---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Azure 가상 컴퓨터에서 SQL Server 개요
이 항목에서는 [포털 이미지에 대한 링크](#option-1-create-a-sql-vm-with-per-minute-licensing) 및 [일반적인 태스크](#manage-your-sql-vm)의 개요와 함께 Azure VM(가상 컴퓨터)에 SQL Server를 실행하는 옵션을 설명합니다.

> [!NOTE]
> SQL Server에 이미 익숙하고 SQL Server VM을 배포하는 방법을 확인하려는 경우 [Azure Portal에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.
> 
> 

## <a name="overview"></a>개요
데이터베이스 관리자 또는 개발자인 경우 Azure VM은 온-프레미스 SQL Server 워크로드 및 응용 프로그램을 클라우드로 이동하는 방법을 제공합니다. 다음 비디오는 SQL Server Azure VM에 대한 기술적 개요를 제공합니다.

> [!비디오 https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016/player]
> 
> 

이 비디오에서는 다음과 같은 영역을 다룹니다.

| Time | 영역 |
| --- | --- |
| 00:21 |Azure VM이란? |
| 01:45 |보안 |
| 02:50 |연결 |
| 03:30 |저장소 안정성 및 성능 |
| 05:20 |VM 크기 |
| 05:54 |고가용성 및 SLA |
| 07:30 |구성 지원 |
| 08:00 |모니터링 |
| 08:32 |데모: SQL Server 2016 VM 만들기 |

> [!NOTE]
> 비디오에서는 SQL Server 2016을 중점적으로 다루지만 2008, 2012, 2014 및 2016을 비롯한 여러 버전의 Azure SQL Server에 대한 VM 이미지를 제공합니다. 
> 
> 

## <a name="scenarios"></a>시나리오
Azure에서 데이터를 호스트하도록 선택할 수 있는 여러 가지 이유가 있습니다. 응용 프로그램이 Azure로 이동하는 경우 데이터를 이동하는 것이 성능을 향상시킵니다. 하지만 다른 이점도 있습니다. 글로벌 서비스 및 재해 복구를 위해 여러 데이터 센터에 대한 액세스를 자동으로 가집니다. 데이터를 잘 보호하고 지속합니다.

Azure VM에서 실행하는 SQL Server는 관계형 데이터를 Azure에 저장하기 위한 한 가지 옵션입니다. 여기서는 몇 가지 시나리오를 사용하는 것이 좋습니다. 예를 들어, Azure VM을 온-프레미스 SQL Server 컴퓨터와 최대한 비슷하게 구성하려고 합니다. 또는 동일한 데이터베이스 서버에서 추가 응용 프로그램 및 서비스를 실행하려고 합니다. 더 많은 시나리오와 고려 사항을 검토 하는 데 도움이 되는 다음과 같은 두 가지 주요 리소스가 있습니다.

* [Azure 가상 컴퓨터의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)는 Azure VM에서 SQL Server를 사용하기 위한 최상의 시나리오의 개요를 제공합니다. 
* [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) Database 또는 Azure VM의 SQL Server(IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md)에서는 SQL VM과 SQL Database 간의 자세한 비교를 제공합니다.

## <a name="create-a-new-sql-vm"></a>새 SQL VM 만들기
다음 섹션에서는 SQL Server 가상 컴퓨터 갤러리 이미지에 Azure Portal에 대한 직접 링크를 제공합니다. 선택한 이미지에 따라 SQL Server 라이선스 비용을 분당 기준으로 지불하거나 사용자 라이선스가 필요할 수 있습니다(BYOL).

이 프로세스에 대한 단계별 지침은 자습서의 [Azure Portal에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 있습니다. 또한 프로비전 중 사용 가능한 적절한 컴퓨터 크기 및 다른 기능을 선택하는 방법을 설명하는 [SQL Server VM에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 검토합니다.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>옵션 1: 분당 라이선스를 사용한 SQL VM 만들기
다음 표에서는 가상 컴퓨터 갤러리의 최신 SQL Server 이미지 매트릭스를 제공합니다. 모든 링크를 클릭하여 지정된 버전, 버전 및 운영 체제로 새 SQL VM 만들기를 시작합니다. 

| 버전 | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |

이 목록 외에도 SQL Server 버전과 운영 체제의 다른 조합을 사용할 수 있습니다. Azure Portal에서 마켓플레이스 검색을 통해 다른 이미지를 찾아보세요. 

## <a name="a-idbyola-option-2-create-a-sql-vm-with-an-existing-license"></a><a id="BYOL"></a> 옵션 2: 기존 라이선스를 사용한 SQL VM 만들기
사용자 라이선스가 필요할 수도 있습니다(BYOL). 이 시나리오에서는 SQL Server 라이선스에 대한 추가 비용 없이 VM에 대해서만 지불합니다. 사용자 고유 라이선스를 사용하려면 아래의 SQL Server 버전, 버전 및 운영 체제의 매트릭스를 사용합니다. 포털에서 이러한 이미지 이름에는 접두사 **{BYOL}**이 붙습니다.

| 버전 | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

이 목록 외에도 SQL Server 버전과 운영 체제의 다른 조합을 사용할 수 있습니다. Azure Portal에서 마켓플레이스 검색을 통해 다른 이미지를 찾아보세요("{BYOL} SQL Server" 검색).

> [!IMPORTANT]
> BYOL VM 이미지를 사용하려면 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)으로 기업 계약을 체결해야 합니다. 또한 사용하려는 SQL Server의 버전/버전에 대한 유효한 라이선스가 필요합니다. VM 프로비전의 [10](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) 일 내에 **필요한 BYOL 정보를 Microsoft에 제공** 해야 합니다.
> 

> [!NOTE]
> 사용자 고유의 라이선스를 사용하기 위해 분 단위로 요금이 부과되는 SQL Server VM의 라이선스 모델을 변경할 수 없습니다. 이 경우에 새 BYOL VM을 만들고 새 VM에 데이터베이스를 마이그레이션해야 합니다. 

## <a name="manage-your-sql-vm"></a>SQL VM 관리
SQL Server VM을 프로비전한 후 여러 가지 선택적 관리 작업이 있습니다. 여러 측면에서 정확하게 온-프레미스 SQL Server 인스턴스를 관리한 대로 SQL Server를 구성 및 관리합니다. 하지만 일부 태스크는 Azure에 특정됩니다. 다음 섹션에서는 자세한 정보에 대한 링크로 이러한 영역 중 일부를 강조 표시합니다.

### <a name="connect-to-the-vm"></a>VM에 연결
SSMS(SQL Server Management Studio)와 같은 도구를 통해 SQL Server VM에 연결하는 가장 기본적인 관리 단계 중 하나입니다. Azure의 SQL Server 연결 방법에 대한 지침은 [Azure의 SQL Server 가상 컴퓨터에 연결](virtual-machines-windows-sql-connect.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

### <a name="migrate-your-data"></a>데이터 마이그레이션
기존 데이터베이스가 있는 경우 새로 프로비전된 SQL VM으로 이동할 수 있습니다. 마이그레이션 옵션 목록 및 지침은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

### <a name="configure-high-availability"></a>고가용성 구성
고가용성이 필요한 경우 SQL Server 가용성 그룹을 구성하는 것이 좋습니다. 여기에는 가상 네트워크의 여러 Azure VM이 포함됩니다. Azure 포털에는 사용자를 위해 이 구성을 설정하는 템플릿이 있습니다. 자세한 내용은 [Azure Resource Manager 가상 컴퓨터에서 AlwaysOn 가용성 그룹 구성](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 가용성 그룹과 연결된 수신기를 수동으로 구성하려면 [Azure VM의 AlwaysOn 가용성 그룹 구성](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

다른 고가용성 고려 사항은 [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

### <a name="back-up-your-data"></a>데이터 백업
Azure VM은 [자동화된 백업](virtual-machines-windows-sql-automated-backup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 이용할 수 있으며 정기적으로 Blob 저장소에 데이터베이스의 백업을 만듭니다. 수동으로 이 기술을 사용할 수 있습니다. 자세한 내용은 [SQL Server 백업 및 복원에 Azure Storage 사용](virtual-machines-windows-use-storage-sql-server-backup-restore.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 모든 백업 및 복원 옵션에 대한 개요는 [Azure 가상 컴퓨터에서 SQL Server 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

### <a name="automate-updates"></a>업데이트 자동화
Azure VM은 [자동화된 패치](virtual-machines-windows-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 사용하여 중요한 Windows 및 SQL Server 업데이트를 자동으로 설치하는 유지 관리 창을 예약할 수 있습니다.

### <a name="customer-experience-improvement-program-ceip"></a>CEIP(사용자 환경 개선 프로그램)
CEIP(사용자 환경 개선 프로그램)를 사용하도록 기본 설정되어 있습니다. 보고서를 주기적으로 Microsoft에 전송하여 SQL Server를 개선하는 데 도움이 됩니다. 프로비전한 후에도 계속 사용하려면 CEIP에 필요한 관리 태스크는 없습니다. 원격 데스크톱을 사용하여 VM에 연결하여 CEIP를 사용자 지정하거나 사용하지 않도록 설정할 수 있습니다. 그런 다음 **SQL Server 오류 및 사용 보고** 유틸리티를 실행합니다. 보고를 해제하려면 지침을 따릅니다. 

자세한 내용은 [사용 조건 동의](https://msdn.microsoft.com/library/ms143343.aspx) 항목의 CEIP 섹션을 참조하세요. 

## <a name="next-steps"></a>다음 단계
[학습 경로를 탐색](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) 합니다.

가격 책정에 대한 질문은 [가격](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)을 참조하세요. **OS/소프트웨어** 목록에서 SQL Server의 대상 버전을 선택합니다. 그런 다음 다양한 크기의 가상 컴퓨터에 대한 가격을 확인합니다. 

추가 질문이 있나요? 먼저 [Azure 가상 컴퓨터의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 또한 SQL VM 항목의 맨 아래에 질문 또는 의견을 추가하여 Microsoft 및 커뮤니티와 상호 의견을 교환하세요.




<!--HONumber=Dec16_HO3-->


