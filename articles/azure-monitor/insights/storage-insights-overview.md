---
title: 저장소에 대 한 Azure Monitor를 사용 하 여 Azure Storage 서비스 모니터링 (미리 보기) | Microsoft Docs
description: 이 문서에서는 저장소 관리자에 게 Azure Storage 계정에 대 한 성능 및 사용 문제를 신속 하 게 이해 하는 저장소 기능을 제공 하는 Azure Monitor에 대해 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2019
ms.author: magoedte
ms.openlocfilehash: 1ab49d17ebf10a1ae0277768ddbd1878195d835d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212772"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>저장소에 대 한 Azure Monitor를 사용 하 여 저장소 서비스 모니터링 (미리 보기)

저장소 (미리 보기)에 대 한 Azure Monitor Azure Storage 서비스 성능, 용량 및 가용성에 대 한 통합 보기를 제공 하 여 Azure Storage 계정에 대 한 포괄적인 모니터링을 제공 합니다. 두 가지 방법으로 저장소 용량 및 성능을 관찰할 수 있습니다. 저장소 계정 또는 Azure Monitor에서 직접 확인 하 여 저장소 계정 그룹 전체를 볼 수 있습니다. 

이 문서는 저장소 (미리 보기)가 제공 하는 Azure Monitor 환경에 대 한 조치를 통해 저장소 계정의 상태 및 성능에 대 한 조치 가능한 정보를 얻을 수 있도록 하는 데 도움이 됩니다. 및 가용성 문제.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>저장소에 대 한 Azure Monitor 소개 (미리 보기)

경험을 살펴보기 전에 정보를 제공 하 고 시각화 하는 방법을 이해 해야 합니다. 저장소 계정에서 직접 또는 Azure Monitor에서 저장소 기능을 선택 하 든, 저장소에 대 한 Azure Monitor은 일관 된 환경을 제공 합니다. 

다음을 제공 합니다.

* 저장소 서비스 또는 API 작업의 상태에 따라 가용성의 스냅숏 보기를 보여 주는 **규모 관점에서** , 저장소 서비스에서 받은 총 요청 수를 보여 주는 사용률, 평균 시간을 보여 주는 대기 시간 저장소 서비스 또는 API 작업 형식에서 요청을 처리 하는 중입니다. Blob, 파일, 테이블 및 큐를 기준으로 용량을 볼 수도 있습니다.

* 특정 저장소 계정에 대 한 **분석을 드릴 다운** 하 여 문제를 진단 하거나 범주별 가용성, 성능, 오류 및 용량을 기준으로 상세 분석을 수행할 수 있습니다. 이러한 옵션 중 하나를 선택 하면 메트릭의 심도 있는 보기가 제공 됩니다.  

* **사용자 지정 가능** -표시 하려는 메트릭을 변경 하 고, 제한과 일치 하는 임계값을 수정 하거나 설정 하 고, 사용자의 통합 문서로 저장할 수 있습니다. 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.  

이 기능을 사용 하도록 설정 하거나 구성 하지 않아도 되지만 저장소 계정의 저장소 메트릭은 기본적으로 수집 됩니다. Azure Storage에서 사용할 수 있는 메트릭에 익숙하지 않은 경우 [Azure Storage 메트릭을](../../storage/common/storage-metrics-in-azure-monitor.md)검토 하 여 Azure Storage 메트릭의 설명 및 정의를 확인 하세요.

