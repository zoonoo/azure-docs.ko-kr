---
title: 'PowerShell: BYOK(Bring Your Own Key) TDE 사용'
titleSuffix: Azure SQL Managed Instance
description: PowerShell을 통해 미사용 암호화에 BYOK(Bring Your Own Key) TDE(투명한 데이터 암호화)를 사용하도록 Azure SQL Managed Instance를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323235"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault의 사용자 고유 키를 사용하는 SQL Managed Instance의 투명한 데이터 암호화

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

이 PowerShell 스크립트 예제에서는 Azure Key Vault의 키를 사용하여 Azure SQL Managed Instance에 대해 고객 관리형 키로 TDE(투명한 데이터 암호화)를 구성합니다. 일반적으로 이 구성을 TDE의 BYOK(Bring Your Own Key) 시나리오라고도 합니다. 자세한 내용은 [고객 관리형 키를 사용한 Azure SQL 투명한 데이터 암호화](../../database/transparent-data-encryption-byok-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 기존 관리형 인스턴스. [PowerShell을 사용하여 관리형 인스턴스 만들기](create-configure-managed-instance-powershell.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

로컬에서 PowerShell을 사용하거나 Azure Cloud Shell을 사용하려면 Azure PowerShell 2.3.2 이상 버전이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하거나 아래 샘플 스크립트를 실행하여 현재 사용자에 대해 모듈을 설치합니다.

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

SQL Managed Instance에 대한 추가 PowerShell 스크립트 샘플은 [Azure SQL Managed Instance PowerShell 스크립트](../../database/powershell-script-content-guide.md)에서 찾을 수 있습니다.
