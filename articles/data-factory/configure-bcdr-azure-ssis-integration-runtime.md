---
title: Azure-SSIS Integration Runtime에 대한 BCDR(비즈니스 연속성 및 재해 복구) 권장 사항 | Microsoft Docs
description: 이 문서에서는 Azure-SSIS Integration Runtime에 대한 비즈니스 연속성 및 재해 복구 권장 사항을 설명합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295309"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime에 대한 BCDR(비즈니스 연속성 및 재해 복구) 권장 사항

재해 복구를 위해 현재 Azure SSIS 통합 런타임이 실행 중인 지역에서 이를 중지하고 다시 시작하기 위해 다른 지역으로 전환할 수 있습니다. 이를 위해 [Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

- 서버에 동시에 가동 중단되는 경우 Azure SQL Database 서버에 대한 재해 복구를 사용하도록 설정했는지 확인합니다. 자세한 내용은 [Azure SQL Database의 비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 참조하세요.

- 현재 지역에서 가상 네트워크를 사용하는 경우 새 지역에서 다른 가상 네트워크를 사용하여 Azure SSIS 통합 런타임에 연결해야 합니다. 자세한 내용은 [Azure-SSIS 통합 런타임을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.

- 사용자 지정 설정을 사용하는 경우 중단 시간 동안 계속 액세스할 수 있도록 사용자 지정 설정 스크립트 및 연결된 파일을 저장하는 Blob 컨테이너에 대한 다른 SAS URI를 준비해야 할 수 있습니다. 자세한 내용은 [Azure-SSIS 통합 런타임에서 사용자 지정 설정 구성](how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요.

## <a name="steps"></a>단계

Azure-SSIS IR을 중지하고, IR을 새 지역으로 전환하고, 다시 시작 하려면 다음 단계를 따릅니다.

1. 원래 지역의 IR을 중지합니다.

2. PowerShell에서 다음 명령을 호출하여 IR 업데이트

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
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
