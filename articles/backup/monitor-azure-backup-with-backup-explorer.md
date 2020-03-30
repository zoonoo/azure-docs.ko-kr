---
title: 백업 탐색기를 통해 백업 모니터링
description: 이 문서에서는 Backup Explorer를 사용하여 볼트, 구독, 지역 및 테넌타 전체에서 백업을 실시간으로 모니터링하는 방법을 설명합니다.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131805"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>백업 탐색기를 통해 백업 모니터링

조직이 점점 더 많은 컴퓨터를 클라우드에 백업함에 따라 이러한 백업을 효율적으로 모니터링하는 것이 점점 더 중요해지고 있습니다. 시작하는 가장 좋은 방법은 대규모 부동산에서 운영 정보를 보기 위해 중앙 위치를 사용하는 것입니다.

백업 탐색기는 Azure Backup 고객에게 이 단일 중앙 위치를 제공하는 기본 제공 Azure Monitor 통합 문서입니다. Backup Explorer를 사용하면 테넌터, 위치, 구독, 리소스 그룹 및 자격 증명 모음에 걸쳐 Azure의 전체 백업 부동산에서 운영 활동을 모니터링할 수 있습니다. 대체로 백업 탐색기는 다음과 같은 기능을 제공합니다.

* **대규모 관점**: 전체 부동산에서 백업을 위해 아직 구성되지 않은 백업 항목, 작업, 경고, 정책 및 리소스에 대한 집계된 보기를 가져옵니다. 
* **드릴다운 분석**: 각 작업, 경고, 정책 및 백업 항목에 대한 자세한 정보를 한 곳에 표시합니다.
* **실행 가능한 인터페이스:** 문제를 식별한 후 관련 백업 항목 또는 Azure 리소스로 원활하게 이동하여 문제를 해결할 수 있습니다.

이러한 기능은 Azure 리소스 그래프 및 Azure Monitor 통합 문서와의 기본 통합을 통해 즉시 제공됩니다.

> [!NOTE]
> * 현재 백업 탐색기는 Azure 가상 시스템(VM) 데이터에만 사용할 수 있습니다.
> * 백업 탐색기는 지난 7일(최대) 동안 백업에 대한 정보를 볼 수 있는 운영 대시보드입니다.
> * 현재 국가 클라우드에서는 백업 탐색기가 지원되지 않습니다.
> * 현재 백업 탐색기 템플릿 사용자 지정은 지원되지 않습니다. 
> * Azure 리소스 그래프 데이터에 사용자 지정 자동화를 작성하지 않는 것이 좋습니다.

## <a name="get-started"></a>시작

복구 서비스 자격 증명 모음으로 이동하여 **개요** 창에서 백업 탐색기 링크를 선택하여 **백업 탐색기에** 액세스할 수 있습니다.

