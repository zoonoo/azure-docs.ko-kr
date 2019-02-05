---
title: 빠른 시작 - Azure SQL Database Managed Instance | Microsoft Docs
description: Azure SQL Database - Managed Instance를 빠르게 시작하는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468175"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance 시작하기

[Azure SQL Database Managed Instance](sql-database-managed-instance-index.yml)는 Azure 클라우드에서 호스팅되는 SQL Server의 완전 관리형 PaaS 버전이며 사설 IP 주소로 자체 VNet에 배치됩니다. 이 섹션에서는 Managed Instance를 신속하게 구성 및 생성하고 데이터베이스를 마이그레이션하는 방법을 알아봅니다.

## <a name="quickstart-overview"></a>빠른 시작 개요

이 섹션에서는 Managed Instance를 신속하게 시작하는 데 사용할 수 있는 문서에 대한 개요를 알아봅니다. 첫 번째 Managed Instance를 만드는 가장 쉬운 방법은 [Azure Portal](sql-database-managed-instance-get-started.md)을 사용하는 것입니다. 여기에서 필요한 매개 변수(사용자 이름/암호, 코어 수, 최대 스토리지)를 구성하고, 네트워킹 세부 정보 및 인프라 요구 사항에 대해 알아볼 필요 없이 Azure 네트워크 환경을 자동으로 만들 수 있습니다. 인스턴스를 만들도록 허용된 [구독 유형](sql-database-managed-instance-resource-limits.md#supported-subscription-types)만 있으면 됩니다.

사용하려는 자체 네트워크가 있거나 네트워크를 사용자 지정하려는 경우에는 Managed Instance에 대한 [네트워크 환경 구성](#configure-network-environment) 방법을 참조하세요.

Managed Instance를 만들 때 다음 방법 중 하나를 사용하여 인스턴스에 연결해야 합니다.

* 설치된 SQL Server Management Studio 및 Managed Instance가 있는 동일한 VNet 내의 서브넷에서 Managed Instance에 액세스하는 데 사용할 수 있는 기타 앱을 사용하여 [Azure Virtual Machine](sql-database-managed-instance-configure-vm.md)을 만듭니다. VM은 Managed Instance와 동일한 서브넷에 있을 수 없습니다.
* Managed Instance가 배치되는 VNet에 조인하고 Managed Instance를 네트워크의 다른 SQL Server로 사용할 수 있게 해주는 컴퓨터에 [지점 및 사이트간 연결](sql-database-managed-instance-configure-p2s.md)을 설정합니다.

대안으로 로컬 네트워크에서 사이트 간 연결 또는 Express 경로를 사용할 수 있지만 이러한 방법은 이 빠른 시작의 범위를 벗어납니다.

Managed Instance를 만들고 액세스를 구성할 때 SQL Server 온-프레미스 또는 Azure VM에 배치된 데이터베이스 마이그레이션을 시작할 수 있습니다. 마이그레이션할 원본 데이터베이스에 지원되지 않는 기능이 있으면 마이그레이션이 실패합니다. 실패를 방지하고 호환성을 확인하려면 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 설치하면 됩니다. DMA는 SQL Server의 데이터베이스를 분석하여 Managed Instance로 마이그레이션을 차단할 수 있는 문제(예: 여러 로그 파일 또는 FileStream 존재)를 찾습니다. 이러한 문제를 해결하면 데이터베이스가 Managed Instance로 이동할 준비가 됩니다. [데이터베이스 실험 도우미](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/)는 또 다른 유용한 도구입니다. SQL Server에 워크로드를 기록하고 Managed Instance에서 재생할 수 있기 때문에 Managed Instance로 마이그레이션할 때 성능 문제가 있는지를 확인할 수 있습니다.

Managed Instance로 데이터베이스를 마이그레이션할 수 있다고 확신이 서면, Transact-SQL 명령을 사용하여 데이터베이스 백업을 만들고 Transact-SQL 명령을 사용하여 Blob 스토리지에서 Azure Blob Storage 및 RESTORE 데이터베이스에 업로드할 수 있는 [네이티브 복원](sql-database-managed-instance-get-started-restore.md) 기능을 사용하면 됩니다.

이 빠른 시작을 사용하면 Managed Instance에서 데이터베이스를 신속하게 구성하고, 만들고, 배치할 수 있습니다. 일부의 경우, Managed Instance 배포와 필요한 네트워킹 환경을 사용자 지정하거나 자동화해야 합니다. 이러한 시나리오는 아래 설명되어 있습니다.

## <a name="customizing-network-environment"></a>네트워크 환경 사용자 지정

[Azure Portal](sql-database-managed-instance-get-started.md)을 사용하여 인스턴스를 만드는 경우 VNet/서브넷을 자동으로 구성할 수 있지만, VNet과 서브넷의 매개 변수를 구성할 수 있기 때문에 Managed Instance를 만들기 전에 인스턴스를 만드는 것이 좋습니다. 네트워크 환경을 만들고 구성하는 가장 쉬운 방법은 인스턴스가 배치되는 곳에 네트워크와 서브넷을 만들고 구성하는 [Azure Resource 배포](sql-database-managed-instance-create-vnet-subnet.md) 템플릿을 사용하는 것입니다. Azure Resource Manager 배포 단추를 누르고 양식에 매개 변수를 입력하기만 하면 됩니다. 또는 [PowerShell 스크립트](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/)를 사용하여 네트워크 만들기를 자동화할 수 있습니다.

Managed Instance를 배포하려는 곳에 VNet과 서브넷이 이미 있는 경우에는 VNet과 서브넷이 [네트워킹 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)을 충족하는지 확인해야 합니다. 이 [PowerShell 스크립트를 사용하여 서브넷이 제대로 구성되어 있는지 확인](sql-database-managed-instance-configure-vnet-subnet.md)해야 합니다. 이 스크립트는 네트워크 유효성을 검사하여 문제를 보고할 뿐만 아니라 변경해야 하는 항목을 알려주고 VNet/서브넷에서 필요한 사항을 변경하도록 제안합니다. 이 스크립트는 VNet/서브넷을 수동으로 구성하지 않으려는 경우에 실행하고, 네트워크 인프라를 대대적으로 재구성한 후에도 실행해야 합니다. 자체 네트워크를 만들고 구성하려는 경우 [Managed Instance 설명서](sql-database-managed-instance-connectivity-architecture.md)와 [이 가이드](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)를 참조하세요.

## <a name="automating-creation-of-managed-instance"></a>Managed Instance 만들기 자동화

 이전 단계에서 설명한 대로 네트워크 환경을 만들지 않았다면 Azure Portal에서 대신 만들 수 있습니다. 이 때 유일한 단점은 나중에 변경할 수 없는 몇 가지 기본 매개 변수가 구성에 사용된다는 점입니다. 이에 대한 대안으로 [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/), [Resource Manager 템플릿과 PowerShell](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) 또는 [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/)를 사용할 수 있습니다.

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>최소 가동 중지 시간으로 Managed Instance로 마이그레이션

빠른 시작의 문서를 참고하여 Managed Instance를 신속하게 설정하고 데이터베이스를 이동할 수 있습니다. 단, 네이티브 복원을 사용하는 경우 데이터베이스가 복원될 때까지 기다려야 하고, 특히 데이터베이스가 클수록 애플리케이션 가동 중지 시간이 길어질 수 있습니다. 프로덕션 데이터베이스를 이동하는 경우에는, 마이그레이션의 가동 중지 시간을 최소화할 수 있는 더 좋은 마이그레이션 방법이 필요합니다. [데이터 마이그레이션 서비스](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json)는 원본 데이터베이스에 변경한 내용을 Managed Instance로 복원 중인 데이터베이스에 증분 방식으로 푸시하여 가동 중지 시간을 최소화시키면서 데이터베이스를 마이그레이션할 수 있는 마이그레이션 서비스입니다. 이 방식을 사용하면 가동 중지 시간을 최소화하면서 원본에서 대상 데이터베이스로 애플리케이션을 신속하게 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Managed Instance에 지원되는 기능에 대한 개략적인 목록](sql-database-features.md)과 [세부 정보 및 알려진 문제](sql-database-managed-instance-transact-sql-information.md)를 찾아봅니다. 
* [Managed Instance의 기술적인 특성](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)을 자세히 알아봅니다. 
* [방법 섹션](sql-database-howto-managed-instance.md)에서 고급 자습서를 찾아봅니다. 
