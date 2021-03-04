---
title: Azure Security Center의 통합 문서 갤러리
description: 통합 Azure Monitor 통합 문서 갤러리를 사용 하 여 Azure Security Center 데이터에 대 한 풍부한 대화형 보고서를 만드는 방법을 알아봅니다.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107712"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Security Center 데이터에 대 한 풍부한 대화형 보고서 만들기

[Azure Monitor 통합 문서](../azure-monitor/visualize/workbooks-overview.md) 는 데이터 분석을 위한 유연한 캔버스를 제공 하 고 Azure Portal 내에서 풍부한 시각적 보고서를 만듭니다. 이를 통해 Azure에서 여러 데이터 원본을 탭하여 통합된 대화형 환경으로 결합할 수 있습니다.

통합 문서는 Azure 데이터를 시각화 하기 위한 풍부한 기능 집합을 제공 합니다. 각 시각화 형식에 대 한 자세한 예제는 [시각화 예제 및 설명서](../azure-monitor/visualize/workbooks-text-visualizations.md)를 참조 하세요. 

Azure Security Center 내에서 기본 제공 보고서에 액세스 하 여 조직의 보안 상태를 추적할 수 있습니다. 또한 사용자 지정 보고서를 빌드하여 Security Center 또는 지원 되는 다른 데이터 원본에서 광범위 한 데이터를 볼 수 있습니다.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="시간에 따른 보안 점수 보고서":::

## <a name="availability"></a>가용성

