---
title: Azure 포털의 디스크 메트릭 사용 중단 | 마이크로 소프트 문서
description: 더 이상 사용되지 않은 디스크 메트릭과 새 메트릭을 사용하도록 메트릭 경고를 업데이트하는 방법을 알아보세요.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299804"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Azure 포털의 디스크 메트릭 사용 중단

더 이상 사용되지 않도록 디스크 관련 메트릭은 곧 Azure 포털에서 제거됩니다. 사용되지 더 이상 사용되지 않습니다. 이 도움말에서는 새로운 측정항목과 이를 사용하도록 측정항목 경고를 업데이트하는 방법을 보여 줍니다.

## <a name="list-of-new-metrics"></a>새 메트릭 목록

이 표는 사용되지 않는 각 메트릭을 해당 새 메트릭에 매핑합니다. 

|더 이상 사용되지 않습니다.|새(대체) 메트릭|
|----|----|
|데이터 디스크 QD(더 이상 사용되지 않습니다)|데이터 디스크 큐 깊이(미리 보기)|
|데이터 디스크 읽기 바이트/초(더 이상 사용되지)|데이터 디스크 읽기 바이트/초(미리 보기)|
|데이터 디스크 읽기 작업/초(더 이상 사용되지)|데이터 디스크 읽기 작업/초(미리 보기)|
|데이터 디스크 쓰기 바이트/초(더 이상 사용되지)|데이터 디스크 쓰기 바이트/초(미리 보기)|
|데이터 디스크 쓰기 작업/초(더 이상 사용되지)|데이터 디스크 쓰기 작업/초(미리 보기)|
|OS QD(더 이상 사용되지 않습니다)|OS 큐 깊이(미리 보기)|
|OS 읽기 바이트/초(더 이상 사용되지 않습니다)|OS 읽기 바이트/초(미리 보기)|
|OS 읽기 작업/초(더 이상 사용되지 않습니다)|OS 읽기 작업/초(미리 보기)|
|OS 쓰기 바이트/초(더 이상 사용되지 않습니다)|OS 쓰기 바이트/초(미리 보기)|
|OS 쓰기 작업/초(더 이상 사용되지 않습니다)|OS 쓰기 작업/초(미리 보기)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>메트릭 경고에서 측정항목 마이그레이션

메트릭 경고를 업데이트하여 새 측정항목을 사용합니다.

1. Azure 포털에서 경고를 **검색합니다.** 그런 다음 **서비스** 섹션에서 **경고를 선택합니다.**

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. 경고 페이지에서 경고 규칙 **관리** **단추를 선택합니다.** 

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. 리소스 **그룹** 드롭다운 목록에서 가상 **시스템** 확인란을 선택하고 **신호 유형** 드롭다운 목록에서 메트릭 확인란을 **선택합니다.** 

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. 메트릭 목록에서 디스크와 관련된 조건을 식별합니다. 규칙의 이름을 클릭합니다. 

   이름은 테이블의 **이름** 열에 하이퍼링크로 나타납니다.

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. **규칙 관리** 페이지의 **조건** 섹션에서 경고 조건을 클릭합니다. 

   조건이 하이퍼링크로 나타납니다.  

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   **신호 논리 구성** 페이지가 나타나고 조건의 설정이 해당 페이지의 경고 **논리** 섹션에 나타납니다.

6. 사용되지 않는 메트릭을 제거할 때 이러한 설정이 사라지도록 기록합니다.

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > 스크린샷이나 텍스트 파일에서 이러한 설정을 캡처하는 것이 좋습니다. 

7. 뒤로를 클릭하여 신호 선택 링크를 **클릭합니다.**

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. 신호 **논리 구성** 페이지에서 적절한 대체 메트릭(새 메트릭)을 선택합니다. 이 문서의 앞에 나타나는 [테이블을](#update-metrics) 사용하여 새 메트릭의 이름을 ID로 지정합니다.

   > [!TIP] 
   > 검색 표시줄에 입력을 시작하여 메트릭 이름 목록을 좁힐 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. 완료 버튼을 **선택합니다.** 

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. **저장** 단추를 선택하여 변경 내용을 커밋합니다. 

    > [!div class="mx-imgBorder"]
    > ![이미지 설명](./media/portal-disk-metrics-deprecation/save-new-metric.png)






