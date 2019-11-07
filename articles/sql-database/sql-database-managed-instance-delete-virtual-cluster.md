---
title: Azure SQL Database 관리 되는 인스턴스를 삭제 한 후 서브넷 삭제
description: Azure SQL Database 관리 되는 인스턴스를 삭제 한 후 Azure 가상 네트워크를 삭제 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 84298e49de2de2ac43c33ec38c22846fd8d1e968
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688129"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 되는 인스턴스를 삭제 한 후 서브넷 삭제

이 문서에서는 마지막으로 Azure SQL Database 관리 되는 인스턴스를 삭제 한 후 수동으로 서브넷을 삭제 하는 방법에 대 한 지침을 제공 합니다.

관리 되는 인스턴스는 [가상 클러스터](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)에 배포 됩니다. 각 가상 클러스터는 서브넷과 연결 됩니다. 가상 클러스터는 마지막 인스턴스 삭제 후 12 시간 동안 설계 된 후에도 동일한 서브넷에서 관리 되는 인스턴스를 보다 신속 하 게 만들 수 있습니다. 빈 가상 클러스터를 유지 하는 데에는 요금이 부과 되지 않습니다. 이 기간 동안에는 가상 클러스터와 연결된 서브넷을 삭제할 수 없습니다.

12 시간을 기다리지 않고 가상 클러스터 및 서브넷을 더 일찍 삭제 하려면 수동으로이 작업을 수행할 수 있습니다. Azure Portal 또는 가상 클러스터 API를 사용 하 여 가상 클러스터를 수동으로 삭제 합니다.

> [!IMPORTANT]
> - 삭제를 성공적으로 수행 하려면 가상 클러스터에 관리 되는 인스턴스가 없어야 합니다. 
> - 가상 클러스터를 삭제 하는 작업은 약 1.5 시간 동안 지속 되는 장기 실행 작업입니다 (최신 가상 클러스터 삭제 시간에 대 한 [관리 되는 인스턴스 관리 작업](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 참조). 프로세스가 완료 되었습니다.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Azure Portal에서 가상 클러스터를 삭제 합니다.

Azure Portal를 사용 하 여 가상 클러스터를 삭제 하려면 가상 클러스터 리소스를 검색 합니다.

![검색 상자가 강조 표시 된 Azure Portal의 스크린샷](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

삭제 하려는 가상 클러스터를 찾은 후이 리소스를 선택 하 고 **삭제**를 선택 합니다. 가상 클러스터 삭제를 확인 하는 메시지가 표시 됩니다.

![삭제 옵션이 강조 표시 된 Azure Portal 가상 클러스터 대시보드의 스크린샷](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure Portal 알림은 가상 클러스터 삭제 요청이 성공적으로 전송 되었음을 확인 하는 메시지를 표시 합니다. 삭제 작업 자체는 포털에서 가상 클러스터가 계속 표시 되는 동안 약 1.5 시간 동안 지속 됩니다. 프로세스가 완료 되 면 가상 클러스터가 더 이상 표시 되지 않고 연결 된 서브넷이 다시 사용할 수 있도록 해제 됩니다.

> [!TIP]
> 가상 클러스터에 표시 된 관리 되는 인스턴스가 없고 가상 클러스터를 삭제할 수 없는 경우 진행 중인 인스턴스 배포가 진행 되 고 있지 않은지 확인 합니다. 아직 진행 중인 시작 및 취소 된 배포도 포함 됩니다. 이러한 작업은 해당 작업에서 가상 클러스터를 계속 사용 하 여 삭제 되지 않기 때문입니다. 인스턴스가 배포 된 리소스 그룹의 배포 검토 탭은 진행 중인 모든 배포를 표시 합니다. 이 경우 배포가 완료 될 때까지 기다리거나 관리 되는 인스턴스를 삭제 한 다음 가상 클러스터를 삭제 합니다.

## <a name="delete-virtual-cluster-by-using-the-api"></a>API를 사용 하 여 가상 클러스터 삭제

API를 통해 가상 클러스터를 삭제 하려면 [가상 클러스터 삭제 방법](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)에 지정 된 URI 매개 변수를 사용 합니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- [Managed Instance의 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)에 대해 알아봅니다.
- [Managed Instance의 기존 가상 네트워크를 수정](sql-database-managed-instance-configure-vnet-subnet.md)하는 방법을 알아봅니다.
- 가상 네트워크를 만들고, Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
