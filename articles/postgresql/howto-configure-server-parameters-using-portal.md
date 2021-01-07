---
title: 서버 매개 변수 구성-Azure Portal Azure Database for PostgreSQL 단일 서버
description: 이 문서에서는 Azure Portal를 통해 Azure Database for PostgreSQL에서 Postgres 매개 변수를 구성 하는 방법을 설명 합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: e1b40e3116d56e87a2f397350ef2ba5510e04c0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707696"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Azure Portal를 통해 Azure Database for PostgreSQL 단일 서버에서 서버 매개 변수 구성 
Azure Portal을 통해 Azure Database for PostgreSQL에 대한 구성 매개 변수를 나열하고, 표시하며, 업데이트할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for PostgreSQL 서버](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>매개 변수 보기 및 편집
1. [Azure Portal](https://portal.azure.com)을 엽니다.

2. Azure Database for PostgreSQL 서버를 선택합니다.

3. **설정** 섹션 아래에서 **서버 매개 변수**를 선택합니다. 페이지에서는 매개 변수, 해당 값 및 설명 목록을 표시합니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="매개 변수의 개요 페이지":::

4. **드롭다운** 단추를 선택하여 client_min_messages와 같이 열거 형식 매개 변수에 사용할 수 있는 값을 확인합니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="매개 변수의 개요 페이지":::

5. **i**(정보) 단추를 선택하거나 위로 마우스를 가져가서 cpu_index_tuple_cost와 같은 숫자 매개 변수에 사용할 수 있는 값의 범위를 확인합니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="매개 변수의 개요 페이지":::

6. 필요한 경우 **검색 상자**를 사용하여 특정 매개 변수로 범위를 좁힙니다. 매개 변수의 이름 및 설명으로 검색할 수 있습니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="매개 변수의 개요 페이지":::

7. 조정하려는 매개 변수 값을 변경합니다. 세션에서 변경할 내용은 모두 자주색으로 강조 표시되어 있습니다. 값을 변경하면 **저장**을 선택할 수 있습니다. 또는 변경 사항을 **취소**할 수 있습니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="매개 변수의 개요 페이지":::

8. 매개 변수에 새 값을 저장한 경우 언제든지 **모두 기본값으로 다시 설정**을 선택하여 모든 항목을 기본값으로 되돌릴 수있습니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="매개 변수의 개요 페이지":::

## <a name="next-steps"></a>다음 단계
자세한 정보:
- [Azure Database for PostgreSQL에서 서버 매개 변수 개요](concepts-servers.md)
- [Azure CLI를 사용하여 매개 변수 구성](howto-configure-server-parameters-using-cli.md)
