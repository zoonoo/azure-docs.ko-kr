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
ms.date: 04/09/2019
ms.openlocfilehash: c08b5559fd599fb297f294a54aed67c65676aee4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783398"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault의 사용자 고유 키를 사용하여 Managed Instance에서 투명한 데이터 암호화 관리

이 PowerShell 스크립트 예제는 Azure Key Vault의 키를 사용하여 Azure SQL Managed Instance에 대한 Bring Your Own Key 시나리오에서 TDE(투명한 데이터 암호화)를 구성합니다. BYOK(Bring Your Own Key) 지원을 통한 TDE에 대한 자세한 내용은 [Azure SQL에 대한 TDE Bring Your Own Key](../transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

- 기존 관리 되는 인스턴스. 참조 [Azure SQL Database를 만들려면 PowerShell을 사용 하 여 관리 되는 인스턴스](sql-database-create-configure-managed-instance-powershell.md)합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

모두 PowerShell를 사용 하 여 로컬로 또는 Azure Cloud Shell을 사용 하 여 AZ PowerShell 1.1.1-preview 또는 이상 미리 보기 버전이 필요 합니다. 업그레이드 해야 하는 경우 참조 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps), 실행 또는 아래 샘플 스크립트 모듈을 설치 합니다.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../sql-database-powershell-samples.md)에 있습니다.
