---
title: Azure Sentinel의 Azure 모니터 통합 문서를 기반으로 대시보드를 사용하여 데이터 시각화 | 마이크로 소프트 문서
description: 이 자습서를 사용하여 Azure Sentinel의 통합 문서를 기반으로 대시보드를 사용하여 데이터를 시각화하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/01/2020
ms.author: yelevin
ms.openlocfilehash: b4461ac43e9356536914b345ef28f5de62fc9f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585223"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>자습서: 데이터 시각화 및 모니터링



데이터 [원본을](quickstart-onboard.md) Azure Sentinel에 연결한 후 사용자 지정 대시보드를 만드는 데 다재다능함을 제공하는 Azure Sentinel 의 Azure Sentinel 채택을 사용하여 데이터를 시각화하고 모니터링할 수 있습니다. 통합 문서는 Azure Sentinel에서 다르게 표시되지만 Azure 모니터 통합 문서를 [사용하여 대화형 보고서를 만드는](../azure-monitor/app/usage-workbooks.md)방법을 확인하는 것이 유용할 수 있습니다. Azure Sentinel을 사용하면 데이터에 대한 사용자 지정 통합 문서를 만들 수 있으며 데이터 원본을 연결하는 즉시 데이터를 신속하게 파악할 수 있는 기본 제공 통합 문서 템플릿도 제공됩니다.


이 자습서에서는 Azure Sentinel에서 데이터를 시각화하는 데 도움이 됩니다.
> [!div class="checklist"]
> * 기본 제공 통합 문서 사용
> * 새 통합 문서 만들기

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역의 리소스 그룹에 대한 통합 문서 판독기 또는 통합 문서 기여자 사용 권한이 이상 있어야 합니다.

> [!NOTE]
> Azure Sentinel에서 볼 수 있는 통합 문서는 Azure Sentinel 작업 영역 리소스 그룹에 저장되며 생성된 작업 영역에 의해 태그가 지정됩니다.

## <a name="use-built-in-workbooks"></a>기본 제공 통합 문서 사용

1. 통합 **문서로** 이동한 다음 **템플릿을** 선택하여 Azure Sentinel 기본 제공 통합 문서의 전체 목록을 확인합니다. 연결된 데이터 형식과 관련된 데이터를 확인하기 위해 각 통합 문서의 **필수 데이터 유형** 필드에 는 관련 데이터를 Azure Sentinel으로 이미 스트리밍하는 경우 녹색 확인 표시 옆에 데이터 형식이 나열됩니다.
  ![통합 문서로 이동](./media/tutorial-monitor-data/access-workbooks.png)
1. **통합 문서 보기를** 클릭하여 데이터로 채워진 템플릿을 확인합니다.
  
1. 통합 문서를 편집하려면 **저장**을 선택한 다음 템플릿에 대한 json 파일을 저장할 위치를 선택합니다. 

   > [!NOTE]
   > 이렇게 하면 관련 템플릿을 기반으로 Azure 리소스가 생성되고 데이터가 아닌 템플릿 Json 파일 자체를 저장합니다.


1. **통합 문서 보기를 선택합니다.** 그런 다음 상단의 **편집** 단추를 클릭합니다. 이제 통합 문서를 편집하고 필요에 따라 사용자 지정할 수 있습니다. 통합 문서를 사용자 지정하는 방법에 대한 자세한 내용은 [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/app/usage-workbooks.md)방법을 참조하세요.
![통합 문서 보기](./media/tutorial-monitor-data/workbook-graph.png)
1. 변경한 후 통합 문서를 저장할 수 있습니다. 

1. 통합 문서를 복제할 수도 있습니다: **편집을** 선택한 다음 로 **저장하여**동일한 구독 및 리소스 그룹에서 다른 이름으로 저장해야 합니다. 이러한 통합 문서는 **내 통합 문서** 탭 아래에 표시됩니다.


## <a name="create-new-workbook"></a>새 통합 문서 만들기

1. 통합 **문서로** 이동한 다음 **통합 문서 추가를** 선택하여 처음부터 새 통합 문서를 만듭니다.
  ![통합 문서로 이동](./media/tutorial-monitor-data/create-workbook.png)

1. 통합 문서를 편집하려면 **편집을**선택한 다음 필요에 따라 텍스트, 쿼리 및 매개 변수를 추가합니다. 통합 문서를 사용자 지정하는 방법에 대한 자세한 내용은 [Azure Monitor 통합 문서를 사용하여 대화형 보고서를 만드는](../azure-monitor/app/usage-workbooks.md)방법을 참조하세요. 

1. 쿼리를 빌드할 때 **데이터 원본이** **로그로**설정되고 **리소스 유형이** **로그 분석으로** 설정된 다음 관련 작업 영역을 선택합니다. 

1. 통합 문서를 만든 후 통합 문서를 저장하여 Azure Sentinel 작업 영역의 구독 및 리소스 그룹에 저장합니다.

1. 조직의 다른 사용자가 통합 문서를 사용하도록 하려면 저장 에서 **공유 보고서를** **선택합니다.** 이 통합 문서를 사용자만 사용할 수 있도록 하려면 **내 보고서를**선택합니다.

1. 작업 영역에서 통합 문서 간에 전환하려면 통합 문서의](./media/tutorial-monitor-data/switch.png)맨 위 창에서 스위치 **열기** ![통합 문서를 선택할 수 있습니다. 오른쪽으로 열리는 창에서 통합 문서 간에 전환합니다.

   ![통합 문서 전환](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>통합 문서를 삭제하는 방법

Azure Sentinel 템플릿에서 만든 통합 문서를 삭제할 수 있습니다. 

사용자 지정된 통합 문서를 삭제하려면 통합 문서 페이지에서 삭제할 저장된 통합 문서를 선택하고 **삭제를**선택합니다. 이렇게 하면 저장된 통합 문서가 제거됩니다.

> [!NOTE]
> 이렇게 하면 템플릿을 변경한 내용뿐만 아니라 리소스도 제거됩니다. 원본 템플릿은 사용 가능한 상태로 유지됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Sentinel에서 데이터를 보는 방법을 배웠습니다.

위협에 대한 응답을 자동화하는 방법을 알아보려면 [Azure Sentinel에서 자동화된 위협 대응 설정을](tutorial-respond-threats-playbook.md)참조하세요.
