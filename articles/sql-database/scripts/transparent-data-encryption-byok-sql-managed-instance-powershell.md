---
title: 'PowerShell: BYOK TDE를 사용 하도록 설정 Azure SQL Database Managed Instance '
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
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691400"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault의 사용자 고유 키를 사용하여 Managed Instance에서 투명한 데이터 암호화 관리

이 PowerShell 스크립트 예제에서는 Azure Key Vault의 키를 사용 하 여 Azure SQL Managed Instance에 대 한 고객 관리 키를 사용 하 여 TDE (투명한 데이터 암호화)를 구성 합니다. 이를 종종 TDE에 대 한 Bring Your Own Key 시나리오 라고 합니다. 고객 관리 키를 사용 하 여 TDE에 대해 자세히 알아보려면 [AZURE SQL에 대 한 tde Bring Your Own Key를](../transparent-data-encryption-byok-azure-sql.md)참조 하세요.

## <a name="prerequisites"></a>필수 조건

- 기존 Managed Instance입니다. [PowerShell을 사용 하 여 Azure SQL Database 관리 되는 인스턴스 만들기를](sql-database-create-configure-managed-instance-powershell.md)참조 하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 사용 하거나 Azure Cloud Shell을 사용 하려면 AZ PowerShell 2.3.2 이상 버전이 필요 합니다. 업그레이드 해야 하는 경우에는 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조 하거나 아래 샘플 스크립트를 실행 하 여 현재 사용자에 대 한 모듈을 설치 합니다.

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
