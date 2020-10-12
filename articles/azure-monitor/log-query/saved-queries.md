---
title: Azure Monitor Log Analytics에 저장 된 쿼리
description: 필요에 따라 시작 하 고 수정할 수 있는 쿼리
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: 462358ab967d4ba26a265d7b5f401b995223815e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85959845"
---
# <a name="saved-queries-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics에 저장 된 쿼리

Log Analytics은 자체적으로 실행 하거나 고유한 쿼리를 위한 시작 지점으로 사용할 수 있는 예제 쿼리 집합을 제공 합니다. 이 문서에서는 예제 쿼리와 이러한 쿼리를 사용 하는 방법을 설명 합니다.

Log Analytics 또는 KQL 쿼리 언어에 익숙하지 않은 경우 예제 쿼리를 사용 하 여 시작 하는 것이 좋은 방법입니다. 이러한 사용자는 리소스에 대 한 즉각적인 통찰력을 제공 하 고 KQL 사용을 시작 하는 좋은 방법을 제공 하므로 Log Analytics 사용을 시작 하는 데 걸리는 시간을 단축할 수 있습니다. 즉시 가치를 제공 하도록 설계 된 250 예제 쿼리를 수집 하 고 큐 레이트 쿼리 예제 수가 지속적으로 증가 하 고 있습니다.

## <a name="in-context-queries"></a>상황에 맞는 쿼리

새 환경에서는 컨텍스트에서 쿼리를 필터링 하 고 제안 합니다. 즉, 시스템은 선택한 범위와 관련 된 쿼리만 자동으로 표시 합니다.

- **단일 리소스** 의 경우-쿼리는 리소스 유형에 따라 필터링 됩니다.
- **리소스 그룹** 의 경우 쿼리는 특정 리소스 그룹의 리소스에 따라 필터링 됩니다.
- **작업 영역의** 경우-쿼리는 작업 영역에 설치 된 솔루션에 따라 필터링 됩니다.

이 동작은 모든 Log Analytics 범위에 대해 일치 합니다. 원하는 리소스 종류에 대 한 예제 쿼리가 표시 되지 않는 경우에는 컨텍스트에 따라 필터가 있기 때문일 수 있습니다. 이후 섹션에서는 가능한 모든 쿼리를 볼 수 있도록 컨텍스트 내 범위를 제거 하는 방법을 설명 합니다.

> [!TIP]
> 범위에 포함 된 리소스가 많을 수록 포털에서 필터링 하 고 샘플 쿼리 대화 상자를 표시 하는 시간이 길어집니다.

## <a name="example-query-user-interface"></a>예제 쿼리 사용자 인터페이스

서로 다른 두 위치에서 예제 쿼리를 가져올 수 있습니다.

### <a name="example-query-dialog"></a>예제 쿼리 대화 상자

Log Analytics 환경을 처음 입력할 때 *쿼리 예제 대화 상자가* 자동으로 표시 됩니다.  **예제 쿼리의**화면 오른쪽 위에 있는를 클릭 하 여 액세스할 수도 있습니다.

![사이드바](media/saved-queries/sidebar-2.png)

그런 다음 쿼리 예제 대화 상자가 아래와 같이 표시 됩니다.  

![예제 쿼리 화면](media/saved-queries/example-query-start.png)

이전 스크린샷은 Azure Key Vault 인스턴스에 대 한 로그 화면을 표시 합니다. 이 문서의 앞에서 설명한 것 처럼 쿼리는 컨텍스트에 표시 됩니다.  그 결과 Key Vault 관련 된 예제만 스크린샷에서 표시 됩니다. 전체 구독을 선택 하면 해당 구독의 모든 리소스 유형에 대 한 쿼리가 표시 됩니다.  

각 예제 쿼리는 카드로 표시 됩니다. 쿼리를 신속 하 게 검색 하 여 필요한 항목을 찾을 수 있습니다. 대화 상자에서 직접 쿼리를 실행 하거나 추가 미세 조정 및 조정을 위해 쿼리 편집기에 로드 하도록 선택할 수 있습니다.

### <a name="sidebar-query-experience"></a>사이드바 쿼리 환경

Log Analytics의 왼쪽 세로 막대에 있는 쿼리 창에서 대화 상자 환경의 모든 기능에 액세스할 수 있습니다. 쿼리 이름을 마우스로 가리키면 쿼리 설명 및 추가 기능을 가져올 수 있습니다.

