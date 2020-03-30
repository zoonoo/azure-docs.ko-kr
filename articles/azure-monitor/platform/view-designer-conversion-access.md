---
title: 통합 문서 변환 요약 및 액세스에 대한 Azure 모니터 보기 디자이너
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658849"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>통합 문서 변환 요약 및 액세스에 대한 보기 디자이너
[뷰 디자이너는](view-designer.md) 차트, 목록 및 타임라인을 사용하여 Log Analytics 작업 영역에서 데이터를 시각화하는 데 도움이 되는 사용자 지정 보기를 만들 수 있는 Azure Monitor의 기능입니다. 단계적으로 폐지되고 추가 기능을 제공하는 통합 문서로 대체됩니다. 이 문서에서는 통합 문서에 액세스하는 데 필요한 개요 요약 및 사용 권한을 만드는 방법에 대해 자세히 설명합니다.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Azure 대시보드에서 작업 영역 요약 만들기
뷰 디자이너 사용자는 뷰 집합을 나타내는 개요 타일이 있는 데 익숙할 수 있습니다. 뷰 디자이너 작업 영역 요약과 같은 시각적 개요를 유지 관리하려면 통합 문서는 Azure [포털 대시보드에](../../azure-portal/azure-portal-dashboards.md)고정할 수 있는 고정된 단계를 제공합니다. 작업 영역 요약의 개요 타일과 마찬가지로 고정된 통합 문서 항목은 통합 문서 보기에 직접 연결됩니다.

Azure 대시보드와 함께 제공되는 높은 수준의 사용자 지정 기능을 활용하여 고정된 항목 및 시각화에 대한 자동 새로 고침, 이동, 크기 조정 및 추가 필터링을 수행할 수 있습니다. 

![대시보드](media/view-designer-conversion-access/dashboard.png)

새 Azure 대시보드를 만들거나 기존 대시보드를 선택하여 통합 문서 항목 고정을 시작합니다.

개별 항목을 고정하려면 특정 단계에 대한 핀 아이콘을 사용하도록 설정해야 합니다. 이렇게 하려면 단계에 해당하는 **편집** 단추를 선택한 다음 기어 아이콘을 선택하여 **고급 설정을**엽니다. 이 단계에서 **항상 핀 아이콘을 표시하는**옵션을 선택하고 핀 아이콘이 단계의 오른쪽 상단 모서리에 나타납니다. 이 핀을 사용하면 개요 타일과 같은 특정 시각화를 대시보드에 고정할 수 있습니다.

![핀 스텝](media/view-designer-conversion-access/pin-step.png)


통합 문서 또는 전체 통합 문서 콘텐츠에서 대시보드에 여러 시각화를 고정할 수도 있습니다. 전체 통합 문서를 고정하려면 상단 도구 모음에서 **편집을** 선택하여 **편집 모드를 전환합니다.** 전체 통합 문서 항목 또는 통합 문서의 모든 개별 단계 및 시각화를 고정할 수 있는 핀 아이콘이 나타납니다.

![모두 고정](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>사용 권한 공유 및 보기 
통합 문서는 개인 문서 또는 공유 문서의 이점이 있습니다. 기본적으로 저장된 통합 문서는 내 **보고서**아래에 저장되므로 작성자만 이 통합 문서를 볼 수 있습니다.

**편집 모드에서**최상위 도구 모음에서 **공유** 아이콘을 선택하여 통합 문서를 공유할 수 있습니다. 통합 문서를 **공유 보고서로**이동하라는 메시지가 표시되며, 통합 정보에 대한 직접 액세스를 제공하는 링크를 생성합니다.

사용자가 공유 통합 문서를 보려면 통합 문서아래에 저장된 구독 및 리소스 그룹에 모두 액세스할 수 있어야 합니다.

![구독 기반 액세스](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>다음 단계

- [일반적인 작업](view-designer-conversion-tasks.md)