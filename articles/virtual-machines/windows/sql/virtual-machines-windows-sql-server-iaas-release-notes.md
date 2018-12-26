---
title: Azure VM의 SQL Server 릴리스 정보 | Microsoft Docs
description: Azure VM - SQL Server의 새로운 기능 및 향상된 기능에 대한 자세한 정보
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 44aee447c7f935cd67ca55902c53c5f6f9eb6fda
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577085"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure Virtual Machines의 SQL Server 릴리스 정보

Azure를 사용하면 SQL Server 이미지가 기본 제공된 가상 머신을 배포할 수 있습니다. 이 문서에는 Azure Virtual Machine에 배포된 최신 버전의 SQL Server에서 예상할 수 있는 새로운 기능 및 향상된 기능이 나와 있습니다. 


## <a name="november-2018"></a>2018년 11월
- **새 SQL 리소스 공급자**: VM의 효율적인 관리를 허용하는 SQL VM용 새 리소스 공급자가 있습니다. VM 등록에 대한 자세한 내용은 [새 리소스 공급자를 사용하여 레거시 SQL VM 등록](virtual-machines-windows-sql-ahb.md#register-legacy-sql-vm-with-new-resource-provider)을 참조하세요.
- **라이선싱 모델 전환**: 이제 Azure CLI 또는 Powershell을 사용하여 SQL VM에 대한 종량제 모델과 사용자 라이선스 필요 모델 간에 전환할 수 있습니다. 자세한 내용은 [SQL VM의 라이선스 모델을 변경하는 방법](virtual-machines-windows-sql-ahb.md)을 참조하세요.



## <a name="additional-resources"></a>추가 리소스

**Windows VM**:

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)
* [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines의 SQL Server에 대한 응용 프로그램 패턴 및 개발 전략](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM 프로비전](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ(Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
