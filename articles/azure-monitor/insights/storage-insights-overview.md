---
title: 저장소용 Azure 모니터를 통해 Azure 저장소 서비스 모니터링(미리 보기)| 마이크로 소프트 문서
description: 이 문서에서는 저장소 관리자에게 Azure Storage 계정의 성능 및 사용률 문제를 빠르게 이해할 수 있는 Azure Storage 용 Azure 모니터 기능에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: f23be7e764ad180a23c76abb7f9bb2218fd61e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662522"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>저장소용 Azure 모니터로 저장소 서비스 모니터링(미리 보기)

Azure 저장소용 모니터(미리 보기)는 Azure Storage 서비스 성능, 용량 및 가용성에 대한 통합보기를 제공하여 Azure Storage 계정에 대한 포괄적인 모니터링을 제공합니다. 저장소 용량 및 성능을 두 가지 방법으로 관찰할 수 있으며, 저장소 계정에서 직접 보거나 Azure Monitor에서 보기를 통해 저장소 계정 그룹 간에 볼 수 있습니다. 

이 문서에서는 저장소용 Azure Monitor for Storage(미리 보기)가 핫스팟에 집중하고 대기 시간, 제한, 및 가용성 문제.

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>저장소용 Azure 모니터 소개(미리 보기)

경험에 다이빙하기 전에, 당신은 그것이 정보를 제시하고 시각화하는 방법을 이해해야합니다. 저장소 계정에서 직접 저장소 기능을 선택하든 Azure 모니터에서 선택하든 저장소용 Azure 모니터는 일관된 환경을 제공합니다. 

결합하면 다음을 제공합니다.

* **스토리지 서비스** 또는 API 작업의 상태를 기반으로 해당 가용성에 대한 스냅숏 보기를 보여 주는 확장 관점에서 저장소 서비스가 수신하는 총 요청 수 및 저장소 서비스 또는 API 작업 유형이 요청을 처리하는 데 걸리는 평균 시간을 보여주는 대기 시간을 보여 주는 사용률입니다. Blob, 파일, 테이블 및 큐별로 용량을 볼 수도 있습니다.

* 특정 저장소 계정의 **분석을 드릴다운하여** 문제를 진단하거나 가용성, 성능, 실패 및 용량과 같은 범주별로 세부 분석을 수행할 수 있습니다. 이러한 옵션 중 하나를 선택하면 메트릭을 심층적으로 볼 수 있습니다.  

* 제한에 부합하는 임계값을 표시, 수정 또는 설정하고 사용자 고유의 통합 문서로 저장할 메트릭을 변경할 수 있는 **사용자 지정 이 가능합니다.** 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.  

이 기능은 아무것도 활성화하거나 구성할 필요가 없으며 저장소 계정의 저장소 메트릭은 기본적으로 수집됩니다. Azure Storage에서 사용할 수 있는 메트릭에 익숙하지 않은 경우 Azure 저장소 메트릭을 검토하여 Azure Storage 메트릭의 설명 및 정의를 [봅니다.](../../storage/common/storage-metrics-in-azure-monitor.md)

