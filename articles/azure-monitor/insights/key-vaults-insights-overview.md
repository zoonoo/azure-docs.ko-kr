---
title: Key Vault에 대 한 Azure Monitor를 사용 하 여 Key Vault 모니터링 (미리 보기) | Microsoft Docs
description: 이 문서에서는 키 자격 증명 모음에 대 한 Azure Monitor 설명 합니다.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 2387f14a537c15c891bff968573bf4d698c01770
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211304"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Key Vault에 대 한 Azure Monitor를 사용 하 여 주요 자격 증명 모음 서비스 모니터링 (미리 보기)
Key Vault (미리 보기)에 대 한 Azure Monitor는 Key Vault 요청, 성능, 실패 및 대기 시간에 대 한 통합 보기를 제공 하 여 주요 자격 증명 모음에 대 한 종합적인 모니터링을 제공 합니다.
이 문서는 Key Vault (미리 보기)에 대 한 Azure Monitor 환경을 등록 하 고 사용자 지정 하는 방법을 이해 하는 데 도움이 됩니다.

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Key Vault에 대 한 Azure Monitor 소개 (미리 보기)

환경으로 이동 하기 전에 정보를 표시 하 고 시각화 하는 방법을 이해 해야 합니다.
-    **규모 관점에서** 요청, 오류 분석 및 작업 및 대기 시간에 대 한 개요를 기준으로 성능에 대 한 스냅숏 보기를 보여 줍니다.
-   상세 분석을 수행 하려면 특정 key vault에 대 한 **분석을 드릴 다운** 합니다.
-    **사용자 지정 가능** -표시 하려는 메트릭을 변경 하 고, 제한과 일치 하는 임계값을 수정 하거나 설정 하 고, 사용자 지정 통합 문서를 저장할 수 있습니다. 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.

