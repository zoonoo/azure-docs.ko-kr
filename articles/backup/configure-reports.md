---
title: Azure Backup 보고서 구성
description: 로그 분석 및 Azure 통합 문서를 사용하여 Azure 백업에 대한 보고서 구성 및 보기
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcc87deb19190149329369ca58f54b45b62b41fe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617813"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup 보고서 구성

백업 관리자의 일반적인 요구 사항은 장기간에 걸친 데이터를 기반으로 백업에 대한 통찰력을 얻는 것입니다. 이러한 솔루션에 대한 사용 사례는 다음과 같습니다.

 - 사용되는 클라우드 스토리지의 할당 및 예측.
 - 백업 및 복원 감사.
 - 다양한 세분성 수준에서 주요 추세를 식별합니다.

현재 Azure Backup은 [Azure Monitor 로그](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) 및 [Azure 통합 문서를](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)사용하는 보고 솔루션을 제공합니다. 이러한 리소스를 사용하면 전체 백업 부동산에서 백업에 대한 풍부한 통찰력을 얻을 수 있습니다. 이 문서에서는 Azure 백업 보고서를 구성하고 보는 방법을 설명합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 백업 보고서는 Azure VM, Azure VM의 SQL, Azure VM의 SAP HANA/ASE, Microsoft Azure 복구 서비스(MARS) 에이전트, MABS(Microsoft Azure 백업 서버) 및 시스템 센터 데이터 보호 관리자(DPM)에 대해 지원됩니다.
* DPM 워크로드의 경우 백업 보고서는 DPM 버전 5.1.363.0 이상 및 에이전트 버전 2.0.9127.0 이상에서 지원됩니다.
* MABS 워크로드의 경우 백업 보고서는 MABS 버전 13.0.415.0 이상 및 에이전트 버전 2.0.9170.0 이상에서 지원됩니다.
* 백업 보고서는 데이터가 사용자가 액세스할 수 있는 Log Analytics 작업 영역으로 전송되는 한 모든 백업 항목, 자격 증명 모음, 구독 및 지역에서 볼 수 있습니다. 볼트 집합에 대한 보고서를 보려면 볼트가 데이터를 보내는 Log Analytics 작업 영역에 대한 판독기 액세스 권한만 있으면 됩니다. 개별 볼트에 액세스할 필요가 없습니다.
* 고객의 구독에 대한 위임된 액세스 권한이 있는 [Azure 등대](https://docs.microsoft.com/azure/lighthouse/) 사용자인 경우 이러한 보고서를 Azure 등대와 함께 사용하여 모든 테넌트의 보고서를 볼 수 있습니다.
* 로그 백업 작업에 대한 데이터는 현재 보고서에 표시되지 않습니다.

## <a name="get-started"></a>시작하기

다음 단계에 따라 보고서 사용을 시작합니다.

#### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. 로그 분석 작업 영역을 만들거나 기존 작업 영역을 사용합니다.

하나 이상의 Log Analytics 작업 영역을 설정하여 백업 보고 데이터를 저장합니다. 이 Log Analytics 작업 영역을 만들 수 있는 위치 및 구독은 볼트가 있는 위치 및 구독과 는 독립적입니다. 

로그 분석 작업 영역을 설정하려면 [Azure 포털에서 로그 분석 작업 영역 만들기를](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)참조하십시오.

기본적으로 Log Analytics 작업 영역의 데이터는 30일 동안 유지됩니다. 더 긴 시간 동안의 데이터를 보려면 Log Analytics 작업 영역의 보존 기간을 변경합니다. 보존 기간을 변경하려면 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)를 참조하십시오.

#### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. 볼트에 대한 진단 설정 구성

복구 서비스 자격 증명 모음과 같은 Azure Resource Manager 리소스는 예약된 작업 및 사용자가 트리거한 작업에 대한 정보를 진단 데이터로 기록합니다. 

복구 서비스 자격 증명 모음의 모니터링 섹션에서 **진단 설정을** 선택하고 복구 서비스 자격 증명 모음의 진단 데이터에 대한 대상을 지정합니다. 진단 이벤트 사용에 대한 자세한 내용은 [복구 서비스 자격 증명 모음에 대한 진단 사용 설정을](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)참조하십시오.

