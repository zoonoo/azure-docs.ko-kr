---
title: Azure VM의 SQL Server 릴리스 정보 | Microsoft Docs
description: Azure VM - SQL Server의 새로운 기능 및 향상된 기능에 대한 자세한 정보
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: e3f44181ca1a5ea64815aadf52aa7ea792a21416
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358578"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure Virtual Machines의 SQL Server 릴리스 정보

Azure를 사용하면 SQL Server 이미지가 기본 제공된 가상 머신을 배포할 수 있습니다. 이 문서에는 Azure Virtual Machine에 배포된 최신 버전의 SQL Server에서 예상할 수 있는 새로운 기능 및 향상된 기능이 나와 있습니다. 

## <a name="december-2018"></a>2018년 12월

| **변경** | 세부 정보 |
| --- | --- |
| **새 SQL 클러스터 그룹 리소스 공급자** | Windows 장애 조치(failover) 클러스터에 대한 메타데이터를 정의하는 새 리소스 공급자(Microsoft.SqlVirtualMachine/SqlVirtualMachineGroup)가 있습니다. SQL Server VM을 *SqlVirtualMachineGroup*에 연결하면 Windows 장애 조치(failover) 클러스터 서비스가 부트스트랩되고 VM이 클러스터에 연결됩니다.  |
|**Azure 빠른 시작 템플릿을 사용하여 가용성 그룹 배포 설정 자동화** |이제 Windows 장애 조치(failover) 클러스터를 만들고, SQL Server VM을 해당 클러스터에 연결하고, 수신기를 만들고, 두 Azure 빠른 시작 템플릿을 사용하여 내부 Load Balancer를 구성할 수 있습니다. 자세한 내용은 [Azure 빠른 시작 템플릿을 사용하여 SQL Server VM에서 WSFC, 수신기 만들기 및 Always On 가용성 그룹에 대해 ILB 구성](virtual-machines-windows-sql-availability-group-quickstart-template.md)을 참조하세요. | 
| **자동 SQL VM 리소스 공급자 등록** | 이번 달 이후에 배포되는 SQL Server VM은 새로운 SQL Server 리소스 공급자에 자동으로 등록됩니다. 이번 달 이전에 배포된 SQL Server VM은 계속해서 수동으로 등록해야 합니다. 자세한 내용은 [새 리소스 공급자에 기존 SQL VM 등록](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider)을 참조하세요.|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018년 11월

| **변경** | 세부 정보 |
| --- | --- |
| **새 SQL VM 리소스 공급자**: |  SQL Server VM의 효과적인 관리를 지원하는 SQL Server VM용 새 리소스 공급자(Microsoft.SqlVirtualMachine)가 있습니다. VM 등록에 대한 자세한 내용은 [새 리소스 공급자에 기존 SQL VM 등록](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-new-resource-provider)을 참조하세요. |
|**라이선스 모델 전환** |이제 Azure CLI 또는 PowerShell을 사용하여 SQL VM의 종량제 모델과 사용자 라이선스 필요 모델 간에 전환할 수 있습니다. 자세한 내용은 [SQL VM의 라이선스 모델을 변경하는 방법](virtual-machines-windows-sql-ahb.md)을 참조하세요. | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>추가 리소스

**Windows VM**:

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)
* [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines의 SQL Server에 대한 애플리케이션 패턴 및 개발 전략](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM 프로비전](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ(Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
