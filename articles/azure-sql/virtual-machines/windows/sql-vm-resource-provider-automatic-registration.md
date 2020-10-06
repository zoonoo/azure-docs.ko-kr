---
title: SQL VM 리소스 공급자를 사용 하 여 자동 등록
description: 자동 등록 기능을 사용 하도록 설정 하 여 Azure Portal를 사용 하 여 이전 및 미래의 모든 SQL Server Vm을 SQL VM 리소스 공급자에 자동으로 등록 하는 방법을 알아봅니다.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: 45285f2f26f1f17408f97bfede2b97e4c4752a5c
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762468"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>SQL VM 리소스 공급자를 사용 하 여 자동 등록
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Portal에서 자동 등록 기능을 사용 하도록 설정 하 여 Azure Vm의 현재 및 미래의 모든 SQL Server를 경량 모드의 SQL VM 리소스 공급자와 자동으로 등록 합니다.

이 문서에서는 자동 등록 기능을 사용 하도록 설정 하는 방법을 설명 합니다. 또는 [단일 vm을 등록](sql-vm-resource-provider-register.md)하거나 SQL vm 리소스 공급자를 사용 하 여 [vm을 대량으로 등록할](sql-vm-resource-provider-bulk-register.md) 수 있습니다. 

## <a name="overview"></a>개요

[SQL VM 리소스 공급자](sql-vm-resource-provider-register.md#overview) 를 사용 하 여 Azure Portal에서 SQL Server VM를 관리할 수 있습니다. 또한 리소스 공급자는 [자동화 된 패치](automated-patching.md), [자동화 된 백업](automated-backup.md)뿐만 아니라 모니터링 및 관리 효율성 기능을 비롯 한 강력한 기능 집합을 사용 하도록 설정 합니다. 또한 [라이선스](licensing-model-azure-hybrid-benefit-ahb-change.md) 및 [버전](change-sql-server-edition.md) 유연성이 잠금 해제됩니다. 이전에는 Azure Marketplace에서 배포된 SQL Server VM 이미지에만 이러한 기능을 사용할 수 있었습니다. 

자동 등록 기능을 통해 고객은 Azure 구독에서 현재 및 미래의 모든 SQL Server Vm을 SQL VM 리소스 공급자와 함께 자동으로 등록할 수 있습니다. 이는 현재 SQL Server Vm에만 초점을 맞춘 수동 등록과는 다릅니다. 

자동 등록은 SQL Server Vm을 경량 모드로 등록 합니다. 전체 기능 집합을 활용 하려면 여전히 [완전 한 관리 효율성 모드로 수동으로 업그레이드](sql-vm-resource-provider-register.md#upgrade-to-full) 해야 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

리소스 공급자에 SQL Server VM을 등록하려면 다음이 필요합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) 를 사용 하는 Azure 리소스 모델 [Windows 가상 머신은](../../../virtual-machines/windows/quick-create-portal.md) 공용 또는 Azure Government 클라우드에 배포 됩니다. 


## <a name="enable"></a>사용

Azure Portal에서 SQL Server Vm의 자동 등록을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [**SQL 가상 컴퓨터**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스 페이지로 이동 합니다. 
1. 자동 **SQL Server VM 등록** 을 선택 하 여 **자동 등록** 페이지를 엽니다. 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="자동 SQL Server VM 등록을 선택 하 여 자동 등록 페이지를 엽니다.":::

1. 드롭다운에서 구독을 선택 합니다. 
1. 약관을 읽고 동의 하면 동의 **함**을 선택 합니다. 
1. **등록** 을 선택 하 여 기능을 사용 하도록 설정 하 고 SQL vm 리소스 공급자를 사용 하 여 현재 및 미래의 모든 SQL Server vm을 자동으로 등록 합니다. 이는 Vm에서 SQL Server 서비스를 다시 시작 하지 않습니다. 

## <a name="disable"></a>사용 중지

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps) 를 사용 하 여 자동 등록 기능을 사용 하지 않도록 설정 합니다. 자동 등록 기능을 사용 하지 않도록 설정 하면 구독에 추가 된 SQL Server Vm을 SQL VM 리소스 공급자에 수동으로 등록 해야 합니다. 이미 등록 된 기존 SQL Server Vm의 등록은 취소 되지 않습니다.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 자동 등록을 해제 하려면 다음 명령을 실행 합니다. 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell를 사용 하 여 자동 등록을 해제 하려면 다음 명령을 실행 합니다. 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---


## <a name="next-steps"></a>다음 단계

SQL VM 리소스 공급자가 제공 하는 전체 기능 집합을 활용 하려면 관리 효율성 모드를 [full](sql-vm-resource-provider-register.md#upgrade-to-full) 로 업그레이드 합니다. 
