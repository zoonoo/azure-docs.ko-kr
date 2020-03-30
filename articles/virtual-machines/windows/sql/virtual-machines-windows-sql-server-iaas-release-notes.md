---
title: Azure 가상 머신의 SQL 서버에 대한 설명서 변경 | 마이크로 소프트 문서
description: Azure VM에서 SQL Server의 새로운 기능 및 개선 사항에 대해 알아보기
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
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201648"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 가상 머신의 SQL 서버에 대한 설명서 변경 사항

Azure를 사용하면 SQL Server 이미지가 내장된 가상 시스템(VM)을 배포할 수 있습니다. 이 문서에서는 [Azure 가상 머신에서 SQL Server의](https://azure.microsoft.com/services/virtual-machines/sql-server/)최근 릴리스에서 새로운 기능 및 개선 사항과 관련된 설명서 변경 내용을 요약합니다. 


## <a name="january-2020"></a>2020년 1월

| 변경 | 세부 정보 |
| --- | --- |
| **Azure 정부 지원** | 이제 [Azure 정부](https://azure.microsoft.com/global-infrastructure/government/) 클라우드에서 호스팅되는 가상 시스템에 대해 SQL VM 리소스 공급자에 SQL Server 가상 컴퓨터를 등록할 수 있습니다. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|변경 | 세부 정보 |
 --- | --- |
| **Azure에서 무료 DR 복제본** | [소프트웨어 보증이](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)있는 경우 온-프레미스 SQL Server 인스턴스에 대해 Azure에서 재해 복구를 위한 [무료 수동 인스턴스를](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) 호스트할 수 있습니다. | 
| **대량 리소스 공급자 등록** | 이제 리소스 공급자에 SQL 가상 컴퓨터를 [대량 등록할](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) 수 있습니다. | 
|**성능 최적화 스토리지 구성** | 이제 새 SQL Server VM을 만들 때 [저장소 구성을 완전히 사용자 지정할](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) 수 있습니다. |
|**FCI를 위한 프리미엄 파일 공유** | 이제 [저장소 공간 직접의](virtual-machines-windows-portal-sql-create-failover-cluster.md)원래 방법 대신 [프리미엄 파일 공유를](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) 사용하여 장애 조치 클러스터 인스턴스를 만들 수 있습니다. 
| **Azure 전용 호스트** | [Azure 전용 호스트에서](virtual-machines-windows-sql-dedicated-host.md)SQL Server VM을 실행할 수 있습니다. | 
| **SQL VM을 다른 지역으로 이동** | Azure 사이트 복구를 사용하여 [SQL Server VM을 한 리전에서 다른 리전으로 마이그레이션합니다.](virtual-machines-windows-sql-move-different-region.md) |
|  **새로운 SQL IaaS 설치 모드** | 이제 SQL Server 서비스를 다시 시작하지 않도록 SQL Server IaaS 확장을 [경량 모드로](virtual-machines-windows-sql-server-agent-extension.md) 설치할 수 있습니다.  |
| **SQL Server 에디션 수정** | 이제 SQL Server VM의 [에디션 속성을](virtual-machines-windows-sql-change-edition.md) 변경할 수 있습니다. |
| **SQL VM 리소스 공급자에 대한 변경 사항** | 새 SQL IaaS 모드를 사용하여 [SQL VM 리소스 공급자에 SQL Server VM을 등록할](virtual-machines-windows-sql-register-with-resource-provider.md) 수 있습니다. 이 기능에는 [Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) 이미지가 포함됩니다.|
| **Azure 하이브리드 혜택을 사용하여 사용자 고유의 라이선스 이미지 가져오기** | 이제 Azure Marketplace에서 배포된 사용자 고유의 라이선스 이미지를 [라이선스 유형을 종량제로](virtual-machines-windows-sql-ahb.md#remarks)전환할 수 있습니다.| 
| **Azure 포털의 새 SQL Server VM 관리** | 이제 Azure 포털에서 SQL Server VM을 관리할 수 있는 방법이 있습니다. 자세한 내용은 [Azure 포털의 SQL 서버 VM 관리를](virtual-machines-windows-sql-manage-portal.md)참조하십시오.  | 
| **SQL Server 2008/2008 R2에 대한 확장지원** | Azure *VM으로 마이그레이션하여* SQL Server 2008 및 SQL Server 2008 R2에 대한 [지원을 확장합니다.](virtual-machines-windows-sql-server-2008-eos-extend-support.md) | 
| **사용자 지정 이미지 지원 가능성** | 이제 [SQL Server IaaS 확장을](virtual-machines-windows-sql-server-agent-extension.md#installation) 사용자 지정 OS 및 SQL 이미지에 설치할 수 있으며, 유연한 [라이선스의](virtual-machines-windows-sql-ahb.md)제한된 기능을 제공합니다. SQL 리소스 공급자에 사용자 지정 이미지를 등록할 때 라이선스 유형을 "AHUB"로 지정합니다. 그렇지 않으면 등록이 실패합니다. | 
| **명명된 인스턴스 지원 가능성** | 이제 기본 인스턴스가 제대로 제거된 경우 명명된 인스턴스와 함께 [SQL Server IaaS 확장을](virtual-machines-windows-sql-server-agent-extension.md#installation) 사용할 수 있습니다. | 
| **포털 향상** | SQL Server VM 을 배포하기 위한 Azure 포털 환경이 유용성을 향상시키기 위해 개선되었습니다. 자세한 내용은 간단한 [빠른 시작](quickstart-sql-vm-create-portal.md) 및 SQL Server VM 배포방법에 대한 보다 철저한 [방법 가이드를](virtual-machines-windows-portal-sql-server-provision.md) 참조하십시오.|
| **포털 개선** | 이제 [Azure 포털을](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider)사용하여 SQL Server VM의 라이선스 모델을 종량제에서 사용자 고유의 라이선스로 변경할 수 있습니다.|
| **Azure SQL Server VM CLI를 사용 하 여 가용성 그룹 배포의 단순화** | 이제 Azure의 SQL Server VM에 가용성 그룹을 배포하는 것이 그 어느 때보다 쉬워졌습니다. [Azure CLI를](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) 사용하여 명령줄에서 Windows 장애 조치 클러스터, 내부 로드 밸런서 및 가용성 그룹 리스너를 만들 수 있습니다. 자세한 내용은 [Azure SQL Server VM CLI를 사용하여 Azure VM에서 SQL Server에 대한 Always On 가용성 그룹을 구성합니다.](virtual-machines-windows-sql-availability-group-cli.md) | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 변경 | 세부 정보 |
| --- | --- |
|  **SQL Server 클러스터에 대한 새 리소스 공급자** | 새 리소스 공급자(Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)는 Windows 장애 조치 클러스터의 메타데이터를 정의합니다. *SQLVirtualMachineGroups에* SQL Server VM을 조인하면 WINDOWS 서버 장애 조치 클러스터(WSFC) 서비스를 부트스트랩하고 VM을 클러스터에 조인합니다.  |
| **Azure 빠른 시작 템플릿을 사용 하 여 가용성 그룹 배포의 자동화 된 설정** |이제 Windows 장애 조치 클러스터를 만들고, SQL Server VM에 참여하고, 수신기를 만들고, 두 개의 Azure 빠른 시작 템플릿으로 내부 로드 밸런서를 구성할 수 있습니다. 자세한 내용은 [Azure 빠른 시작 템플릿 사용을 참조하여 Azure VM에서 SQL Server에 대한 Always On 가용성 그룹을 구성합니다.](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 
| **SQL VM 리소스 공급자에 대한 자동 등록** | 이번 달 이후에 배포되는 SQL Server VM은 새로운 SQL Server 리소스 공급자에 자동으로 등록됩니다. 이 달 이전에 배포된 SQL Server VM은 여전히 수동으로 등록해야 합니다. 자세한 내용은 [SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL Server 가상 컴퓨터 등록을](virtual-machines-windows-sql-register-with-resource-provider.md)참조합니다.|
|**새 SQL VM 리소스 공급자**: |  새 리소스 공급자(Microsoft.SqlVirtualMachine)는 SQL Server VM을 보다 잘 관리합니다. VM 등록에 대한 자세한 내용은 [SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL Server 가상 컴퓨터 등록을](virtual-machines-windows-sql-register-with-resource-provider.md)참조합니다. |
|**라이선스 모델 전환** | 이제 Azure CLI 또는 PowerShell을 사용하여 SQL Server VM에 대한 종량제 모델과 사용자 라이선스 모델 간에 전환할 수 있습니다. 자세한 내용은 [Azure에서 SQL Server 가상 컴퓨터에 대한 라이선스 모델을 변경하는 방법을](virtual-machines-windows-sql-ahb.md)참조하세요. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>추가 리소스

**윈도우 VM**:

* [Windows VM의 SQL 서버 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM에서 데이터베이스를 SQL 서버로 마이그레이션](virtual-machines-windows-migrate-sql.md)
* [Azure 가상 머신의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 가상 머신의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)
* [Azure 가상 머신의 SQL Server에 대한 응용 프로그램 패턴 및 개발 전략](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**리눅스 VM**:

* [Linux VM의 SQL Server 개요](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL 서버 리눅스 가상 컴퓨터 프로비전](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ(Linux)](../../linux/sql/sql-server-linux-faq.md)
* [리눅스 문서에 SQL 서버](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
