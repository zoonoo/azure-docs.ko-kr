---
title: Azure Monitor 뷰 디자이너에서 통합 문서로 전환 요약 및 액세스
description: Azure Monitor의 뷰에서 전환할 때 통합 문서에 액세스하는 데 필요한 권한입니다.
author: shijatsu
ms.author: shijain
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 89b812fff9856fda2bd1da896261481eddfdc566
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316680"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>뷰 디자이너에서 통합 문서로 전환 요약 및 액세스
[뷰 디자이너](view-designer.md)는 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역의 데이터를 시각화하는 데 도움이 되는 사용자 지정 뷰를 만들 수 있게 하는 Azure Monitor 기능입니다. 이러한 기능은 단계적으로 제거되고 있으며, 추가 기능을 제공하는 통합 문서로 대체되고 있습니다. 이 문서에서는 통합 문서에 액세스하는 데 필요한 개요 요약 및 사용 권한을 만드는 방법을 자세히 설명합니다.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Azure 대시보드에서 작업 영역 요약 만들기
뷰 디자이너 사용자는 보기 세트를 나타내는 개요 타일에 익숙할 수 있습니다. 뷰 디자이너 작업 영역 요약과 같은 시각적 개요를 유지 관리하기 위해 통합 문서는 [Azure Portal 대시보드](../../azure-portal/azure-portal-dashboards.md)에 고정할 수 있는 고정된 단계를 제공합니다. 작업 영역 요약의 개요 타일과 마찬가지로 고정된 통합 문서 항목은 통합 문서 보기에 직접 연결됩니다.

고정된 항목 및 시각화에 대한 자동 새로 고침, 이동, 크기 조정 및 추가 필터링을 허용하는 Azure 대시보드와 함께 제공되는 높은 수준의 사용자 지정 기능을 활용할 수 있습니다. 

![작업 영역 요약이라는 사용자 지정된 Azure 대시보드를 보여주는 스크린샷.](media/view-designer-conversion-access/dashboard.png)

새 Azure 대시보드를 만들거나 기존 대시보드를 선택하여 통합 문서 항목의 고정을 시작합니다.

개별 항목을 고정하려면 특정 단계에 대해 고정 아이콘을 사용하도록 설정해야 합니다. 그렇게 하려면 단계에 해당하는 **편집** 단추를 선택한 다음, 톱니바퀴 아이콘을 선택하여 **고급 설정** 을 엽니다. **이 단계에서 항상 고정 아이콘 표시** 옵션을 선택하면 단계의 오른쪽 위에 고정 아이콘이 표시됩니다. 이 핀으로 개요 타일과 같은 특정 시각화를 대시보드에 고정할 수 있습니다.

![고정 단계](media/view-designer-conversion-access/pin-step.png)


통합 문서 또는 전체 통합 문서 콘텐츠의 여러 시각화를 대시보드에 고정할 수도 있습니다. 전체 통합 문서를 고정 하려면 위쪽 도구 모음에서 **편집** 을 선택하여 **편집 모드** 를 전환합니다. 전체 통합 문서 항목 또는 통합 문서의 모든 개별 단계 및 시각화를 고정할 수 있는 고정 아이콘이 나타납니다.

![모두 고정](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>권한 공유 및 보기 
통합 문서는 프라이빗 또는 공유 문서라는 이점이 있습니다. 기본적으로 저장된 통합 문서가 **내 보고서** 아래에 저장됩니다. 즉, 작성자만 이 통합 문서를 볼 수 있습니다.

**편집 모드** 에 있는 동안 상단 도구 모음에서 **공유** 아이콘을 선택하여 통합 문서를 공유할 수 있습니다. 통합 문서를 **공유 보고서** 로 이동하라는 메시지가 표시되며 통합 문서에 직접 액세스할 수 있는 링크가 생성됩니다.

사용자가 공유 통합 문서를 보려면 통합 문서가 저장된 구독 및 리소스 그룹 모두에 대한 액세스 권한이 있어야 합니다.

![구독 기반 액세스](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>다음 단계

- [일반 작업](view-designer-conversion-tasks.md)