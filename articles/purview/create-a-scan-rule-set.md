---
title: 검색 규칙 집합 만들기
description: Azure 부서의 범위에서 검색 규칙 집합을 만들어 조직의 데이터 원본을 빠르게 검색 합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554746"
---
# <a name="create-a-scan-rule-set"></a>검색 규칙 집합 만들기

Azure 부서의 범위 카탈로그에서 검색 규칙 집합을 만들어 조직의 데이터 원본을 빠르게 검색할 수 있습니다.

검색 규칙 집합은 검색 규칙 집합을 함께 그룹화 하 여 검색에 쉽게 연결할 수 있도록 하는 컨테이너입니다. 예를 들어 각 데이터 원본 유형에 대 한 기본 검색 규칙 집합을 만든 다음, 회사 내 모든 검색에 대해 기본적으로 이러한 검색 규칙 집합을 사용할 수 있습니다. 또한 적절 한 권한이 있는 사용자가 비즈니스 요구에 따라 다양 한 구성을 사용 하 여 다른 스캔 규칙 집합을 만들 수 있습니다.

## <a name="steps-to-create-a-scan-rule-set"></a>검사 규칙 집합을 만드는 단계

검색 규칙 집합을 만들려면 다음을 수행 합니다.

1. Azure 부서의 범위 카탈로그에서 **관리 센터** 를 선택 합니다.

1. 왼쪽 창에서 **검색 규칙 집합** 을 선택한 다음 **새로 만들기** 를 선택 합니다.

1. **새 스캔 규칙 집합** 페이지에서 카탈로그 스캐너가 **원본 유형** 드롭다운 목록에서 지 원하는 데이터 원본을 선택 합니다. 검색 하려는 각 데이터 원본 유형에 대 한 검사 규칙 집합을 만들 수 있습니다.

1. 검색 규칙에 **이름을** 지정 합니다. 최대 길이는 63 자 이며 공백은 허용 되지 않습니다. 필요에 따라 **설명을** 입력 합니다. 최대 길이는 256자입니다.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="검색 규칙 집합 페이지를 보여 주는 스크린샷" border="true":::

1. **계속** 을 선택합니다.

   **파일 형식 선택** 페이지가 나타납니다. 이 페이지의 파일 형식 옵션은 이전 페이지에서 선택한 데이터 원본 유형에 따라 달라 집니다. 모든 파일 형식은 기본적으로 사용 하도록 설정 됩니다.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="파일 유형 선택 페이지를 보여 주는 스크린샷":::

   이 페이지의 **문서 파일 형식** 선택을 사용 하 여 다음 office 파일 형식을 포함 하거나 제외할 수 있습니다. doc, .docm, .docx, .dot, odp, .xlt, .xlsm, .pdf, .pot, .pps, ppsx, .ppt,. pptm, .pptx, xlc, .xls, .xlsb, .xlsm, .xlsx, .xlt 및 .xlt입니다.

1. 해당 확인란을 선택 하거나 선택 취소 하 여 파일 형식 타일을 사용 하거나 사용 하지 않도록 설정 합니다. Data Lake 형식 데이터 원본 (예: Azure Data Lake Storage Gen2 또는 Azure Blob)을 선택 하는 경우 스키마를 추출 하 고 분류 하려는 파일 형식을 사용 하도록 설정 합니다.

