---
title: Azure Portal을 사용 하 여 Azure에서 Vm SQL Server 관리 Microsoft Docs
description: Azure에서 호스트 되는 SQL Server VM에 대 한 Azure Portal에서 SQL 가상 컴퓨터 리소스에 액세스 하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243212"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure에서 SQL Server Vm 관리

[Azure Portal](https://portal.azure.com)에서 **SQL 가상 컴퓨터** 리소스는 독립 된 관리 서비스입니다. 이 도구를 사용 하 여 모든 SQL Server Vm을 동시에 확인 하 고 SQL Server 전용 설정을 수정할 수 있습니다. 

![SQL 가상 컴퓨터 리소스](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>설명

- **SQL 가상 컴퓨터** 리소스를 사용 하 여 Azure에서 SQL Server vm을 보고 관리 하는 것이 좋습니다. 그러나 현재 **SQL 가상 컴퓨터** 리소스는 [지원 종료](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server vm의 관리를 지원 하지 않습니다. 지원 종료 SQL Server Vm에 대 한 설정을 관리 하려면 사용 되지 않는 [SQL Server 구성 탭](#access-the-sql-server-configuration-tab) 을 대신 사용 합니다. 
- Sql **가상 컴퓨터** 리소스는 [sql vm 리소스 공급자에 등록](virtual-machines-windows-sql-register-with-resource-provider.md)된 vm SQL Server 에서만 사용할 수 있습니다. 


## <a name="access-the-sql-virtual-machines-resource"></a>SQL 가상 컴퓨터 리소스 액세스
**SQL 가상 컴퓨터** 리소스에 액세스 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 
1. **모든 서비스**를 선택합니다. 
1. 검색 상자에 **SQL 가상 컴퓨터** 를 입력 합니다.
1. (선택 사항):이 옵션을 **즐겨찾기** 메뉴에 추가 하려면 **SQL virtual machines** 옆의 별표를 선택 합니다. 
1. **SQL 가상 컴퓨터**를 선택 합니다. 

   ![모든 서비스에서 SQL Server 가상 컴퓨터 찾기](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 포털에는 구독 내에서 사용할 수 있는 모든 SQL Server Vm이 나열 됩니다. **SQL 가상 컴퓨터** 리소스를 열려면 관리 하려는 항목을 선택 합니다. SQL Server VM 표시 되지 않는 경우 검색 상자를 사용 합니다. 

   ![사용 가능한 모든 SQL Server Vm](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   SQL Server VM 선택 하면 **SQL 가상 머신** 리소스가 열립니다. 


   ![SQL 가상 컴퓨터 리소스](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 가상 컴퓨터** 리소스는 전용 SQL Server 설정에 대 한 것입니다. **가상 컴퓨터** 상자에서 vm의 이름을 선택 하 여 vm에만 적용 되는 설정을 열 수 있지만 SQL Server는 사용할 수 없습니다. 

## <a name="access-the-sql-server-configuration-tab"></a>SQL Server 구성 탭 액세스
**SQL Server 구성** 탭은 더 이상 사용 되지 않습니다. 지금은 [지원 종료](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server VM 및 [SQL VM 리소스 공급자에 등록](virtual-machines-windows-sql-register-with-resource-provider.md)되지 않은 SQL Server vm을 관리 하는 유일한 방법입니다.

사용 되지 않는 **SQL Server 구성** 탭에 액세스 하려면 **Virtual machines** 리소스로 이동 합니다. 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 
1. **모든 서비스**를 선택합니다. 
1. 검색 상자에 **가상 컴퓨터** 를 입력 합니다.
1. (선택 사항):이 옵션을 **즐겨찾기** 메뉴에 추가 하려면 **Virtual machines** 옆의 별표를 선택 합니다. 
1. **가상 머신**을 선택합니다. 

   ![가상 컴퓨터 검색](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 포털은 구독의 모든 가상 컴퓨터를 나열 합니다. **가상 컴퓨터** 리소스를 열려면 관리 하려는 항목을 선택 합니다. SQL Server VM 표시 되지 않는 경우 검색 상자를 사용 합니다. 
1. **설정** 창에서 **SQL Server 구성** 을 선택 하 여 SQL Server VM를 관리 합니다. 

   ![SQL Server 구성](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