>[!NOTE]
>이 기능에 액세스하는 데는 요금이 부과되지 않으며 [Azure Monitor 가격 세부 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명된 대로 구성하거나 활성화한 Azure Monitor 필수 기능에 대해서만 요금이 부과됩니다.

>[!NOTE]
>저장소용 Azure 모니터는 [범용 v1 계정을](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)지원하지 않습니다.
>

## <a name="view-from-azure-monitor"></a>Azure 모니터에서 보기

Azure Monitor에서 구독의 여러 저장소 계정에서 트랜잭션, 대기 시간 및 용량 세부 정보를 보고 성능, 용량 문제 및 오류를 식별하는 데 도움이 됩니다.

모든 구독에서 저장소 계정의 사용률과 가용성을 확인하려면 다음 단계를 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. Azure 포털의 왼쪽 창에서 **모니터를** 선택하고 **Insights** 섹션에서 저장소 **계정(미리 보기)을**선택합니다.

    ![여러 저장소 계정 보기](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>개요 통합 문서

선택한 구독에 대한 **개요** 통합 문서의 표에는 구독 내에서 그룹화된 최대 10개의 저장소 계정에 대한 대화형 저장소 메트릭 및 서비스 가용성 상태가 표시됩니다. 다음 드롭다운 목록에서 선택한 옵션에 따라 결과를 필터링할 수 있습니다.

* **구독** - 저장소 계정이 있는 구독만 나열됩니다.  

* **저장소 계정** - 기본적으로 10개의 저장소 계정이 미리 선택됩니다. 범위 선택기에서 모든 저장소 또는 여러 저장소 계정을 선택하면 최대 200개의 저장소 계정이 반환됩니다. 예를 들어 선택한 세 개의 구독에서 총 573개의 저장소 계정이 있는 경우 200개의 계정만 표시됩니다. 

* **시간 범위** - 기본적으로 해당 선택 항목에 따라 지난 4시간의 정보가 표시됩니다.

드롭다운 목록 아래의 카운터 타일은 구독의 총 저장소 계정 수를 롤업하고 선택한 총 계정 수를 반영합니다. 통합 문서의 트랜잭션 메트릭 또는 오류를 보고하는 열에 대한 조건부 색상 코딩 또는 히트맵이 있습니다. 가장 깊은 색상은 가장 높은 값을 가지며 밝은 색상은 가장 낮은 값을 기반으로 합니다. 오류 기반 열의 경우 값이 빨간색이고 메트릭 기반 열의 경우 값이 파란색입니다.

열 **가용성,** **E2E 대기 시간,** 서버 **대기 시간**및 **트랜잭션 오류 유형/오류에서** 값을 선택하면 해당 저장소 계정에 대해 선택한 열과 일치하는 특정 유형의 저장소 메트릭에 맞게 조정된 보고서로 이동합니다. 각 범주에 대한 통합 문서에 대한 자세한 내용은 아래의 [자세한 저장소 통합 문서](#detailed-storage-workbooks) 섹션을 참조하십시오. 

>[!NOTE]
>보고서에 표시할 수 있는 오류에 대한 자세한 내용은 [응답 유형 스키마를](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions) 참조하고 **ServerOtherError,** **ClientOtherError,** **ClientThrottlingError**와 같은 응답 유형을 찾습니다. 선택한 저장소 계정에 따라 보고된 세 가지 유형 이상의 오류가 있는 경우 다른 모든 오류는 **기타**의 범주에 표시됩니다.

기본 **가용성** 임계값은 다음과 입니다.

* 경고 - 99%
* 중요 - 90%

관찰 또는 요구 사항의 결과에 따라 가용성 임계값을 설정하려면 [가용성 임계값을 수정합니다.](#modify-the-availability-threshold) 

### <a name="capacity-workbook"></a>용량 통합 문서

페이지 상단에서 **용량을** 선택하고 **용량** 통합 문서를 엽니다. 계정의 각 데이터 서비스에서 사용되는 총 저장소의 양과 사용 중인 저장소를 초과 및 미만으로 식별하는 데 도움이 되는 용량을 보여 줍니다.

![여러 저장소 계정 용량 통합 문서](./media/storage-insights-overview/storage-account-capacity-02.png) 

통합 문서의 열에 대한 조건부 색상 코딩 또는 히트맵이 있으며 이 워크북에는 파란색 값으로 용량 메트릭을 보고합니다. 가장 깊은 색상은 가장 높은 값을 가지며 밝은 색상은 가장 낮은 값을 기반으로 합니다.

통합 문서의 열 중 하나에서 값을 선택하면 저장소 계정에 대한 **용량** 통합 문서로 드릴다운합니다. 드릴다운 보고서에 대한 자세한 내용은 [아래의 자세한 저장소 통합 문서](#detailed-storage-workbooks) 섹션에 설명되어 있습니다. 

## <a name="view-from-a-storage-account"></a>저장소 계정에서 보기

저장소 계정에서 직접 VM용 Azure 모니터에 액세스하려면 다음을 수행합니다.

1. Azure 포털에서 저장소 계정을 선택합니다.

2. 목록에서 저장소 계정을 선택합니다. 모니터링 섹션에서 인사이트(미리 보기)를 선택합니다.

    ![선택한 저장소 계정 개요 페이지](./media/storage-insights-overview/storage-account-direct-overview-01.png)

저장소 계정에 대한 **개요** 통합 문서에는 다음과 같은 몇 가지 저장소 성능 메트릭이 표시됩니다.

* 저장소 서비스의 상태는 제어 외부의 문제가 배포된 지역의 저장소 서비스에 영향을 미치는지 즉시 확인할 수 있으며, 이는 **요약** 열 아래에 명시되어 있습니다.

* 저장소 용량, 가용성, 트랜잭션 및 대기 시간과 관련된 가장 중요한 세부 정보를 보여 주면 대화형 성능 차트입니다.  

* 서비스 가용성, 스토리지 서비스에 대한 총 트랜잭션 수, E2E 대기 시간 및 서버 대기 시간을 강조하는 메트릭 및 상태 타일입니다.

**실패,** **성능,** **가용성**및 **용량에** 대한 단추 중 하나를 선택하면 각 통합 통합 문서가 열립니다. 

![선택한 저장소 계정 개요 페이지](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>자세한 저장소 통합 문서

여러 저장소 계정 **개요 통합** 문서의 **가용성,** **E2E 대기 시간,** **서버 대기 시간**및 트랜잭션 오류 **유형/오류열에서** 값을 선택했든, 특정 저장소 계정의 **개요 통합** 문서에서 **오류,** **성능,** **가용성**및 **용량에** 대한 단추 중 하나를 선택하든 각 항목은 해당 범주에 맞는 대화형 저장소 관련 정보 집합을 제공합니다.  

* **가용성에는** 가용성 통합 문서를 **엽니다.** Azure Storage 서비스의 현재 상태, 선택한 시간 범위를 나타내는 추세선이 있는 저장소 계정에 정의된 데이터 서비스로 분류된 각 개체의 사용 가능한 상태 상태를 보여 주는 테이블 및 에 대한 가용성 추세 차트를 보여 주며, 계정의 각 데이터 서비스입니다.  

    ![가용성 보고서 예제](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E 대기 시간** 및 **서버 대기 시간이** **성능** 통합 문서를 엽니다. 여기에는 E2E 대기 시간 및 서버 대기 시간을 보여 줄 롤업 상태 타일, E2E 대 서버 대기 시간의 성능 차트, 저장소 계정에 정의된 데이터 서비스로 분류된 API로 성공적인 호출의 대기 시간을 세분화하는 테이블이 포함됩니다.

    ![실적 보고서 예](./media/storage-insights-overview/storage-account-performance-01.png)

* 표에 나열된 오류 범주 중 하나를 선택하면 **실패** 통합 문서가 열립니다. 이 보고서에는 설명된 요청 및 성공적인 요청, 클라이언트 제한 오류, ClientOtherError 특성에 따른 트랜잭션 **응답 유형** 차원 메트릭에 대한 성능 차트 및 API 이름별 트랜잭션 및 **응답 유형별** **트랜잭션을** 제외한 다른 모든 클라이언트 측 오류의 메트릭 타일이 표시됩니다.

   ![실패 보고서 예제](./media/storage-insights-overview/storage-account-failures-01.png)

* **용량이** **용량** 통합 문서를 엽니다. 타일 및 차트의 계정의 각 저장소 데이터 개체에 사용되는 총 저장소 양과 계정에 저장된 데이터 개체 수를 보여 주며, 이 에 사용되는 데이터 개체의 수를 보여 주며,  

    ![선택한 저장소 계정 용량 페이지](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>핀 및 내보내기

섹션 오른쪽 상단에 있는 푸시핀 아이콘을 선택하여 메트릭 섹션 중 하나를 Azure 대시보드에 고정할 수 있습니다.

![대시보드 예제에 대한 메트릭 섹션 핀](./media/storage-insights-overview/workbook-pin-example.png)

다중 구독 및 저장소 계정 **개요** 또는 **용량** 통합 문서는 푸시핀 아이콘 의 오른쪽에 있는 아래쪽 화살표 아이콘을 선택하여 Excel 형식으로 결과를 내보낼 수 있도록 지원합니다.

![통합 문서 표 결과 내보내기 예제](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>저장소용 Azure 모니터 사용자 지정(미리 보기)

이 섹션에서는 데이터 분석 요구 사항을 지원하여 사용자 지정할 통합 문서를 편집하기 위한 일반적인 시나리오를 중선으로 요약합니다.

* 통합 문서의 범위를 조정하여 항상 특정 구독 또는 저장소 계정을 선택합니다.
* 그리드의 메트릭 변경
* 가용성 임계값 변경
* 색상 렌더링 변경

사용자 지정은 게시된 통합 문서의 기본 구성을 덮어쓰지 않도록 사용자 지정 통합 문서에 저장됩니다. 통합 문서는 개인 정보 **섹션또는** 리소스 그룹에 액세스할 수 있는 모든 사용자가 액세스할 수 있는 **공유 보고서** 섹션의 리소스 그룹 내에 저장됩니다. 사용자 지정 통합 문서를 저장한 후에는 통합 문서 갤러리로 이동하여 실행해야 합니다.

![명령 모음에서 통합 문서 갤러리 시작](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>구독 또는 저장소 계정 지정

다중 구독 및 저장소 계정 **개요** 또는 **용량** 통합 문서를 구성하여 모든 실행에서 특정 구독 또는 저장소 계정으로 범위를 조정하고 다음 단계를 수행할 수 있습니다.

1. 포털에서 **모니터를** 선택한 다음 왼쪽 창에서 **저장소 계정(미리 보기)을** 선택합니다.

2. 개요 **통합** 문서에서 명령 모음에서 **편집을**선택합니다.

3. **구독** 드롭다운 목록에서 기본값으로 사용할 구독 중 하나 이상의 구독을 선택합니다. 통합 문서에서는 총 10개의 구독을 선택할 수 있습니다.  

4. **저장소 계정** 드롭다운 목록에서 기본값으로 설정하려는 하나 이상의 계정을 선택합니다. 통합 문서에서는 총 200개의 저장소 계정을 선택할 수 있습니다. 

5. 명령 모음에서 **로 저장을** 선택하여 사용자 지정과 함께 통합 문서의 복사본을 저장한 다음 **편집 완료를** 클릭하여 읽기 모드로 돌아갑니다.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>통합 문서의 메트릭 및 색상 수정

미리 빌드된 통합 문서는 메트릭 데이터를 포함하며 시각화 중 하나를 수정 또는 제거하고 팀의 특정 요구에 맞게 사용자 지정할 수 있습니다.

이 예제에서는 다중 구독 및 저장소 계정 용량 통합 문서와 함께 작업하여 다음 방법을 보여 줍니다.

* 메트릭 제거
* 색상 렌더링 변경

미리 빌드된 **오류,** **성능,** **가용성**및 **용량** 통합 문서 중 하나에 대해 동일한 변경 내용을 수행할 수 있습니다.

1. 포털에서 **모니터를** 선택한 다음 왼쪽 창에서 **저장소 계정(미리 보기)을** 선택합니다.

2. 용량 통합 문서와 명령 모음에서 전환할 **용량을** 선택하고 명령 모음에서 **편집을** 선택합니다.

    ![편집을 선택하여 통합 문서를 수정합니다.](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 메트릭 섹션 옆에서 **편집을**선택합니다.

    ![용량 통합 문서 메트릭을 수정하려면 편집을 선택합니다.](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. 계정에서 사용한 용량 **타임라인** 열을 제거하므로 메트릭 그리드에서 **열 설정을** 선택합니다.

    ![열 설정 편집](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. 열 설정 창 **편집에서** 열 섹션 **microsoft.storage/storage계정-용량-사용** **용량 타임라인$에서 선택합니다.| 계정은 용량 타임라인$을 사용하고**드롭다운 목록 아래에서 **Column 렌더러가** **숨겨진**.

6. 변경 을 커밋하려면 **저장 및 닫기를** 선택합니다.

이제 보고서의 용량 메트릭에 대한 색상 테마를 파란색 대신 녹색으로 사용하도록 변경해 보겠습니다.

1. 메트릭 표에서 **열 설정을** 선택합니다.

2. 편집 **열 설정** 창에서 **열** 섹션 **microsoft.storage/storageaccounts-Capacity-UsedCapacity=microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity=Microsoft.Storage/Storage/storageaccounts/fileservices-Capacity-FileCapacity$microsoft.storage/storageaccounts/storageservices-Capacity-QueueCapacity$를 선택합니다.microsoft.저장소/저장소 계정/테이블 서비스-테이블 용량..** 드롭 다운 목록 **에서 색상 팔레트를** **선택합니다.**

3. 변경 을 커밋하려면 **저장 및 닫기를** 선택합니다.

4. 명령 모음에서 **로 저장을** 선택하여 사용자 지정과 함께 통합 문서의 복사본을 저장한 다음 **편집 완료를** 클릭하여 읽기 모드로 돌아갑니다.  

### <a name="modify-the-availability-threshold"></a>가용성 임계값 수정

이 예제에서는 저장소 계정 용량 통합 문서와 함께 작업 하 고 가용성 임계값을 수정 하는 방법을 보여 주면 됩니다. 기본적으로 타일 및 그리드 보고 백분율 가용성은 최소 임계값 90및 최대 임계값 99로 구성됩니다. API 이름 표에 **의한 가용성** **%의** 최소 임계값을 85%로 변경하여 임계값이 85% 미만이면 상태 상태가 임계값으로 변경됩니다. 

1. 포털에서 **저장소 계정을** 선택한 다음 목록에서 저장소 계정을 선택합니다.

2. 왼쪽 창에서 **인사이트(미리 보기)를** 선택합니다.

3. 통합 문서에서 **가용성을** 선택하여 가용성 통합 문서로 전환한 다음 명령 모음에서 **편집을** 선택합니다. 

4. 페이지 아래쪽으로 스크롤하고 API 그리드의 **가용성** 옆의 왼쪽에서 **편집을**선택합니다.

    ![API 이름 그리드 설정별 가용성 편집](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. **열 설정을** 선택한 다음 열 **편집 설정** 창에서 **열** 섹션 아래에서 **가용성(%) 선택 (임계값 + 서포맷)**.

6. **임계** 상태 값을 **90에서** **85로** 변경한 다음 **저장 및 닫기를**클릭합니다.

    ![중요 상태에 대한 가용성 임계값 수정](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 명령 모음에서 **로 저장을** 선택하여 사용자 지정과 함께 통합 문서의 복사본을 저장한 다음 **편집 완료를** 클릭하여 읽기 모드로 돌아갑니다.

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 Azure Monitor for Storage(미리 보기)를 사용할 때 발생할 수 있는 몇 가지 일반적인 문제의 진단 및 문제 해결에 도움이 됩니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

### <a name="resolving-performance-capacity-or-availability-issues"></a>성능, 용량 또는 가용성 문제 해결

저장소용 Azure 모니터(미리 보기)를 사용하여 식별하는 저장소 관련 문제를 해결하는 데 도움이 되는 경우 Azure Storage [문제 해결 지침을](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)참조하십시오.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>200개의 저장소 계정만 볼 수 있는 이유는 무엇입니까?

선택한 저장소 계정의 수에는 선택한 구독 수에 관계없이 200개로 제한됩니다.

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>대시보드에서 최근에 고정된 타일을 클릭하면 어떻게 되나요?

* 타일의 아무 곳이나 클릭하면 타일이 고정된 탭으로 이동합니다. 예를 들어 "저장소 계정 개요" 탭에 그래프를 고정한 다음 대시보드에서 해당 타일을 클릭하면 해당 기본 보기가 열리지만 저장된 복사본에서 그래프를 고정하면 저장된 복사본보기가 열립니다.
* 제목 왼쪽 상단에 있는 필터 아이콘이 "타일 설정 구성" 탭을 엽니다.
* 오른쪽 상단의 타원 아이콘은 "제목 데이터 사용자 지정", "사용자 지정", "새로 고침" 및 "대시보드에서 제거"하는 옵션을 제공합니다.

### <a name="what-happens-when-i-save-a-workbook"></a>통합 문서를 저장하면 어떻게 되나요?

* 통합 문서를 저장하면 편집한 통합 문서의 새 복사본을 만들고 제목을 변경할 수 있습니다. 저장은 통합 문서를 덮어쓰지 않으며 현재 통합 문서는 항상 기본 보기가 됩니다.
* **저장되지 않은** 통합 문서만 기본 보기입니다.


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>포털에 모든 구독이 표시되지 않는 이유는 무엇입니까?

포털은 포털 시작 시 선택한 구독에 대한 데이터만 표시합니다. 선택한 구독을 변경하려면 오른쪽 상단으로 이동하여 필터 아이콘이 있는 전자 필기장을 클릭합니다. 디렉토리 + 구독 탭이 표시됩니다.

![디렉터리 + 구독](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>가용성에 대한 색상 및 임계값을 변경하는 방법은 무엇입니까?

[가용성에](storage-insights-overview.md#modify-the-availability-threshold) 대한 색상 및 임계값을 변경하는 방법에 대한 자세한 단계는 가용성 임계값 수정 섹션을 참조하십시오.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>저장소용 Azure 모니터에 표시된 데이터를 분석하고 해결하는 방법은 무엇입니까?

 저장소용 Azure 모니터에 표시된 Azure 저장소 데이터를 분석하고 해결하는 방법에 대한 자세한 내용은 Microsoft Azure Storage 문서를 [모니터, 진단 및 해결을](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting) 참조하십시오.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>메트릭에 모든 유형의 오류가 표시되지 않는 이유는 무엇입니까?

현재 최대 3가지 유형의 오류가 표시되고 나머지 오류는 단일 버킷에 함께 그룹화됩니다. splitByLimit를 사용하여 제어되며 수정할 수 있습니다. 이 속성을 변경하는 경우:

1. 통합 문서 편집을 클릭합니다.
2. 측정항목으로 이동하여 편집을 클릭한 다음 **거래, 합계** 또는 편집하려는 측정항목을 선택합니다.

    ![측정항목으로 이동하여 "거래, 합계"에서 편집을 클릭합니다.](./media/storage-insights-overview/fqa7.png)

1. 그런 다음 분할 수를 변경합니다.

    ![메트릭 매개변수 선택"](./media/storage-insights-overview/fqa7-2.png)

splitByLimit를 n+1로 지정하는 것보다 다른 유형의 오류를 보려면 나머지 오류에 대해 1개의 추가 오류가 표시됩니다.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>일부 저장소 계정에 있는 동안 통합 문서를 저장했습니다. 왜 지금 찾을 수 없습니다?

각 통합 문서저장 계정에 저장됩니다. 사용자가 통합 문서를 저장한 특정 저장소 계정을 찾아보십시오. 그렇지 않으면 리소스(저장소 계정)를 알지 않고 특정 통합 문서를 찾을 수 있는 방법이 없습니다.

### <a name="what-is-time-range"></a>시간 범위는 무엇입니까?

시간 범위는 특정 시간 프레임의 데이터를 표시합니다. 예를 들어 시간 범위가 24시간인 경우 지난 24시간의 데이터가 표시됩니다.

### <a name="what-is-time-granularity-time-grain"></a>시간 세분성 (시간 그레인)은 무엇입니까?

시간 세분성은 두 데이터 점 간의 시간 차이입니다. 예를 들어 시간 그레인이 1초로 설정된 경우 이는 메트릭이 매 초마다 수집된다는 것을 의미합니다.

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>통합 문서의 일부를 대시보드에 고정하면 시간 분분성은 얼마입니까?

기본 시간 세분성이 자동으로 설정되어 있으며 현재 현재 변경할 수 없습니다.

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>대시보드에서 통합 문서 단계의 시간 범위/시간 범위를 변경하려면 어떻게 해야 합니까?

기본적으로 대시보드 타일의 시간 범위/시간 범위가 24시간으로 설정되어 오른쪽 상단의 타원에 대한 이 클릭을 변경하려면 **타일 데이터 사용자 지정을**선택하고 "제목 수준에서 대시보드 시간 설정을 재정의" 상자를 선택한 다음 드롭다운 메뉴를 사용하여 시간 범위를 선택합니다.  

![타일의 오른쪽 모서리에 있는 타원을 선택하고 이 데이터 사용자 지정을 선택합니다.](./media/storage-insights-overview/fqa-data-settings.png)

![타일 구성 설정에서 시간 범위/시간 범위를 변경하는 시간 범위 드롭다운을 선택합니다.](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>대시보드에 고정한 통합 문서 또는 통합 문서 단계의 제목을 변경하려면 어떻게 해야 합니까?

대시보드에 고정된 통합 문서 또는 통합 문서 단계의 제목은 통합 문서의 이름과 동일한 이름을 유지합니다. 제목을 변경하려면 통합 문서의 복사본을 직접 저장해야 합니다. 그런 다음 저장을 누르기 전에 통합 문서의 이름을 지정할 수 있습니다.

![통합 문서의 복사본을 저장하고 이름을 변경하려면 상단에 저장을 선택합니다.](./media/storage-insights-overview/fqa-change-workbook-name.png)

저장된 통합 문서의 단계 이름을 변경하려면 단계 아래에 편집한 다음 설정 맨 아래에 있는 장비를 선택합니다.

![통합 문서 단계 의 하단에서 편집을 선택하여](./media/storage-insights-overview/fqa-edit.png)
![설정을 열면 설정에서 하단의 기어를 선택하여 단계 이름을 변경할 수 있습니다.](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../platform/alerts-metric.md) 및 서비스 [상태 알림을](../../service-health/alerts-activity-log-service-notifications.md) 구성하여 문제 감지에 도움이 될 자동 경고를 설정합니다.

* Azure Monitor 통합 문서를 사용하여 [대화형 보고서 만들기를](../app/usage-workbooks.md)검토하여 통합 문서를 지원하도록 설계된 시나리오, 새 보고서를 작성하고 사용자 지정하는 방법 등을 알아봅니다.

* 스토리지 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.
