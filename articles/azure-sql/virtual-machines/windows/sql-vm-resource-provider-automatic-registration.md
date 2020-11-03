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
ms.openlocfilehash: 23ecc3bdfb0ca85caf219fc262348937923f53c3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286123"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>SQL VM 리소스 공급자를 사용 하 여 자동 등록
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Portal에서 자동 등록 기능을 사용 하 여 Azure Virtual Machines (Vm)의 모든 현재 및 향후 SQL Server를 경량 모드의 SQL VM 리소스 공급자와 자동으로 등록 합니다. SQL VM 리소스 공급자에 등록 하면 [Sql IaaS 에이전트 확장이](sql-server-iaas-agent-extension-automate-management.md)설치 됩니다.

이 문서에서는 자동 등록 기능을 사용 하도록 설정 하는 방법을 설명 합니다. 또는 [단일 vm을 등록](sql-vm-resource-provider-register.md)하거나 SQL vm 리소스 공급자를 사용 하 여 [vm을 대량으로 등록할](sql-vm-resource-provider-bulk-register.md) 수 있습니다. 

## <a name="overview"></a>개요

SQL VM 리소스 공급자를 사용 하 여 SQL Server VM를 등록 하면 [Sql IaaS 에이전트 확장이](sql-server-iaas-agent-extension-automate-management.md)설치 됩니다. 

자동 등록을 사용 하도록 설정 하면 작업이 매일 실행 되어 구독의 등록 되지 않은 모든 Vm에 SQL Server 설치 되어 있는지 여부를 검색 합니다. 이 작업은 SQL IaaS 에이전트 확장 이진 파일을 VM에 복사한 다음 SQL Server 레지스트리 hive를 확인 하는 일회성 유틸리티를 실행 하 여 수행 됩니다. SQL Server 하이브가 검색 되 면 가상 머신이 경량 모드의 [SQL VM 리소스 공급자](sql-vm-resource-provider-register.md) 에 등록 됩니다. 레지스트리에 SQL Server 하이브가 없으면 이진 파일이 제거 됩니다.

구독에 대해 자동 등록을 사용 하도록 설정 하면 SQL Server 설치 된 모든 현재 및 미래의 Vm이 경량 모드의 SQL VM 리소스 공급자에 등록 됩니다. 전체 기능 집합을 활용 하려면 여전히 [완전 한 관리 효율성 모드로 수동으로 업그레이드](sql-vm-resource-provider-register.md#upgrade-to-full) 해야 합니다. 

> [!IMPORTANT]
> SQL IaaS 에이전트 확장은 Azure Virtual Machines 내에서 SQL Server를 사용 하는 경우 고객에 게 선택적 혜택을 제공 하기 위한 express 용도의 데이터를 수집 합니다. Microsoft는 고객의 사전 동의가 없는 라이선스 감사에는이 데이터를 사용 하지 않습니다. 자세한 내용은 [SQL Server 개인 정보 취급 방침](/sql/sql-server/sql-server-privacy#non-personal-data) 을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

리소스 공급자에 SQL Server VM을 등록하려면 다음이 필요합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- 공용 또는 Azure Government 클라우드에 [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) 배포 된 Azure 리소스 모델 [Windows Server 2008 R2 이상 가상 컴퓨터](../../../virtual-machines/windows/quick-create-portal.md) 입니다. Windows Server 2008은 지원 되지 않습니다. 


## <a name="enable"></a>사용

Azure Portal에서 SQL Server Vm의 자동 등록을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [**SQL 가상 컴퓨터**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스 페이지로 이동 합니다. 
1. 자동 **SQL Server VM 등록** 을 선택 하 여 **자동 등록** 페이지를 엽니다. 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="자동 SQL Server VM 등록을 선택 하 여 자동 등록 페이지를 엽니다.":::

1. 드롭다운에서 구독을 선택 합니다. 
1. 약관을 읽고 동의 하면 동의 **함** 을 선택 합니다. 
1. **등록** 을 선택 하 여 기능을 사용 하도록 설정 하 고 SQL vm 리소스 공급자를 사용 하 여 현재 및 미래의 모든 SQL Server vm을 자동으로 등록 합니다. 이는 Vm에서 SQL Server 서비스를 다시 시작 하지 않습니다. 

## <a name="disable"></a>사용 중지

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps) 를 사용 하 여 자동 등록 기능을 사용 하지 않도록 설정 합니다. 자동 등록 기능을 사용 하지 않도록 설정 하면 구독에 추가 된 SQL Server Vm을 SQL VM 리소스 공급자에 수동으로 등록 해야 합니다. 이미 등록 된 기존 SQL Server Vm의 등록은 취소 되지 않습니다.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 자동 등록을 해제 하려면 다음 명령을 실행 합니다. 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell를 사용 하 여 자동 등록을 해제 하려면 다음 명령을 실행 합니다. 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>여러 구독에 대해 사용

PowerShell을 사용 하 여 여러 Azure 구독에 대 한 자동 등록 기능을 사용 하도록 설정할 수 있습니다. 

이렇게 하려면 다음 단계를 따르십시오.

1. [이 스크립트](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) 를 `.ps1` 와 같은 파일에 저장 `EnableBySubscription.ps1` 합니다. 
1. 관리 명령 프롬프트 또는 PowerShell 창을 사용 하 여 스크립트를 저장 한 위치로 이동 합니다. 
1. Azure ()에 연결 `az login` 합니다.
1. 스크립트를 실행 하 여 구독 Id를 매개 변수로 전달 합니다.   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   다음은 그 예입니다. 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

실패 한 등록 오류는 `RegistrationErrors.csv` 스크립트를 저장 하 고 실행 한 디렉터리와 동일한 디렉터리에 저장 됩니다 `.ps1` . 

## <a name="next-steps"></a>다음 단계

SQL VM 리소스 공급자가 제공 하는 전체 기능 집합을 활용 하려면 관리 효율성 모드를 [full](sql-vm-resource-provider-register.md#upgrade-to-full) 로 업그레이드 합니다. 
