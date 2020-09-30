---
title: Azure 지역 이동-Azure Portal-Azure Database for MariaDB
description: 읽기 복제본과 Azure Portal를 사용 하 여 한 Azure 지역에서 다른 지역으로 Azure Database for MariaDB 서버를 이동 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: b2e4bc71a0883c6fef6f0115080a79a74ced92b0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542430"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MariaDB 서버를 다른 지역으로 이동

기존 Azure Database for MariaDB 서버를 한 지역에서 다른 지역으로 이동 하는 다양 한 시나리오가 있습니다. 예를 들어 재해 복구 계획의 일부로 프로덕션 서버를 다른 지역으로 이동 하는 것이 좋습니다.

Azure Database for MariaDB [지역 간 읽기 복제본](concepts-read-replicas.md#cross-region-replication) 을 사용 하 여 다른 지역으로 이동 하는 것을 완료할 수 있습니다. 이렇게 하려면 먼저 대상 지역에서 읽기 복제본을 만듭니다. 그런 다음 읽기 복제본 서버에 대 한 복제를 중지 하 여 읽기 및 쓰기 트래픽을 모두 수락 하는 독립 실행형 서버로 만듭니다. 

> [!NOTE]
> 이 문서에서는 서버를 다른 지역으로 이동 하는 방법을 집중적으로 설명 합니다. 서버를 다른 리소스 그룹 또는 구독으로 이동 하려면 [이동](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) 문서를 참조 하세요. 

## <a name="prerequisites"></a>필수 조건

- 복제본 읽기 기능은 범용 또는 메모리 액세스에 최적화 된 가격 책정 계층의 Azure Database for MariaDB 서버에 대해서만 사용할 수 있습니다. 원본 서버가 이러한 가격 책정 계층 중 하나에 있는지 확인 합니다.

- Azure Database for MariaDB 원본 서버가 이동 하려는 Azure 지역에 있는지 확인 합니다.

## <a name="prepare-to-move"></a>이동 준비

Azure Portal를 사용 하 여 대상 지역에 지역 간 읽기 복제 서버를 만들려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 원본 서버로 사용 하려는 기존 Azure Database for MariaDB 서버를 선택 합니다. 이 작업은 **개요** 페이지를 엽니다.
1. 메뉴의 **설정** 아래에서 **복제**를 선택합니다.
1. **복제본 추가**를 선택합니다.
1. 복제본 서버의 이름을 입력합니다.
1. 복제본 서버의 위치를 선택합니다. 기본 위치는 원본 서버의와 동일 합니다. 복제본을 배포할 대상 위치를 선택 했는지 확인 합니다.
1. **확인**을 선택하여 복제본 만들기를 확인합니다. 복제본을 만드는 동안 원본 서버에서 복제본으로 데이터가 복사 됩니다. 생성 시간은 원본 서버의 크기에 비례하여 몇 분 이상 지속 될 수 있습니다.

>[!NOTE]
> 복제본을 만들 때 원본 서버의 VNet 서비스 끝점은 상속 되지 않습니다. 이러한 규칙은 복제본에 대해 별도로 설정해야 합니다.

## <a name="move"></a>이동

> [!IMPORTANT]
> 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.
> 읽기 복제본에서 복제를 중지하기 전에 복제본에 필요한 모든 데이터가 있는지 확인하십시오.

복제 서버로 복제를 중지 하면 해당 서버가 독립 실행형 서버가 됩니다. Azure Portal에서 복제본에 대 한 복제를 중지 하려면 다음 단계를 사용 합니다.

1. 복제본을 만든 후 Azure Database for MariaDB 원본 서버를 찾아 선택 합니다. 
1. 메뉴의 **설정** 아래에서 **복제**를 선택합니다.
1. 복제 서버를 선택 합니다.
1. **복제 중지**를 선택합니다.
1. **확인**을 클릭하여 복제 중지를 확인합니다.

## <a name="clean-up-source-server"></a>원본 서버 정리

원본 Azure Database for MariaDB 서버를 삭제 하는 것이 좋습니다. 이렇게 하려면 다음 단계를 수행합니다.

1. 복제본을 만든 후 Azure Database for MariaDB 원본 서버를 찾아 선택 합니다.
1. **개요** 창에서 **삭제**를 선택 합니다.
1. 삭제할 원본을 확인할 원본 서버의 이름을 입력 합니다.
1. **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal를 사용 하 여 Azure Database for MariaDB 서버를 한 지역에서 다른 지역으로 이동한 다음 불필요 한 소스 리소스를 정리 했습니다. 

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기
- [Azure Portal에서 읽기 복제본 관리](howto-read-replicas-portal.md) 에 대해 자세히 알아보세요.
- [비즈니스 연속성](concepts-business-continuity.md) 옵션에 대 한 자세한 정보