![진단 설정 창](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

또한 Azure Backup은 지정된 범위의 모든 볼트에 대한 진단 설정 구성을 자동화하는 기본 제공 Azure 정책을 제공합니다. 이 정책을 사용하는 방법을 알아보려면 [규모에 따라 볼트 진단 설정 구성을](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)참조하십시오.

> [!NOTE]
> 진단을 구성한 후 초기 데이터 푸시가 완료되는 데 최대 24시간이 걸릴 수 있습니다. 데이터가 Log Analytics 작업 영역으로 유입을 시작하면 현재 부분 일의 데이터가 보고서에 표시되지 않으므로 보고서에 데이터가 즉시 표시되지 않을 수 있습니다. 자세한 내용은 [백업 보고서에 사용되는 규칙을](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)참조하십시오. Log Analytics로 데이터를 전송하도록 볼트를 구성한 후 2일 후에 보고서를 보는 것이 좋습니다.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Azure 포털에서 보고서 보기

로그 애널리틱스로 데이터를 보내도록 볼트를 구성한 후 자격 증명 모음의 창으로 이동하여 **백업 보고서를**선택하여 백업 보고서를 봅니다.

![자격 증명 모음 대시보드](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

백업 보고서 통합 문서를 열려면 이 링크를 선택합니다.

> [!NOTE]
> * 현재 보고서의 초기 로드는 최대 1분 이걸릴 수 있습니다.
> * 복구 서비스 자격 증명 모음은 백업 보고서의 진입점일 뿐입니다. 백업 보고서 통합 문서를 볼트 창에서 열리면 적절한 Log Analytics 작업 영역 집합을 선택하여 모든 볼트에서 집계된 데이터를 확인합니다.

보고서에는 다음과 같은 다양한 탭이 포함되어 있습니다.

* **요약**: 백업 부동산의 높은 수준의 개요를 얻기 위해이 탭을 사용합니다. 총 백업 항목 수, 소비된 총 클라우드 저장소 수, 보호된 인스턴스 수 및 워크로드 유형별 작업 성공률을 간략히 파악할 수 있습니다. 특정 백업 아티팩트 유형에 대한 자세한 내용은 해당 탭으로 이동하십시오.

   ![요약 탭](./media/backup-azure-configure-backup-reports/summary.png)

* **백업 항목**: 이 탭을 사용하여 백업 항목 수준에서 사용되는 클라우드 저장소에 대한 정보와 추세를 확인합니다. 예를 들어 Azure VM 백업에서 SQL을 사용하는 경우 백업되는 각 SQL 데이터베이스에 대해 사용되는 클라우드 저장소를 볼 수 있습니다. 특정 보호 상태의 백업 항목에 대한 데이터를 보도록 선택할 수도 있습니다. 예를 들어 탭 상단의 **보호 중지** 타일을 선택하면 아래의 모든 위젯이 필터링되어 보호 중지 상태의 백업 항목에 대한 데이터만 표시됩니다.

   ![백업 항목 탭](./media/backup-azure-configure-backup-reports/backup-items.png)

* **용도**: 백업에 대한 주요 청구 매개 변수를 보려면 이 탭을 사용합니다. 이 탭에 표시된 정보는 청구 엔터티(보호된 컨테이너) 수준에 있습니다. 예를 들어 Azure에 백업되는 DPM 서버의 경우 DPM 서버에 대해 사용되는 보호된 인스턴스 및 클라우드 저장소의 추세를 볼 수 있습니다. 마찬가지로 Azure 백업또는 Azure Backup에서 SAP HANA에서 SQL을 사용하는 경우 이 탭은 이러한 데이터베이스가 포함된 가상 컴퓨터 수준에서 사용 관련 정보를 제공합니다.

   ![사용 탭](./media/backup-azure-configure-backup-reports/usage.png)

* **작업**: 이 탭을 사용하여 하루에 실패한 작업 수 및 작업 실패의 주요 원인과 같은 작업에 대한 장기 실행 추세를 볼 수 있습니다. 집계 수준과 백업 항목 수준에서 이 정보를 볼 수 있습니다. 그리드에서 특정 백업 항목을 선택하여 선택한 시간 범위의 해당 백업 항목에서 트리거된 각 작업에 대한 자세한 정보를 봅니다.

   ![작업 탭](./media/backup-azure-configure-backup-reports/jobs.png)

* **정책**: 이 탭을 사용하여 지정된 정책에 따라 백업된 항목에 의해 사용되는 연결된 항목 수 및 총 클라우드 저장소와 같은 모든 활성 정책에 대한 정보를 볼 수 있습니다. 특정 정책을 선택하여 연결된 각 백업 항목에 대한 정보를 봅니다.

   ![정책 탭](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Excel로 내보내기

테이블이나 차트와 같은 위젯의 오른쪽 상단에 있는 아래쪽 화살표 단추를 선택하여 해당 위젯의 내용을 기존 필터가 적용된 것처럼 Excel 시트로 내보냅니다. 테이블의 행을 Excel로 내보내려면 각 그리드 맨 위에 있는 **페이지당 행** 드롭다운 화살표를 사용하여 페이지에 표시되는 행 수를 늘릴 수 있습니다.

## <a name="pin-to-dashboard"></a>대시보드에 고정

각 위젯 상단에 있는 핀 버튼을 선택하여 위젯을 Azure 포털 대시보드에 고정합니다. 이 기능을 사용하면 필요한 가장 중요한 정보를 표시하도록 맞춤화된 대시보드를 만들 수 있습니다.

## <a name="cross-tenant-reports"></a>테넌트 간 보고서

여러 테넌트 환경에서 구독에 대한 위임된 액세스 권한이 있는 [Azure 등대를](https://docs.microsoft.com/azure/lighthouse/) 사용하는 경우 기본 구독 필터를 사용할 수 있습니다. Azure 포털의 오른쪽 상단에 있는 필터 단추를 선택하여 데이터를 보려는 모든 구독을 선택합니다. 이렇게 하면 테넌트 전체에서 Log Analytics 작업 영역을 선택하여 다중 테넌트 보고서를 볼 수 있습니다.

## <a name="conventions-used-in-backup-reports"></a>백업 보고서에 사용되는 규칙

* 필터는 각 탭에서 왼쪽에서 오른쪽으로, 위에서 아래로 작동합니다. 즉, 모든 필터는 해당 필터의 오른쪽 또는 해당 필터 아래에 배치된 모든 위젯에만 적용됩니다. 
* 컬러 타일을 선택하면 해당 타일의 값과 관련된 레코드에 대해 타일 아래의 위젯이 필터링됩니다. 예를 들어 **백업 항목** 탭에서 **보호 중지** 타일옵션을 선택하면 아래 표와 차트가 필터링되어 보호 중지 상태의 백업 항목에 대한 데이터가 표시됩니다.
* 색상이 지정되지 않은 타일은 클릭할 수 없습니다.
* 현재 부분 일의 데이터는 보고서에 표시되지 않습니다. 따라서 **선택한 시간 범위** 값이 **마지막 7일인**경우 보고서에 완료된 마지막 7일의 레코드가 표시됩니다. 현재 일은 포함되지 않습니다.
* 이 보고서에는 선택한 시간 범위에서 *트리거된* 작업(로그 작업 제외)에 대한 세부 정보가 표시됩니다. 
* **클라우드 저장소** 및 **보호된 인스턴스에** 대해 표시된 값은 선택한 시간 범위의 *끝에* 있습니다.
* 보고서에 표시되는 백업 항목은 선택한 시간 범위의 *끝에* 있는 항목입니다. 선택한 시간 범위 중간에 삭제된 백업 항목은 표시되지 않습니다. 백업 정책에도 동일한 규칙이 적용됩니다.

## <a name="query-load-times"></a>쿼리 로드 시간

백업 보고서의 위젯은 사용자의 로그 분석 작업 공간에서 실행되는 Kusto 쿼리에 의해 구동됩니다. 이러한 쿼리에는 일반적으로 더 풍부한 통찰력을 구현하기 위해 여러 조인과 함께 많은 양의 데이터를 처리하는 것이 포함됩니다. 따라서 사용자가 대규모 백업 부동산에서 보고서를 볼 때 위젯이 즉시 로드되지 않을 수 있습니다. 이 표는 백업 항목 수와 보고서를 보는 시간 범위에 따라 다른 위젯이 로드하는 데 걸리는 시간을 대략적인 추정치로 제공합니다.

| **# 데이터 원본**                         | **시간 지평선** | **대략적인 로드 시간**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1개월          | 타일: 5-10초 <br> 그리드: 5-10초 <br> 차트: 5-10초 <br> 보고서 수준 필터: 5-10초|
| ~5 K                       | 3개월          | 타일: 5-10초 <br> 그리드: 5-10초 <br> 차트: 5-10초 <br> 보고서 수준 필터: 5-10초|
| ~10 K                       | 3개월          | 타일: 15-20초 <br> 그리드: 15-20초 <br> 차트: 1-2분 <br> 보고서 수준 필터: 25-30초|
| ~15 K                       | 1개월          | 타일: 15-20초 <br> 그리드: 15-20초 <br> 차트: 50-60초 <br> 보고서 수준 필터: 20-25초|
| ~15 K                       | 3개월          | 타일: 20-30초 <br> 그리드: 20-30초 <br> 차트: 2-3분 <br> 보고서 수준 필터: 50-60초 |

## <a name="what-happened-to-the-power-bi-reports"></a> Power BI 보고서는 어떻게 되었나요? 
* Azure 저장소 계정에서 데이터를 원본화한 보고를 위한 이전 Power BI 템플릿 앱이 사용 중단 경로에 있습니다. 보고서를 보려면 Log Analytics로 볼트 진단 데이터를 보내는 것이 좋습니다.

* 또한 진단 데이터를 저장소 계정 또는 Log Analytics 작업 영역으로 보내는 V1 스키마도 사용 중단 경로에 있습니다. V1 스키마를 기반으로 사용자 지정 쿼리 또는 자동화를 작성한 경우 현재 지원되는 V2 스키마를 사용하도록 이러한 쿼리를 업데이트하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
[Azure 백업을 통해 모니터링 및 보고에 대해 자세히 알아보기](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
