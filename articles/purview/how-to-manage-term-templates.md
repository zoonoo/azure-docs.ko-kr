---
title: 비즈니스 용어집에 대 한 용어 템플릿을 관리 하는 방법
description: Azure 부서의 범위 data catalog에서 비즈니스 용어집에 대 한 용어 템플릿을 관리 하는 방법에 대해 알아봅니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555335"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>비즈니스 용어집에 대 한 용어 템플릿을 관리 하는 방법

Azure 부서의 범위를 사용 하면 데이터를 보강 하는 데 중요 한 용어 설명을 만들 수 있습니다. 부서의 범위 Data Catalog 용어집에 추가 된 각 새 용어는 용어에 대 한 필드를 결정 하는 용어 템플릿을 기반으로 합니다. 이 문서에서는 용어집 용어에 연결할 수 있는 용어 템플릿 및 사용자 지정 특성을 만드는 방법을 설명 합니다.

## <a name="manage-term-templates-and-custom-attributes"></a>용어 템플릿 및 사용자 지정 특성 관리

용어 템플릿을 사용 하 여 사용자 지정 특성을 만들고 그룹화 하 고, 용어를 만드는 동안 템플릿을 적용할 수 있습니다. 용어를 만든 후에는 해당 용어와 연결 된 템플릿을 변경할 수 없습니다.

1. **용어집 용어** 페이지에서 **용어 템플릿 관리** 를 선택 합니다.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="용어 템플릿 관리 단추 > 용어 설명의 스크린샷":::

2. 페이지에는 시스템 및 사용자 지정 특성이 모두 표시 됩니다. **사용자 지정** 탭을 선택 하 여 용어 템플릿을 만들거나 편집 합니다.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="용어 템플릿 관리 페이지 > 용어 설명의 스크린샷":::

3. **+ 새 용어 템플릿** 을 선택 하 고 템플릿 이름 및 설명을 입력 합니다.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="용어 템플릿 > 새 용어 템플릿을 관리 하 > 용어 설명의 스크린샷":::

4. **+ 새 특성** 을 선택 하 여 용어 템플릿에 대 한 새 사용자 지정 특성을 만듭니다. 특성 이름, 설명을 입력 합니다. 사용자 지정 특성 이름은 용어 템플릿 내에서 고유 해야 하지만 동일한 이름을 템플릿 간에 재사용할 수 있습니다.

   옵션 **텍스트**, **단일 선택**, **다중 선택** 또는  **날짜** 목록에서 필드 유형을 선택 합니다. 텍스트 필드 형식에 대 한 기본값 문자열을 제공할 수도 있습니다.  특성은 **필수** 로 표시 될 수도 있습니다.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="새 특성 페이지 > 용어 설명의 스크린샷":::

5. 모든 사용자 지정 특성을 만든 후 **미리 보기** 를 선택 하 여 사용자 지정 특성의 시퀀스를 정렬 합니다. 사용자 지정 특성을 원하는 시퀀스로 끌어서 놓을 수 있습니다.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="용어 템플릿 미리 보기 > 용어집 약관의 스크린샷":::

6. 사용자 지정 특성이 모두 정의 되 면 **만들기** 를 선택 하 여 사용자 지정 특성으로 용어 템플릿을 만듭니다.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="새 용어 템플릿-만들기 단추 > 용어집 용어 스크린샷":::

7. 기존 사용자 지정 특성은 **만료 된 것으로 표시** 를 선택 하 여 만료 된 것으로 표시할 수 있습니다. 만료 되 면 특성을 다시 활성화할 수 없습니다. 만료 된 특성은 기존 용어에 대해 회색으로 표시 됩니다. 이 용어 템플릿으로 만든 이후 새 용어는 만료 된 것으로 표시 된 특성을 더 이상 표시 하지 않습니다.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="특성 > 편집 하 여 만료 된 것으로 표시 하는 용어를 편집 합니다.":::

## <a name="next-steps"></a>다음 단계

자세한 내용은 [자습서: 용어 만들기 및 가져오기](tutorial-import-create-glossary-terms.md) 를 참조 하세요.
