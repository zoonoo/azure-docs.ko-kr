---
title: VM 정보에서 분석 변경
description: VM insights와 응용 프로그램 변경 분석 통합을 사용 하면 it 성능에 영향을 줄 수 있는 가상 컴퓨터에 대 한 변경 내용을 볼 수 있습니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046774"
---
# <a name="change-analysis-in-vm-insights"></a>VM 정보에서 분석 변경
VM insights와 [응용 프로그램 변경 분석](../app/change-analysis.md) 통합을 사용 하면 it 성능에 영향을 줄 수 있는 가상 컴퓨터에 대 한 변경 내용을 볼 수 있습니다.

## <a name="overview"></a>개요
생물 실행 속도가 느리고 해당 구성에 대 한 최근 변경 내용이 성능에 영향을 줄 수 있는지 여부를 조사 하려는 VM이 있다고 가정 합니다. VM 정보를 사용 하 여 VM의 성능을 확인 하 고 지난 시간에 메모리 사용량이 증가 하는 것을 찾을 수 있습니다. 변경 분석을 통해이 시간에 대 한 구성 변경의 원인이 이러한 증가의 원인 인지 여부를 확인할 수 있습니다.

응용 프로그램 변경 분석 서비스는 [Azure 리소스 그래프](../../governance/resource-graph/how-to/get-resource-changes.md) 의 변경 내용 뿐만 아니라 Azure Resource Manager의 네트워크 보안 규칙과 같은 중첩 된 속성 변경 내용을 집계 합니다. 

## <a name="enabling-change-analysis"></a>변경 분석 설정
VM insights에서 변경 분석을 등록 하려면 *Microsoft changeanalysis* 리소스 공급자를 등록 해야 합니다. Azure Portal에서 VM insights 또는 응용 프로그램 변경 분석을 처음 시작 하는 경우이 리소스 공급자가 자동으로 등록 됩니다. 응용 프로그램 변경 분석은 리소스에 대 한 성능 오버 헤드가 없는 무료 서비스입니다.

## <a name="view-change-analysis"></a>변경 분석 보기
변경 분석은 VM insights의 **성능** 또는 **맵** 탭에서 **변경** 옵션을 선택 하 여 사용할 수 있습니다. 

[![변경 내용 조사](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


**변경 내용 조사** 단추를 클릭 하 여 VM에 대해 필터링 된 응용 프로그램 변경 분석 페이지를 시작 합니다. 나열 된 변경 내용을 검토 하 여 문제가 발생할 수 있는 문제가 있는지 확인할 수 있습니다. 특정 변경 내용에 대해 잘 모르는 경우 변경 내용 열을 참조 하 **여** 변경을 수행한 사람을 확인할 수 있습니다.

[![변경 세부 정보](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>다음 단계
- [응용 프로그램 변경 분석](../app/change-analysis.md)에 대해 자세히 알아보세요.
- [Azure 리소스 그래프](../../governance/resource-graph/how-to/get-resource-changes.md)에 대해 자세히 알아보세요. 