| 양상                          | 세부 정보                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:                  | 미리 보기<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| 가격 책정:                        | Free                                                                                                                                         |
| 필요한 역할 및 권한: | 통합 문서를 저장 하려면 대상 리소스 그룹에 대 한 최소 통합 문서 참가자 권한이 있어야 합니다.                                      |
| 클라우드:                         | ![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Azure Security Center의 통합 문서 갤러리

통합 된 Azure 통합 문서 기능을 사용 하 여 Azure Security Center 사용자 지정 대화형 보고서를 쉽게 빌드할 수 있습니다. 또한 Security Center에는 사용자 지정에 사용할 수 있는 다음 보고서를 포함 하는 통합 문서 갤러리가 포함 되어 있습니다.

- **시간에 따른 보안 점수** -구독의 점수를 추적 하 고 리소스에 대 한 권장 사항을 변경 합니다.
- **시스템 업데이트** -리소스, OS, 심각도 등의 누락 된 시스템 업데이트를 봅니다.
- **취약성 평가 결과** -Azure 리소스의 취약성 검색 결과 보기

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Azure Security Center의 기본 제공 통합 문서 갤러리":::

제공 된 보고서 중 하나를 선택 하거나 직접 만듭니다.

> [!TIP]
> **편집** 단추를 사용 하 여 제공 된 보고서 중에서 만족 하는 보고서를 사용자 지정할 수 있습니다. 편집이 완료 되 면 **저장** 을 선택 하면 변경 내용이 새 통합 문서에 저장 됩니다.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="제공 된 통합 문서를 편집 하 여 특정 요구에 맞게 사용자 지정":::

### <a name="use-the-secure-score-over-time-report"></a>' 시간에 따른 보안 점수 ' 보고서 사용

이 보고서는 Log Analytics 작업 영역의 보안 점수 데이터를 사용 합니다. [Azure Portal의 Security Center 페이지에서 연속 내보내기 구성](continuous-export.md?tabs=azure-portal)에 설명 된 대로 연속 내보내기 도구에서 해당 데이터를 내보내야 합니다.

연속 내보내기를 설정할 때 **스트리밍 업데이트** 및 **스냅숏으로** 내보내기 빈도를 설정 합니다.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="시간에 따른 보안 통합 문서에 대해 연속 내보내기 구성의 내보내기 빈도 설정에서 두 옵션을 모두 선택 해야 합니다.":::

> [!NOTE]
> 스냅숏은 매주 내보낼 수 있으므로이 보고서의 데이터를 보려면 먼저 내보낼 첫 번째 스냅숏을 하나 이상 기다려야 합니다.

> [!TIP]
> 조직 전체에서 연속 내보내기를 구성 하려면 [대규모 연속 내보내기 구성](continuous-export.md?tabs=azure-policy)에서 설명 하는 제공 된 Azure Policy ' DeployIfNotExist ' 정책을 사용 합니다.

시간에 따른 보안 점수 보고서에는 선택한 작업 영역에 보고 하는 구독에 대 한 5 개의 그래프가 있습니다.


|그래프  |예제  |
|---------|---------|
|**지난 주 및 월의 점수 추세**<br>이 섹션을 사용 하 여 구독의 점수에 대 한 현재 점수와 일반적인 추세를 모니터링할 수 있습니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="기본 제공 보고서의 보안 점수 추세":::|
|**선택한 모든 구독에 대 한 집계 점수**<br>선택한 시간 범위에서 임의의 날짜에 집계 된 점수를 확인 하려면 추세 선에서 임의의 지점 위로 마우스를 가져갑니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="선택한 모든 구독에 대 한 집계 점수":::|
|**비정상 리소스의 권장 사항**<br>이 표에서는 선택한 기간 동안 비정상으로 변경 된 대부분의 리소스를 가진 권장 사항을 심사 하는 데 도움이 됩니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="비정상 리소스의 권장 사항":::|
|**특정 보안 컨트롤의 점수**<br>Security Center의 보안 컨트롤은 권장 구성의 논리적 그룹화입니다. 이 차트에서는 모든 컨트롤의 주간 점수를 한눈에 보여 줍니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="선택한 기간 동안 보안 컨트롤의 점수":::|
|**리소스 변경**<br>선택한 기간 동안 변경 된 상태 (정상, 비정상 또는 적용 되지 않음)가 있는 대부분의 리소스에 대 한 권장 사항이 여기에 나열 됩니다. 목록에서 권장 사항을 선택 하면 특정 리소스를 나열 하는 새 테이블이 열립니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="상태가 변경 된 대부분의 리소스에 대 한 권장 사항":::|

### <a name="use-the-system-updates-report"></a>' 시스템 업데이트 ' 보고서 사용

이 보고서는 "시스템 업데이트를 컴퓨터에 설치 해야 합니다." 라는 보안 권장 사항을 기반으로 합니다.

이 보고서는 처리 중인 업데이트가 있는 컴퓨터를 식별 하는 데 도움이 됩니다.

다음에 따라 선택한 구독에 대 한 상황을 볼 수 있습니다.

- 해결 되지 않은 업데이트가 포함 된 리소스 목록
- 리소스에서 누락 된 업데이트 목록

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="누락 된 업데이트 보안 권장 사항을 기반으로 하 Security Center의 시스템 업데이트 보고서":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>' 취약성 평가 발견 ' 보고서 사용

Security Center에는 컴퓨터에 대 한 취약성 스캐너, 컨테이너 레지스트리의 컨테이너 레지스트리 및 SQL server가 포함 됩니다.

다음 스캐너 사용에 대해 자세히 알아보세요.

- [통합 VA 스캐너를 사용 하 여 컴퓨터 검색](deploy-vulnerability-assessment-vm.md)
- [취약성에 대한 레지스트리 이미지 스캔](defender-for-container-registries-usage.md)
- [SQL 리소스에서 취약성 검색](defender-for-sql-on-machines-vulnerability-assessment.md)

이러한 각 스캐너에 대 한 결과는 별도의 권장 사항에서 보고 됩니다.

- 가상 머신의 취약성을 수정해야 함
- Azure Container Registry 이미지의 취약성을 수정해야 함(Qualys 제공)
- SQL 데이터베이스에 대한 취약성 평가 결과를 수정해야 함
- 머신의 SQL 서버에 대한 취약성 평가 결과를 수정해야 함

이 보고서는 이러한 결과를 수집 하 여 심각도, 리소스 유형 및 범주별로 구성 합니다.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Security Center의 취약성 평가 결과 보고서":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>다른 통합 문서 갤러리에서 통합 문서 가져오기

다른 Azure 서비스에서 통합 문서를 빌드하고 Azure Security Center 통합 문서 갤러리로 이동 하려면 다음을 수행 합니다.

1. 대상 통합 문서를 엽니다.

1. 도구 모음에서 **편집** 을 선택 합니다.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Azure Monitor 통합 문서 편집":::

1. 도구 모음에서 **</>** 고급 편집기를 선택 하 여 입력 합니다.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="고급 편집기를 시작 하 여 갤러리 템플릿 JSON 코드 가져오기":::

1. 통합 문서의 갤러리 템플릿 JSON을 복사 합니다.

1. Security Center에서 통합 문서 갤러리를 열고 메뉴 모음에서 **새로 만들기** 를 선택 합니다.
1. 고급 편집기를 **</>** 입력 하려면를 선택 합니다.
1. 전체 갤러리 템플릿 JSON에 붙여넣습니다.
1. **적용** 을 선택합니다.
1. 도구 모음에서 다른 **이름으로 저장** 을 선택 합니다.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Security Center 갤러리에 통합 문서 저장":::

1. 통합 문서를 저장 하는 데 필요한 세부 정보를 입력 합니다.
   1. 통합 문서의 이름입니다.
   2. 원하는 지역
   3. 구독, 리소스 그룹 및 공유를 적절 하 게 합니다.

**최근에 수정한** 통합 문서 범주에서 저장 된 통합 문서를 찾을 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 제공 보고서를 포함 하는 통합 Azure Monitor 통합 문서 페이지 Security Center 설명 하 고 사용자 지정 대화형 보고서를 작성 하는 옵션에 대해 설명 했습니다.

- [Azure Monitor 통합 문서](../azure-monitor/visualize/workbooks-overview.md) 에 대 한 자세한 정보
- 기본 제공 보고서는 Security Center의 권장 사항에서 데이터를 가져옵니다. 보안 권장 사항에서 많은 보안 권장 사항에 대해 알아보기 [-참조 가이드](recommendations-reference.md)