![사이드바](media/saved-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>쿼리 찾기 및 필터링

이 섹션의 옵션은 대화 상자 및 사이드바 쿼리 환경에서 사용할 수 있지만 사용자 인터페이스가 약간 다릅니다.  

### <a name="use-favorites"></a>즐겨찾기 사용

자주 사용 하는 쿼리를 사용 하면 빠르게 액세스할 수 있습니다.

> [!TIP]
> 나중에 쿼리를 수집 하 고 표시 하는 것이 좋은 방법입니다. 필요한 쿼리를 찾고 쿼리 옆의 별표를 클릭 하 여 즐겨찾기에 추가 합니다. 나중에 쿼리가 유용 하지 않은 경우에는 즐겨찾기를 해제할 수 있습니다.  

### <a name="filtering-and-group-by"></a>필터링 및 그룹화 방법

쿼리 대화 상자에서 올바른 컨텍스트의 쿼리만 표시 하도록 선택할 수 있지만 필터를 제거 하 고 모든 쿼리를 볼 수 있습니다.

### <a name="group-by"></a>Group By

*묶는 방법* 드롭다운 목록을 클릭 하 여 쿼리 그룹화를 변경 합니다.

![예제 쿼리 화면 groupby](media/saved-queries/example-query-groupby.png)

대화 상자는 다음을 기준으로 그룹화를 지원 합니다.

- **리소스 종류** – 가상 머신과 같이 Azure에 정의 된 리소스입니다. Azure Monitor Logs/Log Analytics 테이블을 리소스 유형에 대 한 전체 매핑은 [Azure Monitor 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype) 를 참조 하세요.  
- **범주** – *보안* 또는 *감사*와 같은 정보의 유형입니다. 범주는 테이블 측 창에 정의 된 범주와 동일 합니다. 전체 범주 목록은 [Azure Monitor 테이블 참조](/azure/azure-monitor/reference/tables/tables-category) 를 참조 하세요.  
- **솔루션** – 쿼리와 연결 된 Azure Monitor 솔루션
- **토픽** – *활동 로그* 또는 *앱 로그*와 같은 예제 쿼리 항목입니다. 토픽 속성은 예제 쿼리에 고유 하며 특정 리소스 종류에 따라 다를 수 있습니다.

또한 그룹화 값은 활성 목차의 역할을 합니다. 화면 왼쪽에 있는 값 중 하나를 클릭 하면 쿼리 보기가 클릭 된 항목으로 오른쪽으로 스크롤됩니다.

### <a name="filter"></a>Assert

앞에서 설명한 groupby 값에 따라 쿼리를 필터링 할 수도 있습니다. 예제 쿼리 대화 상자에서 필터는 위쪽에 있습니다.

![예제 쿼리 화면 필터](media/saved-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Group by 및 필터 조합

필터 및 그룹화 방법 기능은 함께 작동 하도록 설계 되었습니다. 쿼리가 정렬 되는 방식을 유연 하 게 제공 합니다. 예를 들어 여러 리소스가 있는 리소스 그룹을 사용 하는 경우 특정 리소스 종류로 필터링 하 고 결과 쿼리를 항목별로 정렬할 수 있습니다.

## <a name="sample-query-dialog-appearance-behavior"></a>예제 쿼리 대화 상자 모양 동작

KQL pro 이며 쿼리 편집기로 직접 이동 하려는 경우 쿼리 대화 상자를 "해제"로 전환할 수 있습니다. 설정/해제를 사용 하면 Log Analytics 화면이 로드 될 때 쿼리 예제 대화 상자가 로드 되지 않습니다.

![예제 꺼짐](media/saved-queries/examples-on-off.png)

Log Analytics 맨 위 막대의 *예제* 쿼리 단추에서 항상 샘플 쿼리 팝업 환경에 액세스할 수 있습니다.

## <a name="query-explorer"></a>쿼리 탐색기

사용자가 생성 한 쿼리를 저장 하 고 공유 하는 쿼리 탐색기 환경은 시간 동안 변경 되지 않은 상태로 유지 됩니다.

## <a name="next-steps"></a>다음 단계

[KQL 쿼리 시작](get-started-queries.md)

