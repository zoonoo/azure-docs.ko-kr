---
title: Azure 지역 이동 - Azure Portal - Azure Database for MariaDB
description: 읽기 복제본과 Azure Portal을 사용하여 한 Azure 지역에서 다른 지역으로 Azure Database for MariaDB 서버를 이동합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: 831a1927d60c7b583afbe885c385fd85dc78e525
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062219"
---
# <a name="move-an-azure-database-for-mariadb-server-to-another-region-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MariaDB 서버를 다른 지역으로 이동합니다.

기존 Azure Database for MariaDB 서버를 한 지역에서 다른 지역으로 이동하는 다양한 시나리오가 있습니다. 예를 들어 재해 복구 플랜의 일부로 프로덕션 서버를 다른 지역으로 이동할 수 있습니다.

Azure Database for MariaDB [지역 간 읽기 복제본](concepts-read-replicas.md#cross-region-replication)을 사용하여 다른 지역으로 이동을 완료할 수 있습니다. 이렇게 하려면 먼저 대상 지역에 읽기 복제본을 만듭니다. 그런 다음 읽기 복제본 서버에 대한 복제를 중지하여 읽기 및 쓰기 트래픽을 모두 허용하는 독립 실행형 서버로 만듭니다. 

> [!NOTE]
> 이 문서에서는 서버를 다른 지역으로 이동하는 방법에 관해 집중적으로 설명합니다. 서버를 다른 리소스 그룹 또는 구독으로 이동하려면 [이동](../azure-resource-manager/management/move-resource-group-and-subscription.md) 문서를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

- 읽기 복제본 기능은 범용 또는 메모리 최적화 가격 책정 계층의 Azure Database for MariaDB 서버에서만 사용 가능합니다. 원본 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인합니다.

- Azure Database for MariaDB 원본 서버가 이동을 시작하려는 Azure 지역에 있는지 확인합니다.

## <a name="prepare-to-move"></a>이동 준비

Azure Portal을 사용하여 대상 지역에 지역 간 읽기 복제본 서버를 만들려면 다음 단계를 사용하세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 원본 서버로 사용하려는 기존 Azure Database for MariaDB 서버를 선택합니다. 이 작업은 **개요** 페이지를 엽니다.
1. 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.
1. **복제본 추가** 를 선택합니다.
1. 복제본 서버의 이름을 입력합니다.
1. 복제본 서버의 위치를 선택합니다. 기본 위치는 원본 서버의 위치와 같습니다. 복제본을 배포할 대상 위치를 선택했는지 확인합니다.
1. **확인** 을 선택하여 복제본 만들기를 확인합니다. 복제본을 만드는 중에 데이터는 원본 서버에서 복제본으로 복사됩니다. 만든 시간은 원본 서버의 크기에 비례하여 몇 분 이상 지속될 수 있습니다.

>[!NOTE]
> 복제본을 만들 때 복제본은 원본 서버의 VNet 서비스 엔드포인트를 상속하지 않습니다. 이러한 규칙은 복제본에 대해 별도로 설정해야 합니다.

## <a name="move"></a>이동

> [!IMPORTANT]
> 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.
> 읽기 복제본에서 복제를 중지하기 전에 복제본에 필요한 모든 데이터가 있는지 확인하십시오.

복제본 서버에 대한 복제를 중지하면 복제본 서버가 독립 실행형 서버가 됩니다. Azure Portal에서 복제본에 대한 복제를 중지하려면 다음 단계를 수행합니다.

1. 복제본을 만든 후 Azure Database for MariaDB 원본 서버를 찾아 선택합니다. 
1. 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.
1. 복제본 서버 선택
1. **복제 중지** 를 선택합니다.
1. **확인** 을 클릭하여 복제 중지를 확인합니다.

## <a name="clean-up-source-server"></a>원본 서버 정리

원본 Azure Database for MariaDB 서버를 삭제하는 것이 좋습니다. 이렇게 하려면 다음 단계를 수행합니다.

1. 복제본을 만든 후 Azure Database for MariaDB 원본 서버를 찾아 선택합니다.
1. **개요** 창에서 **삭제** 를 선택합니다.
1. 삭제할 원본 서버의 이름을 입력합니다.
1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal을 사용하여 Azure Database for MariaDB 서버를 한 지역에서 다른 지역으로 이동한 다음 불필요한 원본 리소스를 정리했습니다. 

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기
- [Azure Portal에서 읽기 복제본 관리](howto-read-replicas-portal.md)에 대해 자세히 알아보기
- [비즈니스 연속성](concepts-business-continuity.md) 옵션에 대해 자세히 알아보기