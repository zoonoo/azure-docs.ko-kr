---
title: Azure 모니터 보기 디자이너에서 통합 문서 변환 옵션
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658713"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure 모니터 보기 디자이너에서 통합 문서 변환 옵션
[뷰 디자이너는](view-designer.md) 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역에서 데이터를 시각화하는 데 도움이 되는 사용자 지정 보기를 만들 수 있는 Azure Monitor의 기능입니다. 단계적으로 폐지되고 추가 기능을 제공하는 통합 문서로 대체됩니다. 이 문서에서는 둘 사이의 기본 개념과 뷰를 통합 문서로 변환하는 옵션을 비교합니다.

## <a name="basic-workbook-designs"></a>기본 통합 문서 디자인

뷰 디자이너는 고정된 정적 표현 스타일을 가지며 통합 문서를 사용하면 데이터를 표현하는 방법을 자유롭게 포함하고 수정할 수 있습니다. 아래 이미지는 뷰를 변환할 때 통합 문서를 정렬하는 방법에 대한 두 가지 예를 설명합니다.

[수직 통합 문서](view-designer-conversion-examples.md#vertical)
![수직](media/view-designer-conversion-options/view-designer-vertical.png)

[탭 된 작업문서](view-designer-conversion-examples.md#tabbed)
![데이터](media/view-designer-conversion-options/distribution-tab.png)
![유형 배포 탭 시간 동안 데이터 유형 탭](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>타일 변환
뷰 디자이너는 개요 타일 기능을 사용하여 전체 상태를 나타내고 요약합니다. 숫자에서 차트에 이르기까지 7개의 타일로 표시됩니다. 통합 문서에서 사용자는 유사한 시각화를 만들고 개요 타일의 원래 스타일과 유사하도록 고정할 수 있습니다. 

![갤러리](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>대시보드 변환 보기
뷰 디자이너 타일은 일반적으로 두 개의 섹션, 시각화 및 시각화의 데이터와 일치하는 목록(예: **도넛 & 목록** 타일)으로 구성됩니다.

![도넛](media/view-designer-conversion-options/donut-example.png)

통합 문서를 사용하면 사용자가 보기의 하나 또는 두 섹션을 쿼리하도록 선택할 수 있습니다. 통합 문서에서 쿼리를 공식화하는 것은 간단한 2단계 프로세스입니다. 첫째, 데이터는 쿼리에서 생성되고 둘째, 데이터는 시각화로 렌더링됩니다.  통합 문서에서 이 보기를 다시 만들 수 있는 방법의 예는 다음과 같습니다.

![변환](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>다음 단계
- [통합 문서 & 사용 권한 액세스](view-designer-conversion-access.md)