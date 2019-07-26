---
title: Azure VM의 SQL Server 릴리스 정보 | Microsoft Docs
description: Azure VM - SQL Server의 새로운 기능 및 향상된 기능에 대한 자세한 정보
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: e7dea69b507117ba8a3765b5e9bc1fad46be4c53
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444202"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure Virtual Machines의 SQL Server 릴리스 정보

Azure를 사용하면 SQL Server 이미지가 기본 제공된 가상 머신을 배포할 수 있습니다. 이 문서에서는 [Azure 가상 머신의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 최신 릴리스에 제공되는 새 기능과 향상된 기능을 요약합니다. 이 문서에는 릴리스와 직접적인 관련은 없지만 동일한 시간대에 게시된 주목할 만한 콘텐츠 업데이트도 나열되어 있습니다. 다른 Azure 서비스의 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조하세요.


## <a name="june-2019"></a>2019년 6월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| **새 SQL IaaS 설치 모드** | 이제 SQL Server 서비스를 다시 시작 하지 않도록 [경량 모드로](virtual-machines-windows-sql-server-agent-extension.md) SQL IaaS 확장을 설치할 수 있습니다.  |
| **SQL Server 버전 수정** | 이제 SQL Server VM의 [버전 속성](virtual-machines-windows-sql-change-edition.md) 을 변경할 수 있습니다. |
| **SQL VM RP 변경 내용** | 새 SQL IaaS 모드를 사용 하 여 [Windows 2008 이미지](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms) 를 포함 하 [는 sql VM 리소스 공급자를](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) 사용 하 여 SQL Server VM를 등록할 수 있습니다. |
| **AHUB를 사용 하는 BYOL 이미지** | Marketplace에서 배포 된 BYOL 이미지는 이제 [라이선스 유형을 ' PAYG '로](virtual-machines-windows-sql-ahb.md#remarks)전환할 수 있습니다.| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>2019년 5월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| **Azure Portal의 새 SQL VM 관리** | 이제 Azure Portal에서 SQL Server VM를 관리 하는 새로운 방법이 있습니다. 자세한 내용은 [Azure Portal에서 SQL Server VM 관리](virtual-machines-windows-sql-manage-portal.md)를 참조 하세요.  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 | 세부 정보 |
| --- | --- |
| **새 SQL VM 포털 관리** | 문서 약 수십 개는 새로운 SQL VM 관리 포털 환경으로 업데이트 되었습니다. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>2019년 4월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| **SQL Server 2008/2008 r 2 R2 지원 확장** | 있는 *그대로* Azure VM으로 마이그레이션하여 SQL Server 2008 및 SQL Server 2008 r 2에 대 한 [지원을 확장](virtual-machines-windows-sql-server-2008-eos-extend-support.md) 합니다. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>3 월 2019

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| **사용자 지정 이미지 지원 가능성** | 이제 [유연한 라이선스](virtual-machines-windows-sql-ahb.md)의 제한 된 기능을 제공 하는 사용자 지정 OS 및 sql 이미지에 [sql IaaS 확장](virtual-machines-windows-sql-server-agent-extension.md#installation) 을 설치할 수 있습니다. SQL 리소스 공급자를 사용 하 여 사용자 지정 이미지를 등록할 때 라이선스 유형을 ' AHUB '로 지정 합니다. 그렇지 않으면 등록이 실패 합니다. | 
| **명명 된 인스턴스 지원 가능성** | 이제 기본 인스턴스가 제대로 제거 된 경우 [SQL IaaS 확장](virtual-machines-windows-sql-server-agent-extension.md#installation) 을 명명 된 인스턴스와 함께 사용할 수 있습니다. | 
| **포털 기능 향상** | SQL Server VM 배포에 대 한 Azure Portal 환경은 유용성을 개선 하기 위해 개선 된 되었습니다. 자세한 내용은 간략 한 [빠른](quickstart-sql-vm-create-portal.md) 시작 및 더 철저 한 [방법](virtual-machines-windows-portal-sql-server-provision.md) 가이드를 참조 하 여 SQL Server VM 배포를 참조 하세요.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>2019년 2월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| **포털 개선** | 이제를 사용 하 여 SQL Server VM에 대 한 라이선스 모델을 종 량 제로 변경 하 여 사용자 라이선스를 제공할 수 있습니다 [Azure Portal](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)|
|**Azure SQL VM CLI를 사용 하 여 AG 배포 간소화** | 이제 Azure의 SQL Server VM에 가용성 그룹을 배포 하는 것이 더 쉬워졌습니다. [AZURE SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) 를 사용 하면 명령줄에서 WSFC, ILB 및 AG 수신기를 모두 만들 수 있습니다. 자세한 내용은 azure [SQL VM CLI를 사용 하 여 AZURE VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성을](virtual-machines-windows-sql-availability-group-cli.md)참조 하세요. | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018년 12월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| **새 SQL 클러스터 그룹 리소스 공급자** | Windows 장애 조치(failover) 클러스터의 메타데이터를 정의하는 새 리소스 공급자(Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)입니다. SQL Server VM을 *SqlVirtualMachineGroups*에 연결하면 Windows 장애 조치(failover) 클러스터 서비스가 부트스트랩되고 VM이 클러스터에 연결됩니다.  |
|**Azure 빠른 시작 템플릿을 사용하여 가용성 그룹 배포 설정 자동화** |이제 Windows 장애 조치(failover) 클러스터를 만들고, SQL Server VM을 해당 클러스터에 연결하고, 수신기를 만들고, 두 Azure 빠른 시작 템플릿을 사용하여 내부 Load Balancer를 구성할 수 있습니다. 자세한 내용은 [Azure 빠른 시작 템플릿을 사용 하 여 AZURE VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성을](virtual-machines-windows-sql-availability-group-quickstart-template.md)참조 하세요. | 
| **자동 SQL VM 리소스 공급자 등록** | 이번 달 이후에 배포되는 SQL Server VM은 새로운 SQL Server 리소스 공급자에 자동으로 등록됩니다. 이번 달 이전에 배포된 SQL Server VM은 계속해서 수동으로 등록해야 합니다. 자세한 내용은 [SQL vm 리소스 공급자를 사용 하 여 기존 SQL Vm 등록](virtual-machines-windows-sql-register-with-resource-provider.md)을 참조 하세요.|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018년 11월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| **새 SQL VM 리소스 공급자**: |  SQL Server VM의 효과적인 관리를 지원하는 새 SQL Server VM용 리소스 공급자(Microsoft.SqlVirtualMachine)입니다. VM 등록에 대한 자세한 내용은 [새 리소스 공급자에 기존 SQL VM 등록](virtual-machines-windows-sql-register-with-resource-provider.md)을 참조하세요. |
|**라이선스 모델 전환** | 이제 Azure CLI 또는 PowerShell을 사용하여 SQL VM의 종량제 모델과 사용자 라이선스 필요 모델 간에 전환할 수 있습니다. 자세한 내용은 [SQL VM의 라이선스 모델을 변경하는 방법](virtual-machines-windows-sql-ahb.md)을 참조하세요. | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>추가 자료

**Windows VM**:

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)
* [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines의 SQL Server에 대한 애플리케이션 패턴 및 개발 전략](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux Virtual Machine 프로비전](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ(Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