>[!NOTE]
>이 기능에 액세스 하는 것은 무료로 제공 되며 [Azure Monitor 가격 책정 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명 된 대로 구성 하거나 사용 하도록 설정한 Azure Monitor 필수 기능에 대해서만 요금이 부과 됩니다.

>[!NOTE]
>저장소에 대 한 Azure Monitor [범용 v1 계정을](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)지원 하지 않습니다.
>

## <a name="view-from-azure-monitor"></a>Azure Monitor에서 보기

Azure Monitor에서 구독의 여러 저장소 계정에서 트랜잭션, 대기 시간 및 용량 정보를 볼 수 있으며 성능, 용량 문제 및 오류를 식별 하는 데 도움이 됩니다.

모든 구독에서 저장소 계정의 사용률과 가용성을 보려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal의 왼쪽 창에서 **모니터** 를 선택 하 고, **Insights** 섹션 아래에서 **Storage 계정 (미리 보기)** 을 선택 합니다.

    ![여러 저장소 계정 보기](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>개요 통합 문서

선택한 구독의 **개요** 통합 문서에서 테이블은 구독 내에 그룹화 된 최대 10 개의 저장소 계정에 대 한 대화형 저장소 메트릭 및 서비스 가용성 상태를 표시 합니다. 다음 드롭다운 목록에서 선택한 옵션을 기준으로 결과를 필터링 할 수 있습니다.

* **구독** -저장소 계정이 있는 구독만 나열 됩니다.  

* **저장소 계정** -기본적으로 10 개의 저장소 계정이 미리 선택 되어 있습니다. 범위 선택기에서 모든 또는 여러 저장소 계정을 선택 하는 경우 최대 200 개의 저장소 계정이 반환 됩니다. 예를 들어 선택한 3 개의 구독에 대해 총 573 개의 저장소 계정이 있는 경우 200 계정만 표시 됩니다. 

* **시간 범위** -기본적으로는 선택한 항목을 기준으로 지난 4 시간 동안의 정보를 표시 합니다.

드롭다운 목록 아래의 카운터 타일은 구독의 총 저장소 계정 수를 롤업 하 고 선택 된 총 수를 반영 합니다. 통합 문서에서 트랜잭션 메트릭 또는 오류를 보고 하는 열에 대 한 조건부 색 구분 또는 열 지도이 있습니다. 가장 높은 색은 가장 높은 값을 가지 며 가장 밝은 색은 가장 작은 값을 기준으로 합니다. 오류 기반 열의 경우 값은 빨간색이 고 메트릭 기반 열에 대해서는 값이 파란색으로 되어 있습니다.

열 **가용성**, **E2E 대기 시간**, **서버 대기 시간**및 **트랜잭션 오류 유형/오류** 열에서 값을 선택 하면 해당 항목에 대해 선택한 열과 일치 하는 특정 유형의 저장소 메트릭에 맞는 보고서로 이동 합니다. 저장소 계정. 각 범주의 통합 문서에 대 한 자세한 내용은 아래의 [자세한 저장소 통합 문서](#detailed-storage-workbooks) 섹션을 참조 하세요. 

>[!NOTE]
>보고서에 표시 될 수 있는 오류에 대 한 자세한 내용은 [응답 유형 스키마](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) 및 응답 유형 (예: **serverothererror**, **clientothererror**, **ClientThrottlingError**)을 참조 하세요. 선택한 저장소 계정에 따라 세 가지 유형의 오류가 보고 된 경우 다른 모든 오류는 **기타**범주에 표시 됩니다.

기본 **가용성** 임계값은 다음과 같습니다.

* 경고-99%
* 위험-90%

관찰 또는 요구 사항의 결과에 따라 가용성 임계값을 설정 하려면 [가용성 임계값 수정](#modify-the-availability-threshold)을 검토 합니다. 

### <a name="capacity-workbook"></a>용량 통합 문서

페이지 맨 위에 있는 **용량** 을 선택 하면 **용량** 통합 문서가 열립니다. 계정에 사용 되는 총 저장소의 양과 계정에 있는 각 데이터 서비스에서 사용 되는 용량을 확인 하 여 사용 되는 저장소를 확인 하는 데 도움이 됩니다.

![여러 storage 계정 용량 통합 문서](./media/storage-insights-overview/storage-account-capacity-02.png) 

통합 문서의 열에는 파란색 값을 사용 하 여 용량 메트릭을 보고 하는 조건적 색 구분 또는 열 지도 있습니다. 가장 높은 색은 가장 높은 값을 가지 며 가장 밝은 색은 가장 작은 값을 기준으로 합니다.

통합 문서의 열 중 하나에서 값을 선택 하면 저장소 계정에 대 한 **용량** 통합 문서로 드릴 다운 됩니다. 드릴 다운 보고서에 대 한 자세한 내용은 아래의 [자세한 저장소 통합 문서](#detailed-storage-workbooks) 섹션에 설명 되어 있습니다. 

## <a name="view-from-a-storage-account"></a>저장소 계정에서 보기

저장소 계정에서 직접 VM용 Azure Monitor에 액세스 하려면 다음을 수행 합니다.

1. Azure Portal에서 저장소 계정을 선택 합니다.

2. 목록에서 저장소 계정을 선택 합니다. 모니터링 섹션에서 Insights (미리 보기)를 선택 합니다.

    ![선택한 저장소 계정 개요 페이지](./media/storage-insights-overview/storage-account-direct-overview-01.png)

저장소 계정에 대 한 **개요** 통합 문서에는 다음을 신속 하 게 평가 하는 데 도움이 되는 몇 가지 저장소 성능 메트릭이 표시 됩니다.

* 저장소 서비스의 상태를 통해 컨트롤 외부의 문제가 배포 된 지역의 저장소 서비스에 영향을 주는지 즉시 확인할 수 있습니다 .이는 **요약** 열 아래에 명시 되어 있습니다.

* 저장소 용량, 가용성, 트랜잭션 및 대기 시간과 관련 된 가장 중요 한 정보를 보여 주는 대화형 성능 차트  

* 메트릭 및 상태 타일에는 서비스 가용성, 저장소 서비스에 대 한 총 트랜잭션 수, E2E 대기 시간 및 서버 대기 시간이 강조 표시 됩니다.

**오류**, **성능**, **가용성**및 **용량** 에 대 한 단추 중 하나를 선택 하면 해당 통합 문서가 열립니다. 

![선택한 저장소 계정 개요 페이지](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>자세한 저장소 통합 문서

여러 저장소 계정 **개요** 통합 문서에서 **사용 가능**열 값, **E2E 대기 시간**, **서버 대기 시간**및 **트랜잭션 오류 유형/오류** 를 선택 했는지 여부에 관계 없이 다음 단추 중 하나를 선택 합니다. **개요** 통합 문서에서 **발생**하는 오류, **성능**, **가용성**및 **용량** 은 특정 저장소 계정에서 각 범주에 맞게 조정 된 대화형 저장소 관련 정보 집합을 제공 합니다.  

* **가용성** **가용성 통합 문서** 를 엽니다. Azure Storage 서비스의 현재 상태를 표시 하 고, 선택 된 시간 범위를 나타내는 추세 선이 있는 저장소 계정에 정의 된 데이터 서비스에 의해 분류 된 각 개체의 사용 가능한 상태와에 대 한 가용성 추세 차트를 보여 주는 테이블입니다. 계정의 각 데이터 서비스입니다.  

    ![가용성 보고서 예제](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E 대기 시간** 및 **서버 대기 시간** 으로 **성능** 통합 문서를 엽니다. E2E 대기 시간 및 서버 대기 시간을 보여 주는 롤업 상태 타일, E2E 및 서버 대기 시간의 성능 차트, 저장소 계정에 정의 된 데이터 서비스에 의해 분류 된 API의 성공적인 호출 대기 시간 (테이블)이 포함 되어 있습니다.

    ![성능 보고서 예](./media/storage-insights-overview/storage-account-performance-01.png)

* 표에 나열 된 오류 범주 중 하나를 선택 하면 **실패** 통합 문서가 열립니다. 이 보고서에는 설명 및 성공한 요청, 클라이언트 제한 오류, ClientOtherError 특성과 관련 된 트랜잭션 **응답 유형** 차원 메트릭에 대 한 성능 차트를 제외 하 고 다른 모든 클라이언트 쪽 오류의 메트릭 타일이 표시 됩니다. 그리고 두 개의 테이블- **API 이름별 트랜잭션과** **응답 유형별**트랜잭션.

   ![오류 보고서 예](./media/storage-insights-overview/storage-account-failures-01.png)

* **Capacity** **capacity** 통합 문서를 엽니다. 타일 및 차트의 계정에 있는 각 저장소 데이터 개체에 사용 되는 총 저장소 크기와 계정에 저장 된 데이터 개체 수를 보여 줍니다.  

    ![선택한 저장소 계정 용량 페이지](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>고정 및 내보내기

섹션의 오른쪽 위에 있는 압정 아이콘을 선택 하 여 메트릭 섹션 중 하나를 Azure 대시보드에 고정할 수 있습니다.

![대시보드 섹션 대시보드에 고정 예](./media/storage-insights-overview/workbook-pin-example.png)

다중 구독 및 저장소 계정 **개요** 또는 **용량** 통합 문서는 압정 아이콘 오른쪽에 있는 아래쪽 화살표 아이콘을 선택 하 여 Excel 형식으로 결과 내보내기를 지원 합니다.

![통합 문서 표 내보내기 결과 예제](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>저장소에 대 한 Azure Monitor 사용자 지정 (미리 보기)

이 섹션에서는 데이터 분석 요구 사항을 지원 하기 위해 통합 문서를 편집 하는 일반적인 시나리오를 중점적으로 설명 합니다.

* 항상 특정 구독 또는 저장소 계정을 선택 하도록 통합 문서 범위를 선택 합니다.
* 표에서 메트릭 변경
* 가용성 임계값 변경
* 색 렌더링 변경

사용자 지정은 게시 된 통합 문서에서 기본 구성을 덮어쓰지 않도록 사용자 지정 통합 문서에 저장 됩니다. 통합 문서는 사용자가 개인적으로 사용 하는 **내 보고서** 섹션 또는 리소스 그룹에 액세스할 수 있는 모든 사용자가 액세스할 수 있는 **공유 보고서** 섹션에서 리소스 그룹 내에 저장 됩니다. 사용자 지정 통합 문서를 저장 한 후에는 통합 문서 갤러리로 이동 하 여 시작 해야 합니다.

![명령 모음에서 통합 문서 갤러리 시작](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>구독 또는 저장소 계정 지정

여러 구독 및 저장소 계정 **개요** 나 **용량** 통합 문서가 실행 될 때마다 특정 구독 또는 저장소 계정으로 범위가 되도록 구성할 수 있습니다. 다음 단계를 수행 합니다.

1. 포털에서 **모니터** 를 선택 하 고 왼쪽 창에서 **저장소 계정 (미리 보기)** 을 선택 합니다.

2. **개요** 통합 문서의 명령 모음에서 **편집**을 선택 합니다.

3. **구독** 드롭다운 목록에서 기본으로 지정할 하나 이상의 구독을 선택 합니다. 통합 문서에서는 총 10 개의 구독을 선택할 수 있습니다.  

4. **저장소 계정** 드롭다운 목록에서 기본으로 지정할 하나 이상의 계정을 선택 합니다. 통합 문서에서는 총 200 개의 저장소 계정을 선택 하도록 지원 합니다. 

5. 명령 모음에서 다른 **이름으로 저장** 을 선택 하 여 사용자 지정 항목이 포함 된 통합 문서의 복사본을 저장 한 다음 **편집 완료** 를 클릭 하 여 읽기 모드로 돌아갑니다.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>통합 문서의 메트릭 및 색 수정

미리 작성 된 통합 문서에는 메트릭 데이터가 포함 되어 있으며, 시각화 중 하나를 수정 하거나 제거 하 고 팀의 특정 요구에 맞게 사용자 지정할 수 있습니다. 

이 예제에서는 다중 구독 및 저장소 계정 용량 통합 문서를 사용 하 여 다음 작업을 수행 하는 방법을 보여 줍니다.

* 메트릭 제거
* 색 렌더링 변경

미리 작성 된 **오류**, **성능**, **가용성**및 **용량** 통합 문서 중 하나에 대해 동일한 변경 내용을 수행할 수 있습니다.

1. 포털에서 **모니터** 를 선택 하 고 왼쪽 창에서 **저장소 계정 (미리 보기)** 을 선택 합니다.

2. 용량 **을 선택 하** 여 용량 통합 문서로 전환 하 고 명령 모음에서 명령 모음에서 **편집** 을 선택 합니다.

    ![편집을 선택 하 여 통합 문서 수정](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 메트릭 섹션 옆에 있는 **편집**을 선택 합니다. 

    ![용량 통합 문서 메트릭을 수정 하려면 편집을 선택 합니다.](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. **계정 사용 용량 시간 표시 막대** 열을 제거 하 여 메트릭 표에서 **열 설정** 을 선택 하겠습니다.

    ![열 설정 편집](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. **열 설정 편집** 창의 **,/Storageaccounts-Capacity-Timeline $ | 열에서을 선택 합니다. 계정 사용 용량 시간 표시 막대 $** , 드롭다운 목록 **열 렌더러** 아래에서 **숨김**을 선택 합니다. 

6. **저장 후 닫기** 를 선택 하 여 변경 내용을 커밋합니다.

이제 파란색 대신 녹색을 사용 하도록 보고서의 용량 메트릭에 대 한 색 테마를 변경 하겠습니다.

1. 메트릭 표에서 **열 설정** 을 선택 합니다.

2. **열 설정 편집** 창의 **열** 섹션에서 **, $ | microsoft storage/Storageaccounts/Blobservices-capacity-blobservices $ | microsoft. storage/를 선택 합니다. storageaccounts/fileservices-FileCapacity $ | queueservices/storageaccounts/-QueueCapacity $ | microsoft storage/storageaccounts/tableservices-TableCapacity $** . 드롭다운 **목록 색상표에서** **녹색**을 선택 합니다.

3. **저장 후 닫기** 를 선택 하 여 변경 내용을 커밋합니다.

4. 명령 모음에서 다른 **이름으로 저장** 을 선택 하 여 사용자 지정 항목이 포함 된 통합 문서의 복사본을 저장 한 다음 **편집 완료** 를 클릭 하 여 읽기 모드로 돌아갑니다.  

### <a name="modify-the-availability-threshold"></a>가용성 임계값을 수정 합니다.

이 예제에서는 저장소 계정 용량 통합 문서를 사용 하 고 가용성 임계값을 수정 하는 방법을 보여 줍니다. 기본적으로, 타일 및 그리드 보고 비율은 최소 임계값 90 및 최대 임계값 99으로 구성 됩니다. 가용성%의 가용성 **%** 에 대 한 최소 임계값을 **API 이름으로 사용 가능** 표에서 85%로 변경 합니다. 즉, 임계값이 85% 미만인 경우 상태가 위험으로 변경 됩니다. 

1. 포털에서 **저장소 계정** 을 선택 하 고 목록에서 저장소 계정을 선택 합니다.

2. 왼쪽 창에서 **Insights (미리 보기)** 를 선택 합니다.

3. 통합 문서에서 **가용성** 을 선택 하 여 가용성 통합 문서로 전환한 다음 명령 모음에서 **편집** 을 선택 합니다. 

4. 페이지 아래쪽으로 스크롤한 다음 **API로 가용성** 표 옆의 왼쪽에서 **편집**을 선택 합니다.

    ![API 이름 그리드 설정에 따라 가용성 편집](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. 열 **설정** 을 선택한 다음 **열 설정 편집** 창의 **열** 섹션 아래에서 **가용성 (%)을 선택 합니다. (임계값 + 형식 지정)** .

6. **위험** 상태에 대 한 값을 **90** 에서 **85** 로 변경 하 고 **저장 후 닫기**를 클릭 합니다.

    ![위험 상태에 대 한 가용성 임계값을 수정 합니다.](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 명령 모음에서 다른 **이름으로 저장** 을 선택 하 여 사용자 지정 항목이 포함 된 통합 문서의 복사본을 저장 한 다음 **편집 완료** 를 클릭 하 여 읽기 모드로 돌아갑니다.

## <a name="troubleshooting"></a>문제 해결

이 섹션은 저장소 (미리 보기)에 대 한 Azure Monitor를 사용할 때 발생할 수 있는 일반적인 문제 중 일부에 대 한 진단 및 문제 해결에 도움이 됩니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

### <a name="resolving-performance-capacity-or-availability-issues"></a>성능, 용량 또는 가용성 문제 해결

저장소 (미리 보기)에 대 한 Azure Monitor에서 식별 하는 저장소 관련 문제를 해결 하려면 Azure Storage [문제 해결 지침](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)을 참조 하세요.  

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../platform/alerts-metric.md) 및 [서비스 상태 알림을](../../service-health/alerts-activity-log-service-notifications.md) 구성 하 여 문제를 검색 하는 데 도움이 되는 자동화 된 경고를 설정 합니다.

* 통합 문서가 지원 되도록 설계 된 시나리오, 새 보고서를 작성 하 고 기존 보고서를 사용자 지정 하는 방법 등에 대해 알아보고 [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서 만들기](../app/usage-workbooks.md)를 검토 하세요.

* 스토리지 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.
