---
title: 읽기 복제본 관리 - Azure Portal - Azure Database for PostgreSQL - 하이퍼스케일(Citus)
description: Azure Portal을 사용하여 Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 읽기 복제본을 관리하는 방법을 알아봅니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 7bf5cc43ca173aa787c1cde4d5727b48206872af
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315510"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 읽기 복제본 만들기 및 관리

> [!IMPORTANT]
> 하이퍼스케일(Citus)의 읽기 복제본은 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> [하이퍼스케일(Citus)의 미리 보기 기능](hyperscale-preview-features.md)에서 다른 새 기능의 전체 목록을 볼 수 있습니다.

이 문서에서는 Azure Portal을 사용하여 하이퍼스케일(Citus)에서 읽기 복제본을 만들고 관리하는 방법을 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-hyperscale-read-replicas.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

주 서버가 될 [하이퍼스케일(Citus) 서버 그룹](quickstart-create-hyperscale-portal.md)

## <a name="create-a-read-replica"></a>읽기 복제본 만들기

읽기 복제본을 만들려면 다음 단계를 수행합니다.

1. 주 서버로 사용할 기존 Azure Database for PostgreSQL 서버 그룹을 선택합니다. 

2. 서버 그룹 사이드바에 있는 **서버 그룹 관리** 에서 **복제** 를 선택합니다.

3. **복제본 추가** 를 선택합니다.

4. 읽기 복제본의 이름을 입력합니다. 

5. **확인** 을 선택하여 복제본 만들기를 확인합니다.

읽기 복제본을 만든 후에는 **복제** 창에서 확인할 수 있습니다.

> [!IMPORTANT]
>
> [읽기 복제본 개요의 고려 사항 섹션](concepts-hyperscale-read-replicas.md#considerations)을 검토합니다.
>
> 주 서버 그룹 설정을 새 값으로 업데이트하기 전에 복제본 설정을 같거나 더 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 마스터에 대한 변경 내용을 유지할 수 있습니다.

## <a name="delete-a-primary-server-group"></a>주 서버 그룹 삭제

주 서버 그룹을 삭제하려면 독립 실행형 하이퍼스케일(Citus) 서버 그룹을 삭제하는 것과 동일한 단계를 사용합니다. 

> [!IMPORTANT]
>
> 주 서버 그룹을 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 읽기 복제본은 이제 읽기와 쓰기를 모두 지원하는 독립 실행형 서버 그룹이 됩니다.

Azure Portal에서 서버 그룹을 삭제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 주 Azure Database for PostgreSQL 서버 그룹을 선택합니다.

2. 서버 그룹의 **개요** 페이지를 엽니다. **삭제** 를 선택합니다.
 
3. 삭제할 주 서버 그룹의 이름을 입력합니다. **삭제** 를 선택하여 주 서버 그룹의 삭제를 확인합니다.
 

## <a name="delete-a-replica"></a>복제본 삭제

주 서버 그룹을 삭제하는 방법과 유사하게 읽기 복제본을 삭제할 수 있습니다.

- Azure Portal에서 읽기 복제본에 대한 **개요** 페이지를 엽니다. **삭제** 를 선택합니다.
 
다음 단계에 따라 **복제** 창에서 읽기 복제본을 삭제할 수도 있습니다.

1. Azure Portal에서 주 하이퍼스케일(Citus) 서버 그룹을 선택합니다.

2. 서버 그룹 메뉴에 있는 **서버 그룹 관리** 에서 **복제** 를 선택합니다.

3. 삭제할 읽기 복제본을 선택합니다.
 
4. **복제본 삭제** 를 선택합니다.
 
5. 삭제할 복제본의 이름을 입력합니다. **삭제** 를 선택하여 복제본 삭제를 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 읽기 복제본](concepts-hyperscale-read-replicas.md)에 대해 자세히 알아봅니다.
