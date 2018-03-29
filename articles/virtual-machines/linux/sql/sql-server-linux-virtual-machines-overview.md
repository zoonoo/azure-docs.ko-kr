---
title: Azure Linux Virtual Machines에서 SQL Server 개요 | Microsoft Docs
description: Azure Linux Virtual Machines에서 전체 SQL Server 버전을 실행하는 방법을 알아봅니다. 모든 Linux SQL Server VM 이미지 및 관련된 내용에 대한 직접 링크를 가져옵니다.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 03/22/2018
ms.author: jroth
ms.openlocfilehash: e752ad844a6efe572564e7081ebac87193e9c2a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure Virtual Machines에서 SQL Server 개요(Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

이 항목에서는 [포털 이미지에 대한 링크](#create)와 함께 Azure Linux VM(Virtual Machines)에 SQL Server를 실행하는 옵션을 설명합니다.

> [!NOTE]
> SQL Server에 이미 익숙하고 SQL Server Linux VM을 배포하는 방법을 확인하려는 경우 [Azure에서 Linux SQL Server VM 프로비전](provision-sql-server-linux-virtual-machine.md)을 참조하세요. 또는 SQL Server에서 Windows VM을 만들려는 경우 [Azure에서 Windows SQL Server VM 프로비전](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

데이터베이스 관리자 또는 개발자인 경우 Azure VM은 온-프레미스 SQL Server 워크로드 및 응용 프로그램을 클라우드로 이동하는 방법을 제공합니다.

## <a name="scenarios"></a>시나리오

Azure에서 데이터를 호스트하도록 선택할 수 있는 여러 가지 이유가 있습니다. 응용 프로그램을 개발하거나 Azure에 마이그레이션하는 경우 성능을 개선하기 위해 Azure에서 백 엔드 데이터를 찾습니다. 글로벌 서비스 및 재해 복구를 위해 여러 데이터 센터에 대한 액세스를 자동으로 가집니다. 데이터를 잘 보호하고 지속합니다.

Azure VM에서 실행하는 SQL Server는 관계형 데이터를 Azure에 저장하기 위한 한 가지 옵션입니다. Azure SQL Database 서비스를 사용하는 옵션도 있을 수 있습니다. Virtual Machines의 SQL Server와 Azure SQL Database 중에서 선택하는 방법에 대한 자세한 내용은 [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)를 참조하세요.

## <a id="create"></a>새 SQL VM 만들기

새 SQL VM을 만드는 단계별 지침은 [Azure에서 Linux SQL Server VM 프로비전](provision-sql-server-linux-virtual-machine.md) 자습서에 있습니다.

다음 표에서는 가상 머신 갤러리의 최신 SQL Server 이미지 매트릭스를 제공합니다. 모든 링크를 클릭하여 지정된 버전, 버전 및 운영 체제로 새 SQL VM 만들기를 시작합니다.

> [!TIP]
> 이러한 이미지의 VM 및 SQL 가격 책정을 이해하려면 [Linux SQL Server VM의 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 참조하세요.

| 버전 | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2017** | RHEL(Red Hat Enterprise Linux) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SLES(SUSE Linux Enterprise Server) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> 사용 가능한 Windows SQL Server 가상 머신 이미지를 보려면 [Azure Virtual Machines의 SQL Server 개요(Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

## <a id="packages"></a>설치된 패키지

Linux에서 SQL Server를 구성하는 경우 요구 사항에 따라 데이터베이스 엔진 패키지 및 여러 가지 선택 사항 패키지를 설치합니다. SQL Server의 Linux 가상 머신 이미지는 대부분의 패키지를 자동으로 설치합니다. 다음 표에서는 각 배포에 설치된 패키지를 보여줍니다.

| 배포 | [데이터베이스 엔진](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [도구](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server 에이전트](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [전체 텍스트 검색](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA 추가 기능](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>다음 단계

Linux에서 SQL Server를 구성하고 사용하는 방법에 대해 자세히 알아보려면 [Linux의 SQL Server 개요](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)를 참조하세요.
