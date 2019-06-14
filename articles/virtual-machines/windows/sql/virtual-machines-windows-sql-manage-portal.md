---
title: Azure portal을 사용 하 여 Azure에서 SQL Server Vm 관리 | Microsoft Docs
description: Azure에서 호스트 되는 SQL Server VM에 대 한 Azure portal에서 SQL 가상 머신 리소스에 액세스 하는 방법에 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e2a807bbd6baeb2f14a6d36f5d98a28d48725449
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082717"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Azure portal을 사용 하 여 Azure에서 SQL Server Vm 관리

새 액세스 지점이 사용 하 여 Azure에서 SQL Server VM을 관리 하는 [Azure portal](https://portal.azure.com)합니다. 

합니다 **SQL 가상 머신** 리소스는 이제 SQL Server 전용 설정을 수정 하 고 모든 SQL Server Vm을 동시에 볼 수 있는 독립적인 관리 서비스: 

![SQL 가상 컴퓨터 리소스](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>설명

- 합니다 **SQL 가상 머신** 리소스는 보기 및 SQL Server Vm을 관리 하는 것이 좋습니다. 하지만, 현재는 **SQL 가상 머신** 리소스 관리를 지원 하지 않습니다 [남았는지 지원 종료](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server Vm입니다. EOS SQL Server Vm에 대 한 설정을 관리 하려면 사용 되지 않는 사용 [SQL Server 구성 탭](#access-sql-server-configuration-tab) 대신 합니다. 
- 합니다 **SQL 가상 머신** 리소스를만 사용할 수 있는 SQL Server vm [SQL VM 리소스 공급자를 사용 하 여 등록](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider)합니다. 


## <a name="access-sql-virtual-machine-resource"></a>SQL 가상 머신 리소스 액세스
SQL 가상 머신 리소스에 액세스 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 
1. **모든 서비스**를 선택합니다. 
1. 형식 `SQL virtual machines` 검색 상자에 있습니다.
1. (선택 사항): 옆에 별표를 선택 **SQL 가상 머신** 즐겨찾기 메뉴에이 옵션을 추가 합니다. 
1. 선택 **SQL 가상 머신**합니다. 

   ![모든 서비스에서 SQL VM 가상 컴퓨터를 찾으려면](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 구독 내에서 사용할 수 있는 모든 SQL Server Vm 나열 됩니다. 시작 하려면 관리 하려는 것을 선택 합니다 **SQL 가상 머신** 리소스입니다. SQL Server VM을 즉시 확인할 수 없으면 검색 상자를 사용 합니다. 

![모든 사용 가능한 SQL Vm](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

SQL Server VM을 선택 하면 열립니다는 **SQL 가상 머신** 리소스: 


![SQL 가상 컴퓨터 리소스](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > 합니다 **SQL 가상 머신** 리소스 전용된 SQL Server 설정입니다. VM의 이름을 선택 합니다 **가상 머신** 특정 VM에는 SQL Server에만 국한 되지 않고는 설정으로 이동 하는 필드입니다. 

## <a name="access-sql-server-configuration-tab"></a>액세스 SQL Server 구성 탭
SQL Server 구성 탭에 더 이상 사용 되지 않습니다. 이때 관리에 대 한 유일한 방법 이기 [남았는지 지원 종료](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server Vm 및 되지 않은 SQL Server Vm [SQL VM 리소스 공급자를 사용 하 여 등록](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider)합니다.

사용 되지 않는 SQL server 구성 탭에 액세스 하려면로 이동 해야 합니다 **가상 머신** 리소스입니다. 이렇게 하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 
1. **모든 서비스**를 선택합니다. 
1. 형식 `virtual machines` 검색 상자에 있습니다.
1. (선택 사항): 옆에 별표를 선택 **가상 머신** 즐겨찾기 메뉴에이 옵션을 추가 합니다. 
1. **가상 머신**을 선택합니다. 

   ![가상 머신을 검색](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 구독에서 모든 가상 컴퓨터 나열 됩니다. 시작 하려면 관리 하려는 것을 선택 합니다 **가상 머신** 리소스입니다. SQL Server VM을 즉시 확인할 수 없으면 검색 상자를 사용 합니다. 
1. 선택 **SQL Server 구성** 에 **설정** SQL Server를 관리 하는 창입니다. 

![SQL Server 구성](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


