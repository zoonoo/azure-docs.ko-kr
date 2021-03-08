---
title: Azure 센티널 경고의 Surface 사용자 지정 세부 정보 | Microsoft Docs
description: Azure 센티널 분석 규칙의 경고에서 사용자 지정 이벤트 세부 정보를 추출 하 고 노출 하 여 더 나은 전체 인시던트 정보를 확인 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456257"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Azure 센티널의 경고에서 사용자 지정 이벤트 세부 정보 노출 

> [!IMPORTANT]
>
> - 사용자 지정 세부 정보 기능은 **미리 보기** 상태입니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

## <a name="introduction"></a>소개

[예약 된 쿼리 분석 규칙](tutorial-detect-threats-custom.md) 은 Azure 센티널에 연결 된 데이터 원본의 **이벤트** 를 분석 하 고 이러한 이벤트의 내용이 보안 관점에서 중요 한 경우 **경고** 를 생성 합니다. 이러한 경고는 Azure 센티널의 다양 한 엔진에 의해 추가 분석, 그룹화 및 필터링 되며 SOC 분석가의 주의가 보장 되는 **인시던트에** distilled 됩니다. 그러나 분석가가 인시던트를 볼 때 구성 요소 경고 자체의 속성만 즉시 표시 됩니다. 실제 콘텐츠로 가져오기-이벤트에 포함 된 정보를 사용 하려면 몇 가지 방법을 사용 해야 합니다.

**분석 규칙 마법사** 의 **사용자 지정 세부 정보** 기능을 사용 하면 이벤트 데이터를 경고 속성의 일부로 설정 하 여 해당 이벤트에서 생성 된 경고에 이벤트 데이터를 표시할 수 있습니다. 실제로이 기능을 사용 하면 사건에 대 한 즉각적인 이벤트 콘텐츠 가시성을 제공 하 여 사용자가 심사 하 고, 조사 하 고, 결론을 하 고, 훨씬 더 많은 속도와 효율성을 제공 합니다.

아래에 자세히 설명 된 절차는 분석 규칙 만들기 마법사의 일부입니다. 이는 기존 분석 규칙에서 사용자 지정 세부 정보를 추가 하거나 변경 하는 시나리오를 해결 하기 위해 독립적으로 처리 됩니다.

## <a name="how-to-surface-custom-event-details"></a>사용자 지정 이벤트 정보를 노출 하는 방법

1. Azure 센티널 탐색 메뉴에서 **분석** 을 선택 합니다.

1. 예약 된 쿼리 규칙을 선택 하 고 **편집** 을 클릭 합니다. 또는 화면 맨 위에 있는 **&#10132; 예약 된 쿼리 규칙 만들기** 를 클릭 하 여 새 규칙을 만듭니다.

1. **규칙 논리 설정** 탭을 클릭 합니다.

1. **경고 기능** 섹션에서 **사용자 지정 세부 정보** 를 선택 합니다.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="사용자 지정 세부 정보 찾기 및 선택":::

1. 지금까지 확장 된 **사용자 지정 세부 정보** 섹션에서 화면에 표시할 세부 정보에 해당 하는 키-값 쌍을 추가 합니다.

    1. **키** 필드에 경고에서 필드 이름으로 표시 되는 선택 이름을 입력 합니다.

    1. **값** 필드의 드롭다운 목록에서 경고를 표시 하려는 이벤트 매개 변수를 선택 합니다. 이 목록은 규칙 쿼리의 주체가 되는 테이블의 필드에 해당 하는 값으로 채워집니다.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="사용자 지정 세부 정보 추가":::

1. **새 항목 추가** 를 클릭 하 여 자세한 정보를 확인 하 고 마지막 단계를 반복 하 여 키-값 쌍을 정의 합니다. 

    생각이 나 실수를 변경한 경우 해당 세부 정보에 대 한 **값** 드롭다운 목록 옆에 있는 휴지통 아이콘을 클릭 하 여 사용자 지정 세부 정보를 제거할 수 있습니다.

1. 사용자 지정 세부 정보를 정의 하는 작업이 완료 되 면 **검토 및 만들기** 탭을 클릭 합니다. 규칙 유효성 검사가 성공적으로 완료 되 면 **저장** 을 클릭 합니다.

    > [!NOTE]
    > 
    >**서비스 제한**
    > - 단일 분석 규칙에서 **최대 20 개의 사용자 지정 세부 정보를** 정의할 수 있습니다.
    >
    > - 모든 사용자 지정 세부 정보에 대 한 크기 제한은 **2 k b** 입니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널 분석 규칙을 사용 하 여 경고에서 사용자 지정 세부 정보를 노출 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [예약 된 쿼리 분석 규칙](tutorial-detect-threats-custom.md)에 대 한 전체 그림을 가져옵니다.
- [Azure 센티널의 엔터티에](entities-in-azure-sentinel.md)대해 자세히 알아보세요.