![볼트 빠른 링크](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

링크를 선택하면 백업 탐색기가 열리며, 이 탐색기는 액세스 권한이 있는 모든 자격 증명 모음 및 구독에 걸쳐 집계된 보기를 제공합니다. Azure 등대 계정을 사용하는 경우 액세스 권한이 있는 모든 테넌트의 데이터를 볼 수 있습니다. 자세한 내용은 이 문서의 끝에 있는 "테넌트 간 보기" 섹션을 참조하세요.

![백업 탐색기 방문 페이지](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>백업 탐색기 사용 사례

Backup Explorer는 각각 특정 백업 아티팩트(예: 백업 항목, 작업 또는 정책)에 대한 자세한 정보를 제공하는 여러 탭을 표시합니다. 이 섹션에서는 각 탭에 대한 간략한 개요를 제공합니다. 이 비디오에서는 사용 가능한 컨트롤에 대한 설명과 함께 각 백업 아티팩트에 대한 샘플 사용 사례를 제공합니다.

### <a name="the-summary-tab"></a>요약 탭

**요약** 탭은 백업 부동산의 전체 상태를 한눈에 볼 수 있습니다. 예를 들어 보호되는 항목 수, 보호를 사용하도록 설정하지 않은 항목 수 또는 지난 24시간 동안 성공한 작업 수를 볼 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>백업 항목 탭

구독, 자격 증명 모음 및 기타 특성별로 각 백업 항목을 필터링하고 볼 수 있습니다. 백업 항목의 이름을 선택하면 해당 항목에 대한 Azure 창을 열 수 있습니다. 예를 들어 테이블에서 항목 *X에*대한 마지막 백업이 실패한 것을 확인할 수 있습니다. *X를*선택하면 주문형 백업 작업을 트리거할 수 있는 항목의 **백업** 창을 열 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>작업 탭

**작업** 탭을 선택하여 지난 7일 동안 트리거된 모든 작업의 세부 정보를 확인합니다. 여기에서 *작업 작업,* 작업 *상태*및 *오류 코드(실패한* 작업의 경우)별로 필터링할 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>경고 탭

경고 **탭을** 선택하여 지난 7일 동안 볼트에서 생성된 모든 경고의 세부 정보를 볼 수 있습니다. *유형(백업 실패* 또는 *복원 실패),* 현재 상태(활성 또는 *해결됨)* 및*Active* 심각도(중요, *경고*또는 *정보)별로*경고를 필터링할 수 있습니다.*Critical* Azure VM으로 이동하고 필요한 작업을 수행할 링크를 선택할 수도 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>정책 탭

**정책** 탭을 선택하여 백업 부동산에서 생성된 모든 백업 정책에 대한 주요 정보를 볼 수 있습니다. 정책에 의해 지정된 보존 범위 및 백업 빈도와 함께 각 정책과 연결된 항목 수를 볼 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>백업을 사용할 수 없는 탭

보호가 필요한 모든 컴퓨터에 대해 백업을 사용하도록 설정해야 합니다. 백업 탐색기를 사용하면 백업 관리자는 조직의 컴퓨터가 아직 백업으로 보호되지 않는 컴퓨터를 신속하게 식별할 수 있습니다. 이 정보를 보려면 백업 을 사용할 수 없습니다 탭을 **선택합니다.**

**백업 을 사용할 수 없습니다** 창에는 보호되지 않은 컴퓨터 목록이 있는 테이블이 표시됩니다. 조직에서 프로덕션 컴퓨터 및 테스트 컴퓨터 또는 다양한 기능을 제공하는 컴퓨터에 서로 다른 태그를 할당할 수 있습니다. 각 컴퓨터 클래스에는 별도의 백업 정책이 필요하므로 태그별로 필터링하면 각 컴퓨터에 특정한 정보를 볼 수 있습니다. 컴퓨터 이름을 선택하면 해당 컴퓨터의 **백업 구성** 창으로 리디렉션되어 적절한 백업 정책을 적용하도록 선택할 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Excel로 내보내기

모든 테이블 또는 차트의 내용을 Excel 스프레드시트로 내보낼 수 있습니다. 기존 필터가 적용된 내용이 있는 것처럼 내보내줍니다. 추가 테이블 행을 내보내려면 각 탭 상단의 **페이지별 행** 드롭다운 목록을 사용하여 페이지에 표시할 행 수를 늘릴 수 있습니다.

## <a name="pin-to-the-dashboard"></a>대시보드에 고정

각 테이블 또는 차트 상단에 있는 "핀" 아이콘을 선택하여 Azure 포털 대시보드에 고정할 수 있습니다. 이 정보를 고정하면 가장 중요한 정보를 표시하도록 맞춤화된 대시보드를 만들 수 있습니다.

## <a name="cross-tenant-views"></a>테넌트 간 보기

여러 테넌트 환경에서 구독에 대한 위임된 액세스 권한이 있는 Azure 등대 사용자인 경우 기본 구독 필터를 사용할 수 있습니다. Azure 포털의 오른쪽 상단에 있는 "필터" 아이콘을 선택하여 데이터를 표시하려는 구독을 표시합니다. 이 기능을 사용하면 Backup Explorer에서 선택한 구독의 모든 자격 증명 모음에 대한 정보를 집계합니다. 자세한 내용은 [Azure 등대란 무엇입니까?](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="next-steps"></a>다음 단계

[백업 데이터에 대한 통찰력을 얻기 위해 Azure 모니터를 사용하는 방법 알아보기](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