Key Vault에 대 한 Azure Monitor는 로그와 메트릭을 결합 하 여 글로벌 모니터링 솔루션을 제공 합니다. 모든 사용자가 메트릭 기반 모니터링 데이터에 액세스할 수 있지만 로그 기반 시각화를 포함 하는 경우 사용자가 [Azure Key Vault 로깅을 사용 하도록 설정](https://docs.microsoft.com/azure/key-vault/key-vault-logging)해야 할 수 있습니다.

## <a name="configuring-your-key-vaults-for-monitoring"></a>모니터링할 키 자격 증명 모음 구성

> [!NOTE]
> 로그를 사용 하도록 설정 하는 것은 추가 모니터링 기능을 제공 하는 유료 서비스입니다.

1. 작업 & 대기 시간 탭은 사용할 수 있는 키 자격 증명 모음 및 수를 결정 하는 데 도움이 됩니다. 수집을 시작 하려면 진단 로그를 사용 하도록 설정 해야 하는 주요 자격 증명 모음을 나열 하는 별도의 통합 문서를 가져오는 **사용** 단추를 선택 합니다.

    ![파란색 사용 단추가 표시 된 작업 및 대기 시간 탭의 스크린샷](./media/key-vaults-insights-overview/enable-logging.png)

2. 진단 로그를 사용 하도록 설정 하려면 작업 열 아래의 **사용** 링크를 클릭 하 고 로그를 Log Analytics 작업 영역으로 보내는 새 진단 설정을 만듭니다. 모든 로그를 동일한 작업 영역으로 전송 하는 것이 좋습니다.

3. 진단 설정이 저장 되 면 Key Vault Insights 아래의 모든 로그 기반 차트 및 시각화를 볼 수 있습니다. 로그 채우기를 시작 하는 데 몇 분 정도 걸릴 수 있습니다.

4. Key Vault 서비스에 대 한 진단 로그를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [전체 가이드](https://docs.microsoft.com/azure/key-vault/key-vault-logging)를 참조 하세요.

## <a name="view-from-azure-monitor"></a>Azure Monitor에서 보기

Azure Monitor에서 구독의 여러 주요 자격 증명 모음에서 요청, 대기 시간 및 실패 정보를 볼 수 있으며 성능 문제 및 제한 시나리오를 식별할 수 있습니다.

모든 구독에서 저장소 계정의 사용률 및 작업을 보려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal의 왼쪽 창에서 **모니터** 를 선택 하 고, Insights 섹션 아래에서 **Key vault (미리 보기)** 를 선택 합니다.

![여러 그래프를 사용 하는 개요 환경 스크린샷](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>개요 통합 문서

선택한 구독에 대 한 개요 통합 문서에서 테이블은 구독 내에 그룹화 된 key vault에 대 한 interactive key vault 메트릭을 표시 합니다. 다음 드롭다운 목록에서 선택한 옵션을 기준으로 결과를 필터링 할 수 있습니다.

* 구독-키 자격 증명 모음이 있는 구독만 나열 됩니다.

* 주요 자격 증명 모음 – 기본적으로 최대 5 개의 key vault만 미리 선택 됩니다. 범위 선택기에서 전체 또는 여러 키 자격 증명 모음을 선택 하면 최대 200 개의 key vault가 반환 됩니다. 예를 들어 선택한 3 개의 구독에 총 573 개의 key vault가 있는 경우 200 자격 증명 모음만 표시 됩니다.

* 시간 범위 – 기본적으로는 선택한 항목을 기준으로 지난 24 시간 동안의 정보를 표시 합니다.

카운터 타일은 드롭다운 목록 아래에서 선택한 구독에 있는 주요 자격 증명 모음의 총 수를 롤업 하 고 선택 된 항목 수를 반영 합니다. 요청, 오류 및 대기 시간 메트릭을 보고 하는 통합 문서의 열에 대 한 조건부 색으로 구분 된 열 지도가 있습니다. 가장 높은 색은 가장 높은 값을 가지 며 가장 밝은 색은 가장 작은 값을 기준으로 합니다.

## <a name="failures-workbook"></a>실패 통합 문서

페이지 맨 위에서 **실패** 를 선택 하면 오류 탭이 열립니다. 특정 응답 코드의 양과 함께 API 적중 횟수, 시간에 따른 빈도를 표시 합니다.

![실패 통합 문서의 스크린샷](./media/key-vaults-insights-overview/failures.png)

API 적중 메트릭을 파란색 값으로 보고 하는 통합 문서의 열에 대 한 조건부 색 구분 또는 열 지도이 있습니다. 가장 높은 색은 가장 높은 값을 가지 며 가장 밝은 색은 가장 작은 값을 기준으로 합니다.

이 통합 문서에는 성공 (2xx 상태 코드), 인증 오류 (401/403 상태 코드), 제한 (429 상태 코드) 및 기타 실패 (4xx 상태 코드)가 표시 됩니다.

각 상태 코드가 나타내는 사항을 더 잘 이해 하려면 [Azure Key Vault 상태 및 응답 코드](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses)에 대 한 설명서를 참조 하는 것이 좋습니다.

## <a name="operations--latency-workbook"></a>작업 & 대기 시간 통합 문서

페이지 맨 위에서 **작업 & 대기 시간** 을 선택 하면 **작업 & 대기 시간** 탭이 열립니다. 이 탭에서 모니터링을 위해 키 자격 증명 모음을 등록할 수 있습니다. 자세한 단계는 [모니터링을 위한 키 자격 증명 모음 구성](#configuring-your-key-vaults-for-monitoring) 섹션을 참조 하세요.

로깅에 대해 사용 하도록 설정 된 주요 자격 증명 모음의 수를 확인할 수 있습니다. 하나 이상의 자격 증명 모음이 올바르게 구성 된 경우 각 키 자격 증명 모음에 대 한 작업 및 상태 코드를 표시 하는 테이블을 볼 수 있습니다. 행에 대 한 세부 정보 섹션을 클릭 하 여 개별 작업에 대 한 추가 정보를 가져올 수 있습니다.

![작업 및 대기 시간 차트 스크린샷](./media/key-vaults-insights-overview/logs.png)

이 섹션에 대 한 데이터가 표시 되지 않는 경우 Azure Key Vault에 대해 로그를 사용 하도록 설정 하는 방법의 맨 위 섹션을 참조 하거나 아래의 문제 해결 섹션을 참조 하세요.

## <a name="view-from-a-key-vault-resource"></a>Key Vault 리소스에서 보기

키 자격 증명 모음에서 직접 Key Vault에 대 한 Azure Monitor 액세스 하려면:

1. Azure Portal에서 키 자격 증명 모음을 선택 합니다.

2. 목록에서 키 자격 증명 모음을 선택 합니다. 모니터링 섹션에서 Insights (미리 보기)를 선택 합니다.

이러한 보기는 Azure Monitor 수준 통합 문서에서 키 자격 증명 모음의 리소스 이름을 선택 하 여 액세스할 수도 있습니다.

![주요 자격 증명 모음 리소스에서 보기의 스크린샷](./media/key-vaults-insights-overview/key-vault-resource-view.png)

주요 자격 증명 모음에 대 한 **개요** 통합 문서에서 다음과 같이 신속 하 게 평가 하는 데 도움이 되는 몇 가지 성능 메트릭을 보여 줍니다.

- 주요 자격 증명 모음 트랜잭션, 대기 시간 및 가용성과 관련 된 가장 중요 한 정보를 보여 주는 대화형 성능 차트

- 메트릭 및 상태 타일에는 서비스 가용성, 주요 자격 증명 모음 리소스에 대 한 총 트랜잭션 수 및 전체 대기 시간이 강조 표시 됩니다.

**실패** 또는 **작업** 에 대해 다른 탭을 선택 하면 해당 통합 문서가 열립니다.

![실패 뷰의 스크린샷](./media/key-vaults-insights-overview/resource-failures.png)

오류 통합 문서는 선택한 시간 프레임의 모든 key vault 요청 결과를 분석 하 고 성공 (2xx), 인증 오류 (401/403), 제한 (429) 및 기타 오류에 대 한 분류를 제공 합니다.

![작업 보기의 스크린샷](./media/key-vaults-insights-overview/operations.png)

작업 통합 문서를 통해 사용자는 최상위 타일을 사용 하 여 결과 상태별로 필터링 할 수 있는 모든 트랜잭션의 전체 세부 정보를 심층적으로 파악할 수 있습니다.

![작업 보기의 스크린샷](./media/key-vaults-insights-overview/info.png)

또한 사용자는 위쪽 테이블의 특정 트랜잭션 유형에 따라 보기 범위를 지정할 수 있습니다. 그러면 아래쪽 테이블을 동적으로 업데이트 하 여 팝업 컨텍스트 창에서 전체 작업 세부 정보를 볼 수 있습니다.

>[!NOTE]
> 사용자가이 통합 문서를 보려면 진단 설정을 사용 하도록 설정 해야 합니다. 진단 설정 사용에 대 한 자세한 내용은 [Azure Key Vault 로깅](https://docs.microsoft.com/azure/key-vault/general/logging)에 대해 자세히 알아보세요.

## <a name="pin-and-export"></a>고정 및 내보내기

섹션의 오른쪽 위에 있는 압정 아이콘을 선택 하 여 메트릭 섹션 중 하나를 Azure 대시보드에 고정할 수 있습니다.

다중 구독 및 주요 자격 증명 모음 개요 또는 오류 통합 문서는 압정 아이콘 왼쪽에 있는 다운로드 아이콘을 선택 하 여 Excel 형식으로 결과 내보내기를 지원 합니다.

![선택 된 pin 아이콘의 스크린샷](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Key Vault에 대 한 Azure Monitor 사용자 지정

이 섹션에서는 데이터 분석 요구 사항을 지원 하기 위해 통합 문서를 편집 하는 일반적인 시나리오를 중점적으로 설명 합니다.
*  항상 특정 구독 또는 주요 자격 증명 모음을 선택 하도록 통합 문서 범위를 선택 합니다.
* 표에서 메트릭 변경
* 요청 임계값 변경
* 색 렌더링 변경

위쪽 도구 모음에서 **사용자 지정** 단추를 선택 하 여 편집 모드를 사용 하도록 설정 하 여 사용자 지정을 시작할 수 있습니다.

![사용자 지정 단추의 스크린샷](./media/key-vaults-insights-overview/customize.png)

사용자 지정은 게시 된 통합 문서에서 기본 구성을 덮어쓰지 않도록 사용자 지정 통합 문서에 저장 됩니다. 통합 문서는 사용자에 게 개인 또는 리소스 그룹에 액세스할 수 있는 모든 사용자가 액세스할 수 있는 공유 보고서 섹션에서 비공개 인 내 보고서 섹션에 있는 리소스 그룹 내에 저장 됩니다. 사용자 지정 통합 문서를 저장 한 후에는 통합 문서 갤러리로 이동 하 여 시작 해야 합니다.

![통합 문서 갤러리의 스크린샷](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>구독 또는 키 자격 증명 모음 지정

다음 단계를 수행 하 여 모든 실행에서 특정 구독 또는 주요 자격 증명 모음에 대 한 범위를 지정할 수 있도록 다중 구독 및 주요 자격 증명 모음 개요 또는 실패 통합 문서를 구성할 수 있습니다.

1. 포털에서 **모니터** 를 선택 하 고 왼쪽 창에서 **키 자격 증명 모음 (미리 보기)** 을 선택 합니다.
2. **개요** 통합 문서의 명령 모음에서 **편집**을 선택 합니다.
3. **구독** 드롭다운 목록에서 선택 합니다. 원하는 하나 이상의 구독을 기본값으로 사용 합니다. 통합 문서에서는 총 10 개의 구독을 선택할 수 있습니다.
4. **키 자격 증명 모음** 드롭다운 목록에서 기본으로 사용 하려는 계정을 하나 이상 선택 합니다. 통합 문서에서는 총 200 개의 저장소 계정을 선택 하도록 지원 합니다.
5. 명령 모음에서 다른 **이름으로 저장** 을 선택 하 여 사용자 지정 항목이 포함 된 통합 문서의 복사본을 저장 한 다음 **편집 완료** 를 클릭 하 여 읽기 모드로 돌아갑니다.

## <a name="troubleshooting"></a>문제 해결

이 섹션은 Key Vault (미리 보기)에 대 한 Azure Monitor를 사용할 때 발생할 수 있는 일반적인 문제 중 일부에 대 한 진단 및 문제 해결에 도움이 됩니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

### <a name="resolving-performance-issues-or-failures"></a>성능 문제 또는 오류 해결

Key Vault (미리 보기)에 대 한 Azure Monitor에서 식별 하는 주요 자격 증명 모음 관련 문제를 해결 하려면 [Azure Key Vault 설명서](https://docs.microsoft.com/azure/key-vault/)를 참조 하세요.

### <a name="why-can-i-only-see-200-key-vaults"></a>200 키 자격 증명 모음만 볼 수 있는 이유는 무엇 인가요?

선택 하 고 볼 수 있는 키 자격 증명 모음 수는 200 개로 제한 됩니다. 선택한 구독 수에 관계 없이 선택한 키 자격 증명 모음의 수는 200 개로 제한 됩니다.

### <a name="what-will-happen-when-a-pinned-item-is-clicked"></a>고정 된 항목을 클릭 하면 어떤 일이 발생 하나요?

대시보드의 고정 된 항목을 클릭 하면 다음 두 가지 중 하나가 열립니다.
* 정보가 저장 된 경우에는 pin이 저장 된 정보 인스턴스를 엽니다.
* 정보가 저장 되지 않은 경우 새 기본 정보 인스턴스를 엽니다.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>구독 선택에서 내 구독이 모두 표시 되지 않는 이유는 무엇 인가요?

키 자격 증명 모음을 포함 하는 구독을 표시 합니다. 선택 된 구독 필터는 Azure Portal 헤더의 "디렉터리 + 구독"에서 선택 합니다.

![구독 필터 스크린샷](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>"쿼리가 최대 작업 영역/영역을 초과 했습니다." 라는 오류 메시지를 받고 있습니다.

현재, 25 개 지역 및 200 작업 영역에 대 한 제한이 있으므로 데이터를 보려면 구독 및/또는 리소스 그룹의 수를 줄여야 합니다.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Key Vault 정보를 변경 하거나 시각화를 추가 하려고 합니다. 어떻게 해야 하나요?

변경 하려면 "편집 모드"를 선택 하 여 통합 문서를 수정 하 고 지정 된 구독 및 리소스 그룹에 연결 된 새 통합 문서로 작업을 저장할 수 있습니다.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>통합 문서의 모든 부분을 고정 한 후의 시간 수준은 무엇 인가요?

"자동" 시간 수준을 활용 하므로 선택 된 시간 범위에 따라 달라 집니다.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>통합 문서 일부가 고정 된 시간 범위는 어떻게 되나요?

시간 범위는 대시보드 설정에 따라 달라 집니다.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>작업 & 대기 시간 섹션에서 내 Key Vault에 대 한 데이터가 표시 되지 않는 이유는 무엇 인가요?

로그 기반 데이터를 보려면 모니터링 하려는 각 주요 자격 증명 모음에 대 한 로그를 사용 하도록 설정 해야 합니다. 각 키 자격 증명 모음에 대 한 진단 설정에서 수행할 수 있습니다. 지정 된 Log Analytics 작업 영역으로 데이터를 전송 해야 합니다.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>내 Key Vault에 대 한 로그를 이미 사용 하도록 설정 했습니다 .이는 여전히 작업 & 대기 시간에 내 데이터를 볼 수 없는 이유는 무엇입니까?

현재 진단 로그는 소급 작동 하지 않으므로 키 자격 증명 모음에 대 한 작업이 수행 된 후에만 데이터가 표시 되기 시작 합니다. 따라서 키 자격 증명 모음을 활성화 하는 방법에 따라 몇 시간에서 하루까지 시간이 걸릴 수 있습니다.

또한 많은 수의 키 자격 증명 모음 및 구독을 선택한 경우 쿼리 제한 때문에 데이터를 볼 수 없습니다. 데이터를 보려면 선택한 구독 또는 키 자격 증명 모음 수를 줄여야 할 수 있습니다. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>다른 데이터를 보거나 사용자의 시각화를 수행 하려면 어떻게 해야 하나요? Key Vault Insights를 변경 하려면 어떻게 해야 하나요?

편집 모드를 사용 하 여 기존 통합 문서를 편집한 다음 새 변경 내용을 모두 포함 하는 새 통합 문서로 작업을 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

통합 문서가 지원 되도록 설계 된 시나리오, 새 보고서를 작성 하 고 기존 보고서를 사용자 지정 하는 방법 등에 대해 알아보고 [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서 만들기](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)를 검토 하세요.
