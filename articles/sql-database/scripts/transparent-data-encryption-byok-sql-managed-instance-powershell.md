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
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569691"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Azure Key Vault의 고유한 키를 사용 하 여 Managed Instance에서 투명한 데이터 암호화 관리 (미리 보기)

이 PowerShell 스크립트 예제는 Azure Key Vault의 키를 사용 하 여 Azure SQL Managed Instance의 Bring Your Own Key (미리 보기) 시나리오에서 TDE (투명한 데이터 암호화)를 구성 합니다. BYOK(Bring Your Own Key) 지원을 통한 TDE에 대한 자세한 내용은 [Azure SQL에 대한 TDE Bring Your Own Key](../transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

## <a name="prerequisites"></a>전제 조건

- 기존 Managed Instance입니다. [PowerShell을 사용 하 여 Azure SQL Database 관리 되는 인스턴스 만들기를](sql-database-create-configure-managed-instance-powershell.md)참조 하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 사용 하거나 Azure Cloud Shell을 사용 하려면 AZ PowerShell 1.1.1-preview 이상 preview 버전이 필요 합니다. 업그레이드 해야 하는 경우 모듈 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조 하거나 아래 샘플 스크립트를 실행 하 여 모듈을 설치 합니다.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
