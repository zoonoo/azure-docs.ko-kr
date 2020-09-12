---
title: Azure Monitor 통합 문서 텍스트 시각화
description: 모든 Azure Monitor 통합 문서 텍스트 시각화에 대해 알아봅니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664377"
---
# <a name="text-visualizations"></a>텍스트 시각화

통합 문서를 사용 하면 작성자가 해당 통합 문서에 텍스트 블록을 포함할 수 있습니다. 텍스트는 원격 분석에 대 한 사람이 분석할 수 있으며, 사용자가 데이터를 해석 하는 데 도움이 되는 정보, 섹션 제목 등입니다.

[![Apdex 텍스트 표 스크린샷](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

텍스트는 전체 형식 지정 컨트롤을 제공 하는 Markdown 컨트롤을 통해 추가 됩니다. 여기에는 다양 한 제목 및 글꼴 스타일, 하이퍼링크, 테이블 등이 포함 됩니다.

편집 모드:

![편집 모드에서 텍스트 단계의 스크린샷](./media/workbooks-text-visualizations/text-edit-mode.png)

미리 보기 모드:

![미리 보기 탭의 편집 모드에 있는 텍스트 단계의 스크린샷](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>텍스트 컨트롤 추가

1. **편집** 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
2. 텍스트 **추가** 링크를 사용 하 여 통합 문서에 텍스트 컨트롤을 추가 합니다.
3. 편집기 필드에 Markdown를 추가 합니다.
4. *텍스트 스타일* 옵션을 사용 하 여 Azure Portal의 표준 정보/경고/성공/오류 스타일을 사용 하 여 래핑된 일반 markdown와 markdown를 전환할 수 있습니다.
5. **미리 보기** 탭을 사용 하 여 콘텐츠가 어떻게 표시 되는지 확인할 수 있습니다. 편집 하는 동안 미리 보기는 크기를 제한 하는 스크롤 막대 영역 안에 콘텐츠를 표시 합니다. 그러나 런타임에 markdown 콘텐츠는 스크롤 막대 없이 필요한 공간을 채우도록 확장 됩니다.
6. **편집 완료** 단추를 선택 하 여 단계 편집을 완료 합니다.

> [!TIP]
> 이 [Markdown 참고 자료 시트](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) 를 사용 하 여 다양 한 서식 옵션에 대해 알아보세요.

## <a name="text-styles"></a>텍스트 스타일

텍스트 단계에 사용할 수 있는 텍스트 스타일은 다음과 같습니다.

| 스타일     | 설명                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | 추가 서식 지정이 적용 되지 않습니다.                                                      |
| `info`    | `ℹ`또는 유사한 아이콘 및 일반적으로 파란색 배경을 사용 하는 포털의 "info" 스타일      |
| `error`   | 포털의 "오류" 스타일 ( `❌` 또는 유사한 아이콘 및 일반적으로 빨강 배경)입니다.     |
| `success` | 포털의 "성공" 스타일 ( `✔` 또는 유사한 아이콘 및 일반적으로 녹색 배경)입니다.  |
| `upsell`  | 포털의 "상향 판매" 스타일 ( `🚀` 또는 유사한 아이콘 및 일반적으로 자주색 배경)입니다. |
| `warning` | 포털의 "경고" 스타일은 `⚠` 또는 유사한 아이콘 및 일반적으로 파란색 배경을 사용 합니다.   |

특정 스타일을 선택 하는 대신 스타일의 소스로 텍스트 매개 변수를 선택할 수도 있습니다. 매개 변수 값은 위의 텍스트 값 중 하나 여야 합니다. 값 또는 인식할 수 없는 값이 없으면 스타일로 처리 됩니다 `plain` .

정보 스타일 예:

![정보 스타일의 모양에 대 한 스크린샷](./media/workbooks-text-visualizations/text-preview-info-style.png)

경고 스타일 예:

![경고 스타일의 모습입니다.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>다음 단계

* [통합 문서에서 차트](workbooks-chart-visualizations.md)를 만드는 방법에 대해 알아봅니다.
* [통합 문서에서 그리드](workbooks-grid-visualizations.md)를 만드는 방법에 대해 알아봅니다.
