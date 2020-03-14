---
title: Azure Portal에서 디스크 메트릭 사용 중단 | Microsoft Docs
description: 더 이상 사용 되지 않는 디스크 메트릭과 새 메트릭을 사용 하도록 메트릭 경고를 업데이트 하는 방법에 대해 알아봅니다.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299804"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Azure Portal에서 디스크 메트릭 사용 중단

더 이상 사용 되지 않는 디스크 관련 메트릭이 Azure Portal에서 곧 제거 될 예정입니다. 사용 되지 않는 각 메트릭의 새 버전을 사용할 수 있습니다. 이 문서에서는 새로운 메트릭과 메트릭을 사용 하도록 메트릭 경고를 업데이트 하는 방법을 보여 줍니다.

## <a name="list-of-new-metrics"></a>새 메트릭 목록

이 테이블은 사용 되지 않는 각 메트릭을 해당 하는 새 메트릭에 매핑합니다. 

|사용 되지 않는 메트릭|새 (대체) 메트릭|
|----|----|
|데이터 디스크 QD (사용 되지 않음)|데이터 디스크 큐 깊이 (미리 보기)|
|데이터 디스크 읽기 바이트/초 (사용 되지 않음)|데이터 디스크 읽기 바이트/초(미리 보기)|
|데이터 디스크 읽기 작업/초 (사용 되지 않음)|데이터 디스크 읽기 작업/초(미리 보기)|
|데이터 디스크 쓰기 바이트/초 (사용 되지 않음)|데이터 디스크 쓰기 바이트/초(미리 보기)|
|데이터 디스크 쓰기 작업/초 (사용 되지 않음)|데이터 디스크 쓰기 작업/초(미리 보기)|
|OS QD (사용 되지 않음)|OS 큐 깊이 (미리 보기)|
|OS Read Bytes/Sec (사용 되지 않음)|OS 읽기 바이트/초 (미리 보기)|
|OS 읽기 작업/초 (사용 되지 않음)|OS 읽기 작업/초 (미리 보기)|
|OS Write Bytes/Sec (사용 되지 않음)|OS Write Bytes/Sec (미리 보기)|
|OS 쓰기 작업/초 (사용 되지 않음)|OS 쓰기 작업/초 (미리 보기)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>메트릭 경고에서 메트릭 마이그레이션

메트릭 경고를 업데이트 하 여 새 메트릭을 사용 합니다.

1. Azure Portal에서 **경고**를 검색 합니다. 그런 다음 **서비스** 섹션에서 **경고**를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. **경고** 페이지에서 **경고 규칙 관리** 단추를 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. **리소스 그룹** 드롭다운 목록에서 **Virtual Machines** 확인란을 선택 하 고 **신호 형식** 드롭다운 목록에서 **메트릭** 확인란을 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. 메트릭 목록에서 디스크와 관련 된 조건을 식별 합니다. 규칙의 이름을 클릭 합니다. 

   이름은 테이블의 **이름** 열에 하이퍼링크로 나타납니다.

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. **규칙 관리** 페이지의 **조건** 섹션에서 경고의 조건을 클릭 합니다. 

   조건이 하이퍼링크로 표시 됩니다.  

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   **신호 논리 구성** 페이지가 표시 되 고 해당 페이지의 **경고 논리** 섹션에 조건 설정이 표시 됩니다.

6. 더 이상 사용 되지 않는 메트릭을 제거할 때 이러한 설정이 사라질 때 이러한 설정을 기록해 둡니다.

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > 스크린샷 또는 텍스트 파일에서 이러한 설정을 캡처할 것을 고려 합니다. 

7. **신호 선택 영역으로 돌아가기 링크를** 클릭 합니다.

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. **신호 논리 구성** 페이지에서 적절 한 대체 메트릭 (새 메트릭)을 선택 합니다. 이 문서 앞부분에 표시 된 [표](#update-metrics) 를 사용 하 여 새 메트릭의 이름을 식별할 수 있습니다.

   > [!TIP] 
   > 검색 창에서 입력을 시작 하 여 메트릭 이름 목록을 좁힙니다. 

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. **완료** 단추를 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![이미지 설명](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. **저장** 단추를 선택 하 여 변경 내용을 커밋합니다. 

    > [!div class="mx-imgBorder"]
    > ![이미지 설명](./media/portal-disk-metrics-deprecation/save-new-metric.png)






