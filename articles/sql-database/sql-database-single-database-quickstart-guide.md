---
title: 빠른 시작 - Azure SQL Database의 단일 데이터베이스 | Microsoft Docs
description: Azure SQL Database의 단일 데이터베이스로 빠르게 시작하는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 0dc9d18ea4dae6ea0f45d1a91392eb1994e6e80e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757240"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Azure SQL Database의 단일 데이터베이스로 빠르게 시작하기

[단일 데이터베이스](sql-database-single-index.yml)는 완전 관리형 PaaS DbaaS(Database as a Service)이며, 최신 클라우드 기반 애플리케이션에 이상적인 스토리지 엔진입니다. 이 섹션에서는 SQL Database에서 신속하게 단일 데이터베이스를 구성하고 만드는 방법을 알아봅니다.

## <a name="quickstart-overview"></a>빠른 시작 개요

이 섹션에서는 단일 데이터베이스를 신속하게 시작하는 데 사용할 수 있는 문서에 대한 개요를 알아봅니다. 다음 빠른 시작에 따라 신속하게 단일 데이터베이스를 만들고, 데이터베이스 서버 방화벽 규칙을 구성하고, `.bacpac` 파일을 사용하여 데이터베이스를 새 단일 데이터베이스로 가져올 수 있습니다.

- [Azure Portal을 사용하여 단일 데이터베이스 만들기](sql-database-single-database-get-started.md)
- 데이터베이스를 만든 후에는 [방화벽 규칙을 구성하여 데이터베이스를 보호](sql-database-server-level-firewall-rule.md)해야 합니다.
- SQL Server에 Azure로 마이그레이션하려는 기존 데이터베이스가 있는 경우 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 설치해야 합니다. DMA는 SQL Server의 데이터베이스를 분석하여 단일 데이터베이스로 마이그레이션하는 옵션을 차단할 수 있는 문제를 찾습니다. 문제가 없으면 데이터베이스를 `.bacpac` 파일로 내보내서 [Azure Portal이나 SqlPackage를 사용하여 가져오면](sql-database-import.md) 됩니다.

## <a name="automating-management-operations"></a>관리 작업 자동화

PowerShell 또는 Azure CLI를 사용하여 데이터베이스를 만들고, 구성하고, 크기를 조정할 수 있습니다.

- [PowerShell을 사용하여 단일 데이터베이스를 만들고 구성](scripts/sql-database-create-and-configure-database-powershell.md)
- [Azure CLI를 사용하여 단일 데이터베이스를 만들고 구성](scripts/sql-database-create-and-configure-database-cli.md)
- [PowerShell을 사용하여 단일 데이터베이스를 업데이트하고 리소스 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Azure CLI를 사용하여 단일 데이터베이스를 업데이트하고 리소스 크기 조정](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>가동 중지 시간을 최소화하면서 단일 데이터베이스 마이그레이션

이러한 빠른 시작에 따라 `.bacpac` 파일을 사용하여 신속하게 데이터베이스를 만들거나 Azure로 가져올 수 있습니다. 하지만 `.bacpac` 및 `.dacpac` 파일은 Azure SQL Database 내에서 배포 옵션을 사용하여 서로 다른 버전의 SQL Server 간에 신속하게 데이터베이스를 이동하거나 DevOps 파이프라인에서 지속적인 통합을 구현하는 용도로 설계되었습니다. 그러나 이 방법은 가동 중지 시간을 최소화하면서 프로덕션 데이터베이스를 마이그레이션하도록 설계되지는 않았습니다. 새 데이터 추가를 중지하고, 원본 데이터베이스를 `.bacpac` 파일로 내보낸 다음, Azure SQL Database로 가져오는 작업이 완료될 때까지 기다려야 합니다. 이 모든 대기 시간은 애플리케이션의 가동 중지 시간으로 이어지고, 특히 대형 데이터베이스에서 더 많이 발생합니다. 프로덕션 데이터베이스를 이동하려면 마이그레이션 가동 중지 시간을 최소화할 수 있는 더 좋은 마이그레이션 방법이 필요합니다. 이렇게 하려면 [DMS(Data Migration Service)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json)를 사용하여 최소한의 가동 중지 시간으로 데이터베이스를 마이그레이션하세요. DMS는 이를 위해 원본 데이터베이스의 변경 내용을 복원 중인 단일 데이터베이스에 점진적으로 푸시합니다. 이 방식을 사용하면 가동 중지 시간을 최소화하면서 원본에서 대상 데이터베이스로 애플리케이션을 신속하게 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database에서 지원되는 기능의 개략적인 목록](sql-database-features.md)을 찾습니다.
- [데이터베이스를 더 안전하게 보호](sql-database-security-tutorial.md)하는 방법을 알아봅니다.
- [Azure SQL Database에서 단일 데이터베이스를 사용하는 방법](sql-database-howto-single-database.md)에서 고급 작업 방법을 자세히 알아보세요.
- [PowerShell](sql-database-powershell-samples.md) 및 [Azure CLI](sql-database-cli-samples.md)로 작성된 샘플 스크립트를 찾아봅니다.
- 데이터베이스를 구성하는 데 사용할 수 있는 [관리 API](sql-database-single-databases-manage.md)에 대해 자세히 알아봅니다.
