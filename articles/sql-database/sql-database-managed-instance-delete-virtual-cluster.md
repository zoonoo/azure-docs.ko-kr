---
title: 관리 되는 인스턴스를 Azure SQL Database를 삭제 한 후에 VNet을 삭제할 | Microsoft Docs
description: 관리 되는 인스턴스를 Azure SQL Database를 삭제 한 후에 VNet을 삭제 하는 방법에 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 61f6c25031c4906e65c2f75a7679600741e8311a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791385"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>관리 되는 인스턴스를 Azure SQL Database를 삭제 한 후 서브넷을 삭제 합니다.

이 문서에서는 관리 되는 것에 있는 인스턴스를 마지막으로 Azure SQL Database를 삭제 한 후 서브넷을 수동으로 삭제 하는 방법에 지침을 제공 합니다.

합니다 [가상 클러스터](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) 는 포함 된 삭제 된 인스턴스 삭제에서 관리 되는 인스턴스를 12 시간 동안 유지 됩니다. 가상 클러스터 동일한 서브넷에서 관리 되는 인스턴스를 빠르게 만들 수 있도록 디자인 하 여 활성 상태로 유지 됩니다. 빈 가상 클러스터를 유지 하는 것은 무료입니다. 이 기간 동안 가상 클러스터와 연결 된 서브넷을 삭제할 수 없습니다.

빈 가상 클러스터에서 사용 하는 서브넷의 즉시 릴리스는 가상 클러스터의 수동 삭제를 통해 수행할 수 있습니다. Azure portal 또는 가상 클러스터 API 통해 가상 클러스터의 삭제를 수행할 수 있습니다.

> [!NOTE]
> 가상 클러스터 삭제가 성공 하려면에 대 한 관리 되는 인스턴스가 있어야 합니다.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Azure portal에서 가상 클러스터를 삭제 합니다.

Azure portal을 사용 하는 가상 클러스터를 삭제 하려면 기본 제공 검색을 사용 하 여 가상 클러스터 리소스를 검색 합니다.

![가상 클러스터를 검색 합니다.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

삭제 하려는 가상 클러스터를 찾은 후이 리소스를 선택 하 고 삭제 옵션을 선택 합니다. 가상 클러스터 삭제를 확인 하 라는 메시지가 표시 됩니다.

![가상 클러스터를 삭제 합니다.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

확인 가상 클러스터 삭제 된 Azure portal 알림 제공 됩니다. 가상 클러스터 성공적으로 삭제는 즉시 추가 재사용에 대 한 서브넷을 해제합니다.

## <a name="delete-virtual-cluster-using-api"></a>API를 사용 하 여 가상 클러스터를 삭제 합니다.

삭제 하려면 가상 클러스터 API 사용 하 여 지정 된 URI 매개 변수를 [메서드를 삭제 하는 가상 클러스터](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)합니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- [Managed Instance의 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)에 대해 알아봅니다.
- [Managed Instance의 기존 가상 네트워크를 수정](sql-database-managed-instance-configure-vnet-subnet.md)하는 방법을 알아봅니다.
- 가상 네트워크를 만들고, Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
