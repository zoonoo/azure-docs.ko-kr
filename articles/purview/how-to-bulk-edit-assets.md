---
title: 선택한 자산 목록에 여러 용어집 용어를 태그를 표시 하는 방법
description: Azure 부서의 범위에서 자산을 대량 편집 하는 방법에 대해 알아봅니다.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 493d3ceb596bb29da0e60ab08518375412417a0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049488"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>용어를 태그 용어집으로 대량 편집 하는 방법

이 문서에서는 여러 용어를 단일 작업으로 선택한 자산 목록에 태그를 표시 하는 방법을 설명 합니다.

### <a name="add-assets-to-view-selected-list-using-search"></a>검색을 사용 하 여 선택한 목록 보기에 자산 추가

1. 대량 편집을 위해 목록에 추가 하려는 데이터 자산을 검색 합니다.

2. 검색 결과 페이지에서 대량 편집 **보기 선택** 목록에 추가할 자산을 마우스로 가리켜 확인란을 표시 합니다.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="확인란의 스크린샷":::

3. 확인란을 선택 하 여 선택한 대량 편집 **보기** 목록에 추가 합니다. 추가 되 면 페이지 맨 아래에 선택한 항목 아이콘이 표시 됩니다.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="목록의 스크린샷.":::

4. 위의 단계를 반복 하 여 모든 데이터 자산을 목록에 추가 합니다.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>자산 세부 정보 페이지에서 선택한 목록 보기에 자산 추가

1. 자산 정보 페이지에서 오른쪽 위 모서리에 있는 확인란을 선택 하 여 선택한 대량 편집 **보기** 목록에 자산을 추가 합니다.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="자산의 스크린샷.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>선택 된 보기 목록에서 자산을 대량 편집 하 여 용어를 추가, 대체 또는 제거 합니다.

1. 대량 편집 해야 하는 모든 데이터 자산의 id를 확인 한 후 검색 결과 페이지 또는 자산 세부 정보 페이지에서 **선택한 목록 보기** 를 선택 합니다.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="뷰의 스크린샷":::

2. 조건을 제거 하려면 목록을 검토 하 고 **제거** 를 선택 합니다.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="제거의 스크린샷":::

3. 선택한 모든 자산에 대 한 용어를 추가, 제거 또는 바꾸기 위해 대량 편집을 선택 합니다.

4. 용어집 용어를 추가 하려면 작업을 **추가** 로 선택 합니다. 새 값에 추가 하려는 용어집 용어를 모두 선택 합니다. 완료 되 면 적용을 선택 합니다.
    - 작업 추가는 이미 데이터 자산에 태그가 지정 된 용어 목록에 새 값을 추가 합니다.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="추가의 스크린샷":::

5. 용어집 용어를 바꾸려면 선택 작업을 **로 바꿉니다.** 새 값에서 바꿀 용어집 용어를 모두 선택 합니다. 완료 되 면 적용을 선택 합니다.
    - 바꾸기 작업은 선택한 데이터 자산에 대 한 모든 용어집 용어를 새 값에서 선택한 용어로 바꿉니다.
   
6. 용어집 용어를 제거 하려면 **제거** 로 작업을 선택 합니다. 완료 되 면 적용을 선택 합니다.
    - 제거 작업은 선택한 데이터 자산에 대 한 모든 용어를 제거 합니다.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="제거 용어의 스크린샷":::

7. 분류, 소유자 및 전문가를 위해 위의 단계를 반복 합니다.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="분류 및 연락처의 스크린샷":::

8. 완료 되 면 **닫기** 또는 **모두 제거를 선택 하 고 닫기** 를 선택 하 여 대량 편집 블레이드를 닫습니다. 닫기는 선택한 자산을 제거 하지 않지만 모두 제거 및 닫기는 선택한 자산을 모두 제거 합니다.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="닫기의 스크린샷":::

   > [!Important]
   > 대량 편집에 권장 되는 자산 수는 25 개입니다. 25 개를 초과 하 여 선택 하면 성능 문제가 발생할 수 있습니다.
   > 선택 된 **보기** 상자는 선택한 자산이 하나 이상 있는 경우에만 표시 됩니다.


자세한 내용은 [자습서: 용어 만들기 및 가져오기](how-to-create-import-export-glossary.md) 를 참조 하세요.
