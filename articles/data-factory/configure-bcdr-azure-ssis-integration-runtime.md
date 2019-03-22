---
title: SQL Database 장애 조치(Failover)를 위해 Azure-SSIS Integration Runtime 구성 | Microsoft Docs
description: 이 문서에서는 SSISDB 데이터베이스에 대해 Azure SQL Database 지역에서 복제 및 장애 조치(Failover)를 사용하여 Azure SSIS Integration Runtime을 구성하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: dea0153b9ca6d8e751fd94cc558abd44b2591907
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453034"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Azure SQL Database 지역에서 복제 및 장애 조치(Failover)를 사용하여 Azure-SSIS Integration Runtime 구성

이 문서에서는 SSISDB 데이터베이스에 대해 Azure SQL Database 지역에서 복제를 사용하여 Azure SSIS Integration Runtime을 구성하는 방법을 설명합니다. 장애 조치(Failover)가 발생하는 경우 Azure-SSIS IR이 보조 데이터베이스를 계속 사용하는지 확인할 수 있습니다.

지역에서 복제 및 SQL Database에 대 한 장애 조치 하는 방법에 대 한 자세한 내용은 참조 하세요. [개요: 활성 지역 복제 및 자동 장애 조치 그룹](../sql-database/sql-database-geo-replication-overview.md)합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>시나리오 1 - Azure-SSIS IR이 읽기-쓰기 수신기 엔드포인트를 가리킴

### <a name="conditions"></a>조건

이 섹션은 다음 조건이 충족되면 적용됩니다.

- Azure-SSIS IR이 장애 조치(Failover) 그룹의 읽기-쓰기 수신기 엔드포인트를 가리키고 있습니다.

  AND

- SQL Database 서버가 가상 네트워크 서비스 엔드포인트 규칙으로 구성되지 *않았습니다*.

### <a name="solution"></a>해결 방법

장애 조치(Failover)가 발생하는 경우 Azure-SSIS IR에 투명하게 진행됩니다. Azure-SSIS IR은 장애 조치(Failover) 그룹의 새로운 주 데이터베이스에 자동으로 연결됩니다.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>시나리오 2 - Azure-SSIS IR이 주 서버 엔드포인트를 가리킴

### <a name="conditions"></a>조건

이 섹션은 다음 조건 중 하나라도 충족되면 적용됩니다.

- Azure-SSIS IR이 장애 조치(Failover) 그룹의 주 서버 엔드포인트를 가리키고 있습니다. 이 엔드포인트는 장애 조치(Failover)가 발생하면 변경됩니다.

  또는

- Azure SQL Database 서버가 가상 네트워크 서비스 엔드포인트 규칙으로 구성되어 있습니다.

  또는

- 데이터베이스 서버는 가상 네트워크로 구성된 SQL Database Managed Instance입니다.

### <a name="solution"></a>해결 방법

장애 조치(Failover)가 발생하면 다음을 수행해야 합니다.

1. Azure-SSIS IR을 중지합니다.

2. 새 기본 엔드포인트 및 새 지역의 가상 네트워크를 가리키도록 IR을 다시 구성합니다.

3. IR을 다시 시작합니다.

다음 섹션에서는 이러한 단계를 좀 더 자세히 설명합니다.

### <a name="prerequisites"></a>필수 조건

- 서버에 동시에 가동 중단되는 경우 Azure SQL Database 서버에 대한 재해 복구를 사용하도록 설정했는지 확인합니다. 자세한 내용은 [Azure SQL Database의 비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 참조하세요.

- 현재 지역에서 가상 네트워크를 사용하는 경우 새 지역에서 다른 가상 네트워크를 사용하여 Azure SSIS 통합 런타임에 연결해야 합니다. 자세한 내용은 [Azure-SSIS 통합 런타임을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.

- 사용자 지정 설정을 사용하는 경우 중단 시간 동안 계속 액세스할 수 있도록 사용자 지정 설정 스크립트 및 연결된 파일을 저장하는 Blob 컨테이너에 대한 다른 SAS URI를 준비해야 할 수 있습니다. 자세한 내용은 [Azure-SSIS 통합 런타임에서 사용자 지정 설정 구성](how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요.

### <a name="steps"></a>단계

Azure-SSIS IR을 중지하고, IR을 새 지역으로 전환하고, 다시 시작 하려면 다음 단계를 따릅니다.

1. 원래 지역의 IR을 중지합니다.

2. PowerShell에서 다음 명령을 호출하여 새 설정으로 IR을 업데이트합니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    이 PowerShell 명령에 대한 자세한 내용은 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md) 참조

3. IR을 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

Azure-SSIS IR에 대한 이러한 기타 구성 옵션이 좋습니다.

- [고성능을 위해 Azure-SSIS Integration Runtime 구성](configure-azure-ssis-integration-runtime-performance.md)

- [Azure-SSIS 통합 런타임을 위한 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS Integration Runtime의 Enterprise 버전 프로비전](how-to-configure-azure-ssis-ir-enterprise-edition.md)
