---
title: 빠른 시작 - Azure SQL Database 관리되는 인스턴스 | Microsoft Docs
description: Azure SQL Database - 관리되는 인스턴스를 빠르게 시작하는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a70e83737c6b56aee3279375ec653f12810b13b4
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749817"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Azure SQL Database 관리되는 인스턴스 시작하기

[관리되는 인스턴스](sql-database-managed-instance-index.yml) 배포 옵션은 최신 SQL Server 온-프레미스(Enterprise Edition) 데이터베이스 엔진과 거의 100% 호환되는 데이터베이스를 만들며, 일반적인 보안 문제를 해결하는 원시 [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 구현과 온-프레미스 SQL Server 고객이 편리하게 사용할 수 있는 [비즈니스 모델](https://azure.microsoft.com/pricing/details/sql-database/)을 제공합니다. 이 섹션에서는 관리되는 인스턴스를 신속하게 구성 및 생성하고 데이터베이스를 마이그레이션하는 방법을 알아봅니다.

## <a name="quickstart-overview"></a>빠른 시작 개요

다음 빠른 시작에서는 관리되는 인스턴스를 빠르게 만들고, 클라이언트 애플리케이션에 가상 머신 또는 지점과 사이트 간 VPN 연결을 구성하고, `.bak` 파일을 사용하여 새로운 관리되는 인스턴스로 데이터베이스를 복원할 수 있게 해줍니다.

- [Azure Portal을 사용하여 관리되는 인스턴스 만들기](sql-database-managed-instance-get-started.md). Azure Portal에서 필요한 매개 변수(사용자 이름/암호, 코어 수, 최대 스토리지)를 구성하고, 네트워킹 세부 정보 및 인프라 요구 사항에 대해 알아볼 필요 없이 Azure 네트워크 환경을 자동으로 만들 수 있습니다. 관리되는 인스턴스를 만들도록 허용된 [구독 유형](sql-database-managed-instance-resource-limits.md#supported-subscription-types)만 있으면 됩니다. 사용하려는 자체 네트워크가 있거나 네트워크를 사용자 지정하려는 경우에는 관리되는 인스턴스에 대한 [네트워크 환경 구성](#configure-network-environment) 방법을 참조하세요.
- 관리되는 인스턴스는 공개 엔드포인트를 사용하여 자체 VNet에 생성됩니다. 클라이언트 애플리케이션 액세스의 경우 동일한 VNet(여러 서브넷)에 VM을 만들거나 다음과 같은 빠른 시작 중 하나를 사용하여 클라이언트 컴퓨터에서 VNet에 대한 지점과 사이트 간 VPN 연결을 만들 수 있습니다.
  - 클라이언트 애플리케이션 연결에 사용할 [관리되는 인스턴스 VNet의 Azure Virtual Machine](sql-database-managed-instance-configure-vm.md)(SQL Server Management Studio 포함)을 만듭니다.
  - SQL Server Management Studio 및 기타 클라이언트 연결 애플리케이션이 있는 클라이언트 컴퓨터에서 [관리되는 인스턴스에 대한 지점과 사이트 간 VPN 연결](sql-database-managed-instance-configure-p2s.md)을 설정합니다. 이는 관리되는 인스턴스 및 VNet 연결에 대한 두 가지 옵션입니다.

  > [!NOTE]
  > 또한 로컬 네트워크에서 사이트 간 연결 또는 Express 경로를 사용할 수 있지만 이러한 방법은 이 빠른 시작의 범위를 벗어납니다.

관리되는 인스턴스를 만들고 액세스를 구성한 후 SQL Server 온-프레미스 또는 Azure VM에 배치된 데이터베이스 마이그레이션을 시작할 수 있습니다. 마이그레이션할 원본 데이터베이스에 지원되지 않는 기능이 있으면 마이그레이션이 실패합니다. 실패를 방지하고 호환성을 확인하려면 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 설치하면 됩니다. DMA는 SQL Server의 데이터베이스를 분석하여 관리되는 인스턴스로 마이그레이션을 차단할 수 있는 문제(예: 여러 로그 파일 또는 [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 존재)를 찾습니다. 이러한 문제를 해결하면 데이터베이스를 관리되는 인스턴스로 마이그레이션할 수 있습니다. [데이터베이스 실험 도우미](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/)는 또 다른 유용한 도구입니다. SQL Server에 워크로드를 기록하고 관리되는 인스턴스에서 재생할 수 있기 때문에 관리되는 인스턴스로 마이그레이션할 때 성능 문제가 있는지를 확인할 수 있습니다.

데이터베이스를 관리되는 인스턴스로 마이그레이션할 수 있다는 확신이 들면 원시 SQL Server 복원 기능을 사용하여 `.bak` 파일에서 관리되는 인스턴스로 데이터베이스를 복원할 수 있습니다. 빠른 시작의 경우 [백업에서 관리되는 인스턴스로 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요. 이 빠른 시작에서는 `RESTORE` Transact-SQL 명령을 사용하여 Azure Blob Storage에 저장된 `.bak` 파일로 복원합니다. 

> [!TIP]
> `BACKUP` Transact-SQL 명령을 사용하여 Azure Blob Storage에 데이터베이스 백업을 만들려면 [URL에 SQL Server 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)을 참조하세요.

이 빠른 시작을 사용하면 데이터베이스 백업을 신속하게 만들고, 구성하고, 관리되는 인스턴스로 복원할 수 있습니다. 일부의 경우, 관리되는 인스턴스 배포와 필요한 네트워킹 환경을 사용자 지정하거나 자동화해야 합니다. 이러한 시나리오는 아래 설명되어 있습니다.

## <a name="customizing-network-environment"></a>네트워크 환경 사용자 지정

Azure Portal을 사용하여 인스턴스를 만드는 경우 VNet/서브넷을 자동으로 구성할 수 있지만, VNet과 서브넷의 매개 변수를 구성할 수 있기 때문에 관리되는 인스턴스를 만들기 전에 VNet/서브넷을 만드는 것이 좋습니다. 네트워크 환경을 만들고 구성하는 가장 쉬운 방법은 관리되는 인스턴스를 위한 네트워크와 서브넷을 만들고 구성하는 [Azure Resource 배포](sql-database-managed-instance-create-vnet-subnet.md) 템플릿을 사용하는 것입니다. Azure Resource Manager 배포 단추를 누르고 양식에 매개 변수를 입력하기만 하면 됩니다. 

또는 [PowerShell 스크립트](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/)를 사용하여 네트워크 만들기를 자동화할 수도 있습니다.

관리되는 인스턴스를 배포하려는 곳에 VNet과 서브넷이 이미 있는 경우에는 VNet과 서브넷이 [네트워킹 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)을 충족하는지 확인해야 합니다. 이 [PowerShell 스크립트를 사용하여 서브넷이 제대로 구성되어 있는지 확인](sql-database-managed-instance-configure-vnet-subnet.md)하세요. 이 스크립트는 네트워크 유효성을 검사하여 문제를 보고할 뿐만 아니라 변경해야 하는 항목을 알려주고 VNet/서브넷에서 필요한 사항을 변경하도록 제안합니다. VNet/서브넷을 수동으로 구성하지 않으려는 경우 이 스크립트를 실행하세요. 또한 네트워크 인프라의 모든 주요 재구성 후 실행할 수도 있습니다. 자체 네트워크를 만들고 구성하려면 [연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md) 및 [관리되는 인스턴스 환경 생성 및 구성을 위한 최고의 가이드](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)를 읽어 보세요.

## <a name="automating-creation-of-a-managed-instance"></a>관리되는 인스턴스 만들기 자동화

 이전 단계에서 설명한 대로 네트워크 환경을 만들지 않았다면 Azure Portal에서 대신 만들 수 있습니다. 이 때 유일한 단점은 나중에 변경할 수 없는 몇 가지 기본 매개 변수가 구성에 사용된다는 점입니다. 또는 다음을 사용할 수 있습니다.

- [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
- [Resource Manager와 PowerShell 템플릿](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>최소 가동 중지 시간으로 관리되는 인스턴스로 마이그레이션

빠른 시작의 문서를 참고하여 관리되는 인스턴스를 신속하게 설정하고 원시 `RESTORE` 기능을 사용하여 데이터베이스를 이동할 수 있습니다. 그러나 원시 `RESTORE`를 사용할 경우 데이터베이스가 복원될 때까지(그리고 이미 복원된 경우에는 Azure Blob Storage로 복사될 때까지) 기다려야 합니다. 이렇게 하면 특히 대규모 데이터베이스에서 애플리케이션의 가동 중지 시간이 발생합니다. 프로덕션 데이터베이스를 이동하려면 [DMS(Data Migration Service)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json)를 사용하여 최소 가동 중지 시간으로 데이터베이스를 마이그레이션하세요. DMS는 이를 위해 복원 중인 관리되는 인스턴스 데이터베이스에 원본 데이터베이스의 변경 사항을 점진적으로 내보냅니다. 이 방식을 사용하면 가동 중지 시간을 최소화하면서 원본에서 대상 데이터베이스로 애플리케이션을 신속하게 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Managed Instance에 지원되는 기능에 대한 개략적인 목록](sql-database-features.md)과 [세부 정보 및 알려진 문제](sql-database-managed-instance-transact-sql-information.md)를 찾아봅니다.
- [Managed Instance의 기술적인 특성](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)을 자세히 알아봅니다. 
- [Azure SQL Database에서 관리되는 인스턴스를 사용하는 방법](sql-database-howto-managed-instance.md)에서 고급 작업 방법을 자세히 알아보세요. 
