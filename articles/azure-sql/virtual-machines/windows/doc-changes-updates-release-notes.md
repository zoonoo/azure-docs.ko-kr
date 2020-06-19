---
title: Azure 가상 머신의 SQL Server 설명서 변경 내용| Microsoft Docs
description: Azure VM의 SQL Server에서 새로운 기능 및 향상된 기능을 알아봅니다.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: f87d72df977e98c60948389d794eb102ac08f8d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032474"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 가상 머신의 SQL Server 설명서 변경 내용
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure에서는 SQL Server 이미지가 기본 제공된 VM(가상 머신)을 배포할 수 있습니다. 이 문서에서는 최신 릴리스의 [Azure 가상 머신의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)에서 새로운 기능 및 향상된 기능과 관련된 설명서 변경 내용을 요약합니다. 


## <a name="january-2020"></a>2020년 1월

| 변경 | 세부 정보 |
| --- | --- |
| **Azure Government 지원** | 이제 [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) 클라우드에서 호스트되는 가상 머신에 대한 SQL VM 리소스 공급자에 SQL Server 가상 머신을 등록할 수 있습니다. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|변경 | 세부 정보 |
 --- | --- |
| **Azure의 무료 DR 복제본** | [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)가 있는 경우 온-프레미스 SQL Server 인스턴스 재해 복구를 위한 [무료 수동 인스턴스](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)를 Azure에서 호스트할 수 있습니다. | 
| **대량 리소스 공급자 등록** | 이제 리소스 공급자에 SQL 가상 머신을 [대량 등록](sql-vm-resource-provider-bulk-register.md)할 수 있습니다. | 
|**성능 최적화 스토리지 구성** | 이제 새 SQL Server VM을 만들 때 [스토리지 구성를 완전히 사용자 지정](storage-configuration.md#new-vms)할 수 있습니다. |
|**FCI용 프리미엄 파일 공유** | 이제 원래 방법인 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 대신 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md)를 사용하여 장애 조치(failover) 클러스터 인스턴스를 만들 수 있습니다. 
| **Azure 전용 호스트** | [Azure 전용 호스트](dedicated-host.md)에서 SQL Server VM을 실행할 수 있습니다. | 
| **다른 지역으로 SQL VM 이동** | Azure Site Recovery를 사용하여 [SQL Server VM을 한 지역에서 다른 지역으로 마이그레이션](move-sql-vm-different-region.md)할 수 있습니다. |
|  **새 SQL IaaS 설치 모드** | 이제 SQL Server 서비스를 다시 시작하지 않도록 SQL Server IaaS 확장을 [경량 모드](sql-server-iaas-agent-extension-automate-management.md)에서 설치할 수 있습니다.  |
| **SQL Server 버전 수정** | 이제 SQL Server VM에 대한 [버전 속성](change-sql-server-edition.md)을 변경할 수 있습니다. |
| **SQL VM 리소스 공급자 변경 내용** | 새 SQL IaaS 모드를 사용하여 [SQL Server VM을 SQL VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)할 수 있습니다. 이 기능에는 [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) 이미지가 포함됩니다.|
| **Azure 하이브리드 혜택을 사용하는 BYOL 이미지** | Azure Marketplace에서 배포된 BYOL 이미지는 이제 [라이선스 유형을 종량제](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)로 전환할 수 있습니다.| 
| **Azure Portal의 새 SQL Server VM 관리** | 이제 Azure Portal에서 SQL Server VM를 관리하는 새로운 방법이 있습니다. 자세한 내용은 [Azure Portal에서 SQL Server VM 관리](manage-sql-vm-portal.md)를 참조하세요.  | 
| **SQL Server 2008/2008 R2에 대한 지원 연장** | Azure VM으로 있는 그대로 마이그레이션하여 SQL Server 2008 및 SQL Server 2008 R2에 대한 [지원을 연장](sql-server-2008-extend-end-of-support.md)합니다. | 
| **사용자 지정 이미지 지원 가능성** | 이제 [SQL Server IaaS 확장](sql-server-iaas-agent-extension-automate-management.md#installation)을 사용자 지정 OS 및 SQL 이미지에 설치할 수 있습니다. 이는 [유연한 라이선스](licensing-model-azure-hybrid-benefit-ahb-change.md)의 제한된 기능을 제공합니다. SQL 리소스 공급자에 사용자 지정 이미지를 등록하는 경우 라이선스 유형을 "AHUB"로 지정합니다. 그렇지 않으면 등록이 실패합니다. | 
| **명명된 인스턴스 지원 가능성** | 이제 기본 인스턴스가 제대로 제거되면 [SQL Server IaaS 확장](sql-server-iaas-agent-extension-automate-management.md#installation)을 명명된 인스턴스와 함께 사용할 수 있습니다. | 
| **포털 기능 향상** | SQL Server VM 배포를 위한 Azure Portal 환경이 사용성을 향상하도록 수정되었습니다. SQL Server VM 배포에 대한 자세한 내용은 간략한 [빠른 시작](sql-vm-create-portal-quickstart.md) 또는 보다 자세한 [방법 가이드](create-sql-vm-portal.md)를 참조하세요.|
| **포털 개선** | 이제 [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider)을 사용하여 SQL Server VM의 라이선스 모델을 종량제에서 BYOL로 변경할 수 있습니다.|
| **Azure SQL Server VM CLI를 사용하여 가용성 그룹 배포 간소화** | 이제 Azure에서 SQL Server VM에 가용성 그룹을 배포하기가 더 쉬워졌습니다. [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid)를 사용하여 Windows 장애 조치(failover) 클러스터, 내부 부하 분산 장치, 가용성 그룹 수신기를 모두 명령줄에서 만들 수 있습니다. 자세한 내용은 [Azure SQL Server VM CLI를 사용하여 Azure VM에서 SQL Server에 대한 Always On 가용성 그룹 구성](availability-group-az-cli-configure.md)을 참조하세요. | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 변경 | 세부 정보 |
| --- | --- |
|  **SQL Server 클러스터에 대한 새 리소스 공급자** | 새 리소스 공급자(Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)는 Windows 장애 조치(failover) 클러스터의 메타데이터를 정의합니다. SQL Server VM을 *SqlVirtualMachineGroups*에 조인하면 WSFC(Windows Server 장애 조치(failover) 클러스터) 서비스가 부트스트랩되고 VM이 클러스터에 연결됩니다.  |
| **Azure 빠른 시작 템플릿을 사용하여 가용성 그룹 배포 설정 자동화** |이제 Windows 장애 조치(failover) 클러스터를 만들고, SQL Server VM을 해당 클러스터에 연결하고, 수신기를 만들고, 두 Azure 빠른 시작 템플릿을 사용하여 내부 부하 분산 장치를 구성할 수 있습니다. 자세한 내용은 [Azure 빠른 시작 템플릿을 사용하여 Azure VM에서 SQL Server에 대한 Always On 가용성 그룹 구성](availability-group-quickstart-template-configure.md)을 참조하세요. | 
| **SQL VM 리소스 공급자에 대한 등록 자동화** | 이번 달 이후에 배포되는 SQL Server VM은 새로운 SQL Server 리소스 공급자에 자동으로 등록됩니다. 이번 달 이전에 배포된 SQL Server VM은 계속해서 수동으로 등록해야 합니다. 자세한 내용은 [Azure의 SQL Server 가상 머신을 SQL VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)을 참조하세요.|
|**새 SQL VM 리소스 공급자**: |  새 리소스 공급자(Microsoft.SqlVirtualMachine)를 통해 SQL Server VM을 보다 효율적으로 관리할 수 있습니다. VM을 등록하는 방법에 대한 자세한 내용은 [Azure의 SQL Server 가상 머신을 SQL VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)을 참조하세요. |
|**라이선스 모델 전환** | 이제 Azure CLI 또는 PowerShell을 사용하여 SQL Server VM의 라이선스 모델을 종량제와 BYOL 간에 전환할 수 있습니다. 자세한 내용은 [Azure의 SQL Server 가상 머신에 대한 라이선스 모델을 변경하는 방법](licensing-model-azure-hybrid-benefit-ahb-change.md)을 참조하세요. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>추가 리소스

**Windows VM**:

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server Windows VM 프로비전](create-sql-vm-portal.md)
* [Azure VM의 SQL Server로 데이터베이스 마이그레이션](migrate-to-vm-from-sql-server.md)
* [Azure 가상 머신의 SQL Server에 대한 고가용성 및 재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 가상 머신의 SQL Server에 대한 성능 모범 사례](performance-guidelines-best-practices.md)
* [Azure 가상 머신의 SQL Server에 대한 애플리케이션 패턴 및 개발 전략](application-patterns-development-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server Linux 가상 머신 프로비전](../linux/sql-vm-create-portal-quickstart.md)
* [FAQ(Linux)](../linux/frequently-asked-questions-faq.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
