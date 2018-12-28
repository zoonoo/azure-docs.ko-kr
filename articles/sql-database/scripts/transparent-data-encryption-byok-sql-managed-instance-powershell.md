---
title: 'PowerShell: BYOK TDE 사용 - Azure SQL Database Managed Instance | Microsoft Docs'
description: PowerShell을 사용하여 저장 데이터 암호화에 BYOK TDE(투명한 데이터 암호화)를 사용하도록 Azure SQL Managed Instance를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: d428c74a486c4bcb6ea1568fbb8abd813960c708
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976646"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault의 사용자 고유 키를 사용하여 Managed Instance에서 투명한 데이터 암호화 관리

이 PowerShell 스크립트 예제는 Azure Key Vault의 키를 사용하여 Azure SQL Managed Instance에 대한 Bring Your Own Key 시나리오에서 TDE(투명한 데이터 암호화)를 구성합니다. BYOK(Bring Your Own Key) 지원을 통한 TDE에 대한 자세한 내용은 [Azure SQL에 대한 TDE Bring Your Own Key](../transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure PowerShell 모듈 버전이 5.7.0 이상이어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

PowerShell을 로컬로 설치하여 사용하려면 이 자습서에 AzureRM.Sql PowerShell 패키지의 미리 보기 버전인 *4.11.6-preview*가 필요합니다. `Install-Module -Name AzureRM.Sql -RequiredVersion 4.11.6-preview -AllowPrerelease` 명령을 실행하여 PowerShell을 설치합니다.

## <a name="sample-scripts"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
