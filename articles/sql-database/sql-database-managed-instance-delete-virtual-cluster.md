---
title: 관리되는 인스턴스를 삭제한 후 서브넷 삭제
description: Azure SQL Database 관리 인스턴스를 삭제한 후 Azure 가상 네트워크를 삭제하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820456"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 인스턴스를 삭제한 후 서브넷 삭제

이 문서에서는 서브넷에 있는 마지막 Azure SQL Database 관리 인스턴스를 삭제한 후 서브넷을 수동으로 삭제하는 방법에 대한 지침을 제공합니다.

관리되는 인스턴스는 [가상 클러스터에 배포됩니다.](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) 각 가상 클러스터는 서브넷과 연결됩니다. 가상 클러스터는 마지막 인스턴스 삭제 후 12시간 동안 설계하여 동일한 서브넷에서 관리되는 인스턴스를 보다 빠르게 만들 수 있도록 합니다. 빈 가상 클러스터를 유지하는 데는 요금이 부과되지 않습니다. 이 기간 동안에는 가상 클러스터와 연결된 서브넷을 삭제할 수 없습니다.

12시간을 기다리지 않고 가상 클러스터와 해당 서브넷을 더 빨리 삭제하려는 경우 수동으로 삭제할 수 있습니다. Azure 포털 또는 가상 클러스터 API를 사용하여 가상 클러스터를 수동으로 삭제합니다.

> [!IMPORTANT]
> - 가상 클러스터에는 삭제가 성공하기 위해 관리되는 인스턴스가 포함되어서는 안 됩니다. 
> - 가상 클러스터의 삭제는 약 1.5시간 동안 지속되는 장기 실행 작업입니다(최신 가상 클러스터 삭제 시간에 대한 [관리되는 인스턴스 관리 작업](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 참조) 이 프로세스가 완료될 때까지 가상 클러스터가 포털에 계속 표시됩니다.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Azure 포털에서 가상 클러스터 삭제

Azure 포털을 사용하여 가상 클러스터를 삭제하려면 가상 클러스터 리소스를 검색합니다.

![검색 상자가 강조 표시된 Azure 포털의 스크린샷](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

삭제할 가상 클러스터를 찾은 후 이 리소스를 선택하고 **삭제를**선택합니다. 가상 클러스터 삭제를 확인하라는 메시지가 표시됩니다.

![삭제 옵션이 강조 표시된 Azure 포털 가상 클러스터 대시보드의 스크린샷](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure 포털 알림에는 가상 클러스터 삭제 요청이 성공적으로 제출되었음을 확인하는 것이 표시됩니다. 삭제 작업 자체는 약 1.5시간 동안 지속되며, 이 기간 동안 가상 클러스터는 포털에 계속 표시됩니다. 프로세스가 완료되면 가상 클러스터가 더 이상 표시되지 않으며 해당 클러스터와 연결된 서브넷이 다시 사용할 수 있습니다.

> [!TIP]
> 가상 클러스터에 표시되는 관리되는 인스턴스가 없고 가상 클러스터를 삭제할 수 없는 경우 진행 중인 인스턴스 배포가 진행 중인지 확인합니다. 여기에는 아직 진행 중인 시작 및 취소된 배포가 포함됩니다. 이러한 작업은 여전히 삭제에서 잠금 가상 클러스터를 사용 하기 때문에. 인스턴스가 배포된 리소스 그룹의 배포 탭을 검토하면 진행 중인 배포가 표시됩니다. 이 경우 배포가 완료될 때까지 기다렸다가 관리되는 인스턴스를 삭제한 다음 가상 클러스터를 삭제합니다.

## <a name="delete-virtual-cluster-by-using-the-api"></a>API를 사용하여 가상 클러스터 삭제

API를 통해 가상 클러스터를 삭제하려면 가상 클러스터 [delete 메서드에](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)지정된 URI 매개 변수를 사용합니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- [Managed Instance의 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)에 대해 알아봅니다.
- [Managed Instance의 기존 가상 네트워크를 수정](sql-database-managed-instance-configure-vnet-subnet.md)하는 방법을 알아봅니다.
- 가상 네트워크를 만들고, Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DNS 문제의 경우 [사용자 지정 DNS 구성을](sql-database-managed-instance-custom-dns.md)참조하십시오.
