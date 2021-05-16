---
title: Azure Monitor 뷰 디자이너에서 통합 문서로 변환 옵션
description: Azure Monitor에서 뷰를 통합 문서로 전환하기 위한 변환 옵션입니다.
author: shijatsu
ms.author: shijain
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 1110a19f67c41e5cc170e056f6d337bdf8860588
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108318066"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor 뷰 디자이너에서 통합 문서로 변환 옵션
[뷰 디자이너](view-designer.md)는 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역의 데이터를 시각화하는 데 도움이 되는 사용자 지정 뷰를 만들 수 있게 해주는 Azure Monitor 기능입니다. 이러한 기능은 단계적으로 제거되고 있으며, 추가 기능을 제공하는 통합 문서로 대체되고 있습니다. 이 문서에서는 뷰를 통합 문서로 변환하기 위한 두 가지 옵션의 기본적인 개념을 비교합니다.

## <a name="basic-workbook-designs"></a>기본 통합 문서 디자인

뷰 디자이너는 데이터를 고정된 정적 스타일로 표현하고, 통합 문서는 데이터의 표현 방식을 자유롭게 포함하고 수정하도록 지원합니다. 아래 이미지는 뷰를 변환할 때 통합 문서를 정렬하는 방법에 대한 두 가지 예를 보여 줍니다.

[세로 통합 문서](view-designer-conversion-examples.md#vertical)
![세로](media/view-designer-conversion-options/view-designer-vertical.png)

[탭 구분 통합 문서](view-designer-conversion-examples.md#tabbed)
![데이터 형식 분포 탭](media/view-designer-conversion-options/distribution-tab.png)
![시간별 데이터 형식 탭](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>타일 변환
뷰 디자이너는 개요 타일 기능을 사용하여 전체 상태를 나타내고 요약합니다. 이는 숫자에서 차트에 이르는 일곱 개의 타일로 표시됩니다. 통합 문서에서 사용자는 비슷한 시각화를 만들어 개요 타일의 원래 스타일과 비슷하게 고정할 수 있습니다. 

![갤러리](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>뷰 대시보드 변환
뷰 디자이너 타일은 일반적으로 시각화와 시각화의 데이터와 일치하는 목록(예: **도넛형 및 목록** 타일)이라는 두 가지 섹션으로 구성됩니다.

![도넛](media/view-designer-conversion-options/donut-example.png)

통합 문서에서는 사용자가 뷰의 섹션 한 개 또는 두 개를 쿼리하도록 선택할 수 있습니다. 통합 문서에서 쿼리를 작성하는 것은 간단한 2단계 프로세스로 이루어집니다. 첫 번째는 쿼리에서 데이터를 생성하는 것이고, 두 번째는 데이터를 시각화로 렌더링하는 것입니다.  통합 문서에서 이 뷰를 다시 만드는 방법의 예는 다음과 같습니다.

![변환](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>다음 단계
- [통합 문서 액세스 및 권한](view-designer-conversion-access.md)