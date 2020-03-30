---
title: 'PowerShell: BYOK TDE - Azure SQL 데이터베이스 관리 인스턴스 사용 '
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691400"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault의 사용자 고유 키를 사용하여 Managed Instance에서 투명한 데이터 암호화 관리

이 PowerShell 스크립트 예제에서는 Azure 키 자격 증명 모음의 키를 사용하여 Azure SQL 관리 인스턴스에 대한 고객 관리 키로 투명 데이터 암호화(TDE)를 구성합니다. 이를 TDE에 대한 사용자 고유의 키 가져오기 시나리오라고도 합니다. 고객 관리 키가 있는 TDE에 대해 자세히 알아보려면 [Azure SQL에 사용자 고유키 가져오기](../transparent-data-encryption-byok-azure-sql.md)를 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

- 기존 관리되는 인스턴스입니다. [PowerShell 사용을 사용하여 Azure SQL Database 관리 인스턴스를 만듭니다.](sql-database-create-configure-managed-instance-powershell.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

로컬로 PowerShell을 사용하거나 Azure 클라우드 셸을 사용하려면 AZ PowerShell 2.3.2 또는 이후 버전이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 설치 모듈을](/powershell/azure/install-az-ps)참조하거나 아래 샘플 스크립트를 실행하여 현재 사용자에 대한 모듈을 설치합니다.

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
