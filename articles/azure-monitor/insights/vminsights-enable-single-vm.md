---
title: Azure Portal에서 VM용 Azure Monitor 사용
description: 단일 Azure 가상 머신 또는 가상 머신 확장 집합에서 VM용 Azure Monitor를 평가 하는 방법에 대해 알아봅니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507061"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Azure Portal에서 단일 VM 또는 VMSS에 대 한 Azure Monitor를 사용 하도록 설정
이 문서에서는 Azure Portal를 사용 하 여 단일 가상 머신 또는 가상 머신 확장 집합에 대 한 VM용 Azure Monitor를 사용 하도록 설정 하는 방법을 설명 합니다. 이 절차는 다음과 같은 경우에 사용할 수 있습니다.

- Azure 가상 머신
- Azure 가상 머신 확장 집합
- Azure Arc 컴퓨터

시작 하기 전에 [필수 구성 요소](vminsights-enable-overview.md) 를 검토 하 고 구독과 리소스가 요구 사항을 충족 하는지 확인 합니다.  

## <a name="enable-azure-monitor-for-vms"></a>VM용 Azure Monitor 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **가상**머신, **가상 머신 확장 집합**또는 **컴퓨터-Azure Arc**를 선택 합니다.

1. 목록에서 리소스를 선택 합니다.

1. 메뉴의 **모니터링** 섹션에서 **Insights** 를 선택 하 고 **사용**을 선택 합니다. 다음 예제에서는 Azure 가상 머신을 보여 주지만 Azure VMSS 또는 Azure Arc의 메뉴는 유사 합니다.

    ![VM에 대해 VM용 Azure Monitor를 사용하도록 설정](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. VM이 Log Analytics 작업 영역에 이미 연결 되어 있지 않은 경우 하나를 선택 하 라는 메시지가 표시 됩니다. 이전에 [작업 영역을 만들지](../../azure-monitor/learn/quick-create-workspace.md)않은 경우 구독에서 VM 또는 vmss가 배포 된 위치에 대 한 기본값을 선택할 수 있습니다. 이 작업 영역은 아직 없는 경우 만들어지고 구성 됩니다.

2. 구성이 수행 되 면 상태 메시지가 표시 됩니다.

    >[!NOTE]
    >확장 집합에 수동 업그레이드 모델을 사용 하는 경우 인스턴스를 업그레이드 하 여 설치를 완료 합니다. **인스턴스** 페이지의 **설정** 섹션에서 업그레이드를 시작할 수 있습니다.

    ![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>다음 단계

* 검색 된 응용 프로그램 종속성을 보려면 [VM용 Azure Monitor 매핑 사용](vminsights-maps.md)을 참조 하세요. 
* 병목 상태, 전반적인 사용률 및 VM의 성능을 식별 하려면 [AZURE VM 성능 보기](vminsights-performance.md)를 참조 하세요.
