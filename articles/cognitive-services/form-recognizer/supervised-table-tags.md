---
title: 테이블 태그를 사용 하 여 사용자 지정 폼 모델을 학습 하는 방법-폼 인식기
titleSuffix: Azure Cognitive Services
description: 감독 된 테이블 태그 레이블을 효과적으로 사용 하는 방법에 대해 알아봅니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467861"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>테이블 태그를 사용 하 여 사용자 지정 폼 모델 학습

이 문서에서는 테이블 태그 (레이블)를 사용 하 여 사용자 지정 폼 모델을 학습 하는 방법을 배웁니다. 일부 시나리오에서는 키-값 쌍을 정렬 하는 것 보다 복잡 한 레이블 지정이 필요 합니다. 이러한 시나리오에는 복잡 한 계층 구조를 포함 하는 양식에서 정보를 추출 하거나 서비스에서 자동으로 검색 하 여 추출 하지 않는 항목이 포함 됩니다. 이러한 경우에는 테이블 태그를 사용 하 여 사용자 지정 폼 모델을 학습 시킬 수 있습니다.

## <a name="when-should-i-use-table-tags"></a>테이블 태그를 언제 사용 해야 하나요?

테이블 태그를 사용 하는 경우에 대 한 몇 가지 예는 다음과 같습니다.

- 추출 하려는 데이터가 폼에 테이블로 표시 되 고 테이블의 구조가 의미가 있습니다. 예를 들어, 테이블의 각 행은 하나의 항목을 나타내며 행의 각 열은 해당 항목의 특정 기능을 나타냅니다. 이 경우 열이 기능을 나타내는 테이블 태그를 사용 하 고 각 기능에 대 한 정보를 나타내는 행을 사용할 수 있습니다.
- 특정 양식 필드에는 표시 되지 않는 데이터를 추출 하 고, 데이터는 2 차원 표에 맞을 수 있습니다. 예를 들어 양식에는 사람 목록이 있으며, 이름, 성, 전자 메일 주소 등이 포함 됩니다. 이 정보를 추출 하 고 싶습니다. 이 경우 이름, 성 및 전자 메일 주소와 함께 테이블 태그를 열로 사용할 수 있으며 각 행은 목록에 있는 사람에 대 한 정보로 채워집니다.

> [!NOTE]
> 양식 인식기는 테이블에 태그가 지정 되었는지 여부에 관계 없이 자동으로 문서에 있는 모든 테이블을 찾아서 추출 합니다. 따라서 폼에서 테이블 태그를 사용 하 여 모든 테이블에 레이블을 추가할 필요가 없으며, 테이블 태그는 폼에 있는 테이블의 구조를 복제할 필요가 없습니다. 양식 인식기에서 자동으로 추출 된 테이블은 JSON 출력의 pageResults 섹션에 포함 됩니다.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>FOTT (양식 OCR 테스트 도구)를 사용 하 여 테이블 태그 만들기
<!-- markdownlint-disable MD004 -->
* **동적** 또는 **고정 크기** 테이블 태그를 사용할지 여부를 결정 합니다. 행 수가 문서에 따라 다를 경우에는 동적 테이블 태그를 사용 합니다. 행 수가 문서 전체에 걸쳐 있는 경우 고정 크기 테이블 태그를 사용 합니다.
* 테이블 태그가 동적 이면 각 열에 대해 열 이름과 데이터 형식을 정의 합니다.
* 테이블이 고정 크기 이면 각 태그의 열 이름, 행 이름, 데이터 형식 및 형식을 정의 합니다.
:::image type="content" source="./media/table-tag-configure.png" alt-text="테이블 태그 구성":::

## <a name="label-your-table-tag-data"></a>테이블 태그 데이터 레이블

* 프로젝트에 테이블 태그가 있는 경우 레이블 패널을 열고 키-값 필드를 레이블 지정할 때 태그를 채울 수 있습니다.
:::image type="content" source="media/table-labeling.png" alt-text="테이블 태그가 있는 레이블":::

## <a name="next-steps"></a>다음 단계

빠른 시작을 따라 사용자 지정 양식 인식기 모델을 학습 하 고 사용 합니다.

> [!div class="nextstepaction"]
> [샘플 레이블 도구를 사용 하 여 레이블로 학습](quickstarts/label-tool.md)

## <a name="see-also"></a>참조

* [Form Recognizer란?](overview.md)
>