1. 특정 데이터 원본 유형에 대해 [사용자 지정 파일 유형을 만들](#create-a-custom-file-type)수도 있습니다.

1. **계속** 을 선택합니다.

   **분류 규칙 선택** 페이지가 나타납니다. 이 페이지에서는 선택한 **시스템 규칙과** **사용자 지정 규칙** 및 선택한 분류 규칙의 총 수를 표시 합니다. 기본적으로 모든 **시스템 규칙** 확인란이 선택 되어 있습니다.

1. 포함 하거나 제외 하려는 규칙에 대해 범주별로 **시스템 규칙** 분류 규칙 확인란을 선택 하거나 선택 취소할 수 있습니다.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="분류 규칙 선택 페이지를 보여 주는 스크린샷":::

1. 범주 노드를 확장 하 고 개별 확인란을 선택 하거나 선택 취소할 수 있습니다. 예를 들어, **아르헨티나. DNI 번호** 에 대 한 규칙의 가양성이 높으면 특정 확인란을 선택 취소할 수 있습니다.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="시스템 규칙을 선택 하는 방법을 보여 주는 스크린샷":::

1. **만들기** 를 선택 하 여 검색 규칙 집합 만들기를 완료 합니다.

### <a name="create-a-custom-file-type"></a>사용자 지정 파일 형식 만들기

Azure 부서의 범위는 사용자 지정 확장 추가 및 스캔 규칙 집합에 사용자 지정 열 구분 기호 정의를 지원 합니다.

사용자 지정 파일 형식을 만들려면 다음을 수행 합니다.

1. [검색 규칙 집합을 만들거나](#steps-to-create-a-scan-rule-set) 기존 스캔 규칙 집합을 편집 하는 단계에서 1 ~ 5 단계를 수행 합니다.

1. **파일 유형 선택** 페이지에서 **새 파일 유형** 을 선택 하 여 새 사용자 지정 파일 유형을 만듭니다.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="파일 유형 선택 페이지에서 새 파일 유형을 선택 하는 방법을 보여 주는 스크린샷":::

1. **파일 확장명** 및 **설명**(선택 사항)을 입력 합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="새 사용자 지정 파일 형식 페이지를 보여 주는 스크린샷" border="true":::

1. **내의 파일 내용** 에 대 한 다음 선택 항목 중 하나를 사용 하 여 파일 내에서 파일 내용 유형을 지정 합니다.

   - **사용자 지정 구분 기호** 를 선택 하 고 고유한 **사용자 지정 구분 기호** (단일 문자만)를 입력 합니다.

   - 시스템 **파일 유형을 선택 하 고** **시스템 파일 유형 드롭다운 목록** 에서 시스템 파일 유형 (예: XML)을 선택 합니다.

1. **만들기** 를 선택 하 여 사용자 지정 파일을 저장 합니다.

   시스템에서 **파일 형식 선택** 페이지로 돌아갑니다. 새 사용자 지정 파일 형식을 새 타일로 삽입 합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="파일 유형 선택 페이지에서 새 사용자 지정 파일 유형 타일을 보여 주는 스크린샷":::

1. 변경 하거나 삭제 하려는 경우 새 파일 형식 타일에서 **편집** 을 선택 합니다.

1. **계속** 을 선택 하 여 검사 규칙 집합 구성을 마칩니다.

## <a name="system-scan-rule-sets"></a>시스템 검색 규칙 집합

시스템 검색 규칙 집합은 각 Azure 부서의 범위 카탈로그에 대해 자동으로 생성 되는 Microsoft 정의 검사 규칙 집합입니다. 각 시스템 검색 규칙 집합은 특정 데이터 원본 유형과 연결 됩니다. 검색을 만들 때 시스템 검색 규칙 집합과 연결할 수 있습니다. Microsoft에서 이러한 시스템 규칙 집합을 업데이트할 때마다 카탈로그에서 업데이트를 수행 하 고 관련 된 모든 검색에 업데이트를 적용할 수 있습니다.

1. 시스템 검색 규칙 집합의 목록을 보려면 **관리 센터** 에서 **스캔 규칙 집합** 을 선택 하 고 **시스템** 탭을 선택 합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="시스템 검색 규칙 집합의 목록을 보여 주는 스크린샷" lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. 각 시스템 검색 규칙 집합에는 **이름**, **원본 유형** 및 **버전이** 있습니다. **버전** 열에서 검색 규칙 집합의 버전 번호를 선택 하면 현재 버전과 연결 된 규칙 및 이전 버전 (있는 경우)이 표시 됩니다.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="시스템 검색 규칙 집합 페이지를 보여 주는 스크린샷" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. 시스템 검색 규칙 집합에 대해 업데이트를 사용할 수 있는 경우 **버전** 열에서 **업데이트** 를 선택할 수 있습니다. 시스템 검색 규칙 페이지의 **업데이트할 새 버전 선택** 드롭다운 목록에서 버전을 선택 합니다. 이 페이지에서는 새 버전 및 현재 버전과 연결 된 시스템 분류 규칙의 목록을 제공 합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="시스템 검색 규칙 집합의 버전을 변경 하는 방법을 보여 주는 스크린샷" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>검색을 시스템 검색 규칙 집합과 연결

검색을 [만들](tutorial-scan-data.md#scan-data-into-the-catalog)때 다음과 같이 시스템 검색 규칙 집합에 연결 하도록 선택할 수 있습니다.

1. **검색 규칙 집합 선택** 페이지에서 시스템 검색 규칙 집합을 선택 합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="검색에 대 한 시스템 검색 규칙 집합을 선택 하는 방법을 보여 주는 스크린샷" lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. **계속** 을 선택한 다음 **저장 및 실행** 을 선택 합니다.
