---
title: 포함 파일
description: 포함 파일
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026479"
---
## <a name="transform-data-before-using-large-usage-files"></a>큰 사용량 파일을 사용하기 전에 데이터 변환

사용량 또는 조정 파일이 너무 커서 열 수 없는 경우도 있습니다. 또는 정보의 일부만 문제를 해결하는 데 필요할 수도 있습니다. 예를 들어 특정 리소스에 대한 정보만 필요하거나 소수의 서비스 또는 리소스 그룹에 대한 사용량만 필요할 수 있습니다. 이 경우 피벗 테이블을 만들기 전에 데이터를 변환하여 요약할 수 있습니다.

1. Excel에서 빈 통합 문서를 엽니다.
1. 위쪽 메뉴에서 **데이터** > **텍스트/CSV**를 차례로 선택하고, 사용량 파일을 선택한 다음, **가져오기**를 선택합니다.
1. 창 아래쪽에서 **데이터 변환**을 선택합니다. 새 창에 데이터 요약이 표시됩니다.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="요약된 데이터를 보여 주는 예" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Microsoft 고객 계약이 있는 경우 MCA 사용량 파일에는 일반적으로 열 제목이 첫 번째 행에 있으므로 이 단계를 건너뛰고 다음 단계로 계속 진행합니다. 테이블을 만들어 데이터를 준비합니다. 제목만 남겨두고 상위 행을 제거합니다. **행 제거** > **상위 행 제거**를 차례로 선택합니다.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="요약된 데이터를 보여 주는 예" :::
1. [상위 행 제거] 창에서 위쪽에서 제거할 행 수를 입력합니다. 일반적으로 EA의 경우 2이고, CSP의 경우 1입니다. **확인**을 선택합니다.
1. **첫 행을 머리글로 사용**을 선택합니다.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="요약된 데이터를 보여 주는 예" :::
    
    테이블 보기의 위쪽에 열 제목이 표시됩니다.
1. 다음으로 필터를 추가합니다. 필터링할 각 열 제목의 오른쪽에 있는 선택기 화살표를 사용합니다. 제안되는 필터는 구독 ID, 서비스 이름(미터 범주), 인스턴스 ID, 리소스 그룹입니다. 동일한 문서에서 여러 필터를 사용할 수 있습니다. 문서 크기를 줄이고 나중에 작업하는 데 도움이 되도록 가능한 모든 필터를 적용하는 것이 좋습니다.
1. 필터가 적용되면 **닫기 및 로드**를 선택합니다.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="요약된 데이터를 보여 주는 예" :::

파일이 로드되고 필터링된 사용량 데이터가 있는 테이블이 표시됩니다. 이제 새 피벗 테이블을 만들어 사용량 문제를 해결할 수 있습니다.