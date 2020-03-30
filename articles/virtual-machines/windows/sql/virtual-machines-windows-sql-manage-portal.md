---
title: Azure 포털을 사용하여 Azure에서 SQL 서버 VM 관리 | 마이크로 소프트 문서
description: Azure에서 호스팅되는 SQL Server VM에 대한 Azure 포털의 SQL 가상 시스템 리소스에 액세스하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243212"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure에서 SQL 서버 VM 관리

Azure [포털에서](https://portal.azure.com) **SQL 가상 시스템** 리소스는 독립 관리 서비스입니다. 이 앱을 사용하여 모든 SQL Server VM을 동시에 보고 SQL Server 전용 설정을 수정할 수 있습니다. 

![SQL 가상 시스템 리소스](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>설명

- **SQL 가상 시스템** 리소스를 사용하여 Azure에서 SQL Server VM을 보고 관리하는 것이 좋습니다. 그러나 현재 **SQL 가상 시스템** 리소스는 [지원 종료](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server VM의 관리를 지원하지 않습니다. 지원 종료 SQL Server VM에 대한 설정을 관리하려면 더 이상 사용되지 않고 SQL [Server 구성 탭을](#access-the-sql-server-configuration-tab) 대신 사용하십시오. 
- **SQL 가상 컴퓨터** 리소스는 SQL [VM 리소스 공급자에 등록한](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server VM에서만 사용할 수 있습니다. 


## <a name="access-the-sql-virtual-machines-resource"></a>SQL 가상 시스템 리소스에 액세스
**SQL 가상 시스템** 리소스에 액세스하려면 다음을 수행합니다.

1. Azure [포털을](https://portal.azure.com)엽니다. 
1. **모든 서비스를**선택합니다. 
1. 검색 상자에 **SQL 가상 컴퓨터를** 입력합니다.
1. (선택 사항): SQL **가상 컴퓨터** 옆에 있는 별을 선택하여 즐겨찾기 메뉴에 이 옵션을 **추가합니다.** 
1. **SQL 가상 컴퓨터를 선택합니다.** 

   ![모든 서비스에서 SQL Server 가상 컴퓨터 찾기](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 포털에는 구독 내에서 사용할 수 있는 모든 SQL Server VM이 나열됩니다. **SQL 가상 컴퓨터** 리소스를 열려면 관리할 리소스를 선택합니다. SQL Server VM이 나타나지 않는 경우 검색 상자를 사용합니다. 

   ![사용 가능한 모든 SQL 서버 VM](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   SQL Server VM을 선택하면 **SQL 가상 시스템** 리소스가 열립니다. 


   ![SQL 가상 시스템 리소스](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 가상 컴퓨터** 리소스는 전용 SQL Server 설정을 위한 것입니다. **가상 컴퓨터** 상자에서 VM 이름을 선택하여 SQL Server에만 해당되지 는 않지만 VM에 만연한 설정을 엽니다. 

## <a name="access-the-sql-server-configuration-tab"></a>SQL Server 구성 탭에 액세스
**SQL Server 구성** 탭이 더 이상 사용되지 않습니다. 현재 는 [SQL VM 리소스 공급자에 등록되지](virtual-machines-windows-sql-register-with-resource-provider.md)않은 [지원 종료](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server VM 및 SQL Server VM을 관리하는 유일한 방법입니다.

더 이상 사용되지 이상인 **SQL Server 구성** 탭에 액세스하려면 가상 **컴퓨터** 리소스로 이동합니다. 다음 단계를 사용합니다.

1. Azure [포털을](https://portal.azure.com)엽니다. 
1. **모든 서비스를**선택합니다. 
1. 검색 상자에 **가상 컴퓨터를** 입력합니다.
1. (선택 사항): 가상 **시스템** 옆에 있는 별을 선택하여 즐겨찾기 메뉴에 이 옵션을 **추가합니다.** 
1. **가상 컴퓨터를**선택합니다. 

   ![가상 컴퓨터 검색](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 포털은 구독의 모든 가상 컴퓨터를 나열합니다. **가상 시스템** 리소스를 열려고 관리할 리소스를 선택합니다. SQL Server VM이 나타나지 않는 경우 검색 상자를 사용합니다. 
1. **설정** 창에서 **SQL Server 구성을** 선택하여 SQL Server VM을 관리합니다. 

   ![SQL Server 구성](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM의 SQL 서버 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL 서버에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL 서버에 대한 가격 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM에서 SQL 서버에 대한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


