---
title: 관리 되는 인스턴스를 Azure SQL Database를 삭제 한 후 서브넷을 삭제 합니다. | Microsoft Docs
description: 관리 되는 인스턴스를 Azure SQL Database를 삭제 한 후 Azure 가상 네트워크를 삭제 하는 방법에 알아봅니다.
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
ms.date: 06/26/2019
ms.openlocfilehash: 4679ecda210fa78aad4315bc6602b67dd1795ce9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427984"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>관리 되는 인스턴스를 Azure SQL Database를 삭제 한 후 서브넷을 삭제 합니다.

이 문서에서는 관리 되는 것에 있는 인스턴스를 마지막으로 Azure SQL Database를 삭제 한 후 서브넷을 수동으로 삭제 하는 방법에 지침을 제공 합니다.

SQL Database를 사용 하는 [가상 클러스터](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) 삭제 된 관리 되는 인스턴스를 포함 합니다. 가상 클러스터 동일한 서브넷에서 관리 되는 인스턴스를 빠르게 만들 수 있도록 하는 인스턴스를 삭제 한 후 12 시간 동안 유지 됩니다. 빈 가상 클러스터를 유지 하는 것에 대 한 요금은 없습니다. 이 기간 동안에는 가상 클러스터와 연결된 서브넷을 삭제할 수 없습니다.

12 시간 동안 기다린 후 가상 클러스터 및 해당 서브넷을 즉시 삭제 하는 것을 선호를 않으려면 되므로 수동으로 수행할 수 있습니다. Azure portal 또는 가상 클러스터 API 사용 하 여 수동으로 가상 클러스터를 삭제 합니다.

> [!NOTE]
> 가상 클러스터 삭제가 성공 하려면에 대 한 관리 되는 인스턴스가 있어야 합니다.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Azure portal에서 가상 클러스터를 삭제 합니다.

Azure portal을 사용 하 여 가상 클러스터를 삭제 하려면 가상 클러스터 리소스를 검색 합니다.

![강조 표시 하는 검색 상자를 사용 하 여 Azure portal의 스크린샷](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

삭제 하려는 가상 클러스터를 찾은 후이 리소스를 선택 하 고 선택 **삭제**합니다. 가상 클러스터를 삭제할 것인지 묻는 메시지가 나타납니다.

![Azure portal 가상 스크린샷 삭제 옵션이 강조 표시 된 대시보드, 클러스터](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure portal 알림 영역 가상 클러스터가 삭제 되었습니다 확인 보여 줍니다. 가상 클러스터 성공적으로 삭제는 즉시 다시 사용할 수 있도록 서브넷을 해제합니다.

> [!TIP]
> 가상 클러스터에 표시 된 관리 되는 인스턴스가 없는 가상 클러스터를 삭제할 수 없는 경우에 필요가 없습니다 지속적인 인스턴스 배포를 진행에서을 확인 합니다. 여기에 여전히 진행 중인 배포를 시작 및 취소 합니다. 배포에 배포 된 리소스 그룹 인스턴스 탭 검토 진행률에 대 한 모든 배포를 표시 합니다. 이 경우 배포 완료에 대 한 await, 관리 되는 인스턴스 및 다음 가상 클러스터 삭제 합니다.

## <a name="delete-virtual-cluster-by-using-the-api"></a>API를 사용 하 여 가상 클러스터를 삭제 합니다.

API 통해 가상 클러스터를 삭제 하려면에 지정 된 URI 매개 변수를 사용 합니다 [메서드를 삭제 하는 가상 클러스터](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)합니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- [Managed Instance의 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)에 대해 알아봅니다.
- [Managed Instance의 기존 가상 네트워크를 수정](sql-database-managed-instance-configure-vnet-subnet.md)하는 방법을 알아봅니다.
- 가상 네트워크를 만들고, Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
