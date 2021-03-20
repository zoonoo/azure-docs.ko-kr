---
title: 자산에 분류 적용 (미리 보기)
description: 이 문서에서는 자산에 분류를 적용 하는 방법을 설명 합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554867"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Azure 부서의 범위에서 자산에 대 한 분류 적용

이 문서에서는 자산에 분류를 적용 하는 방법을 설명 합니다.

## <a name="introduction"></a>소개

분류는 시스템 또는 사용자 지정 형식일 수 있습니다. 시스템 분류는 부서의 범위에 기본적으로 제공 됩니다. 정규식 패턴을 기반으로 사용자 지정 분류를 만들 수 있습니다. 분류는 자동 또는 수동으로 자산에 적용 될 수 있습니다.

이 문서에서는 데이터에 분류를 적용 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 필요에 따라 사용자 지정 분류를 만듭니다.
- 데이터 원본에 대 한 검색을 설정 합니다.

## <a name="apply-classifications"></a>분류 적용
Azure 부서의 범위에서 파일, 테이블 또는 열 자산에 시스템 또는 사용자 지정 분류를 적용할 수 있습니다. 이 문서에서는 자산에 분류를 수동으로 적용 하는 단계를 설명 합니다.

### <a name="apply-classification-to-a-file-asset"></a>파일 자산에 분류 적용
Azure 부서의 범위는 설명서 파일을 스캔 하 고 자동으로 분류할 수 있습니다. 예를 들어 *multiple.docx* 이라는 파일이 있고 해당 콘텐츠에 국가 ID 번호가 있는 경우 Azure 부서의 범위는 파일 자산의 세부 정보 페이지에 해당 분류 **EU 국가별 id 번호** 를 추가 합니다.

일부 시나리오에서는 파일 자산에 분류를 수동으로 추가 하는 것이 좋습니다. 리소스 집합으로 그룹화 되는 파일이 여러 개 있는 경우 리소스 집합 수준에서 분류를 추가 합니다.

파티션 리소스 집합에 사용자 지정 또는 시스템 분류를 추가 하려면 다음 단계를 수행 합니다.

1. 파티션을 검색 하거나 찾아본 후 자산 정보 페이지로 이동 합니다.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="자산 세부 정보 페이지를 보여 주는 스크린샷":::

1. **개요** 탭에서 **분류** 섹션을 확인 하 여 기존 분류가 있는지 확인 합니다. **편집** 을 선택합니다.

1. **분류** 드롭다운 목록에서 관심 있는 특정 분류를 선택 합니다. 예를 들어 시스템 분류 인 **신용 카드 번호** 와 사용자 지정 분류 인 **customeraccountid** 가 있습니다.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="자산에 추가할 분류를 선택 하는 방법을 보여 주는 스크린샷":::

1. **저장** 을 선택합니다.

1. **개요** 탭에서 선택한 분류가 **분류** 섹션 아래에 표시 되는지 확인 합니다.

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="분류가 자산에 추가 되었는지 확인 하는 방법을 보여 주는 스크린샷":::

### <a name="apply-classification-to-a-table-asset"></a>테이블 자산에 분류 적용

Azure 부서의 범위는 데이터 원본을 검색할 때 자동으로 테이블 자산에 분류를 할당 하지 않습니다. 테이블 자산이 분류를 갖도록 하려면 수동으로 추가 해야 합니다.

테이블 자산에 분류를 추가 하려면 다음을 수행 합니다.

1. 관심이 있는 테이블 자산을 찾습니다. 예를 들어 **Customer** 테이블입니다.

1. 테이블에 분류가 할당 되지 않았는지 확인 합니다. **편집** 을 선택합니다.

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="테이블 자산의 분류를 보고 편집 하는 방법을 보여 주는 스크린샷":::

1. **분류** 드롭다운 목록에서 하나 이상의 분류를 선택 합니다. 이 예제에서는 **Customerinfo** 라는 사용자 지정 분류를 사용 하지만이 단계에 대 한 모든 분류를 선택할 수 있습니다.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="테이블 자산에 추가할 분류를 선택 하는 방법을 보여 주는 스크린샷":::

1. **저장** 을 선택 하 여 분류를 저장 합니다.

1. **개요** 페이지에서 Azure 부서의 범위에서 새 분류를 추가 했는지 확인 합니다.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="분류가 테이블 자산에 추가 되었는지 확인 하는 방법을 보여 주는 스크린샷":::

### <a name="add-classification-to-a-column-asset"></a>열 자산에 분류 추가

Azure 부서의 범위는 자동으로 모든 열 자산에 분류를 검색 하 고 추가 합니다. 그러나 분류를 변경 하려는 경우에는 열 수준에서 분류를 변경할 수 있습니다.

열에 분류를 추가 하려면 다음을 수행 합니다.

1. 열 자산을 찾아 선택한 다음 **개요** 탭에서 **편집** 을 선택 합니다.

1. **스키마** 탭 선택

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="열의 스키마를 편집 하는 방법을 보여 주는 스크린샷":::

1. 관심이 있는 열을 식별 하 고 **분류 추가** 를 선택 합니다. 이 예에서는 **PasswordHash** 열에 **공통 암호** 분류를 추가 합니다.

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="열에 분류를 추가 하는 방법을 보여 주는 스크린샷":::

1. **저장** 을 선택합니다.

1. **스키마** 탭을 선택 하 고 분류가 열에 추가 되었는지 확인 합니다.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="분류가 열 스키마에 추가 되었는지 확인 하는 방법을 보여 주는 스크린샷":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>기존 분류에서 다시 검사의 영향

분류는 데이터에 대 한 샘플 집합 검사에 따라 처음 적용 되며 set regex 패턴과 일치 합니다. 다시 검사할 때 새 분류가 적용 되 면 열에서 추가 분류를 가져옵니다. 기존 분류는 열에 유지 되며 수동으로 제거 해야 합니다.

## <a name="next-steps"></a>다음 단계
사용자 지정 분류를 만드는 방법에 대 한 자세한 내용은 [사용자 지정 분류 만들기](create-a-custom-classification-and-classification-rule.md)를 참조 하세요.