---
title: Azure Portal에서 VM용 Azure Monitor 사용
description: 단일 Azure 가상 머신 또는 가상 머신 확장 집합에서 VM용 Azure Monitor를 평가 하는 방법에 대해 알아봅니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480711"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Azure Portal에서 VM용 Azure Monitor 사용

이 문서에서는 Azure Portal를 사용 하 여 적은 수의 Azure Vm (가상 머신)에서 VM용 Azure Monitor를 사용 하도록 설정 하는 방법을 설명 합니다. 목표는 Vm을 모니터링 하 고 성능 또는 가용성 문제를 검색 하는 것입니다. 

시작 하기 전에 [필수 구성 요소](vminsights-enable-overview.md) 를 검토 하 고 구독과 리소스가 요구 사항을 충족 하는지 확인 합니다.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>단일 Azure VM에 대 한 모니터링 사용
Azure VM의 모니터링을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Virtual Machines**를 선택합니다.

1. 목록에서 VM을 선택합니다.

1. VM 페이지의 **모니터링** 섹션에서 **Insights** 를 선택 하 고 **사용**을 선택 합니다.

    ![VM에 대해 VM용 Azure Monitor를 사용하도록 설정](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. **Azure Monitor Insights 등록** 페이지에서 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 드롭다운 목록에서 해당 작업 영역을 선택합니다.  

    이 목록은 구독에서 VM이 배포된 기본 작업 영역 및 위치를 미리 선택합니다. 

    >[!NOTE]
    >VM의 모니터링 데이터를 저장할 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요. Log Analytics 작업 영역은 [지원 지역](vminsights-enable-overview.md#log-analytics) 중 하나에 속해야 합니다.

6. 구성이 수행 되 면 상태 메시지가 표시 됩니다.

    ![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>단일 가상 머신 확장 집합에 대 한 모니터링 사용

Azure 가상 머신 확장 집합의 모니터링을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Virtual Machine Scale Sets**를 선택 합니다.

3. 목록에서 가상 머신 확장 집합을 선택 합니다.

4. 가상 머신 확장 집합 페이지의 **모니터링** 섹션에서 **Insights** 를 선택 하 고 **사용**을 선택 합니다.

5. **Insights** 페이지에서 기존 Log Analytics 작업 영역을 사용 하려면 드롭다운 목록에서 해당 작업 영역을 선택 합니다.

    목록은 구독에서 VM이 배포 되는 기본 작업 영역 및 위치를 preselects 합니다. 

    ![가상 머신 확장 집합에 대 한 VM용 Azure Monitor 사용](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >가상 머신 확장 집합의 모니터링 데이터를 저장할 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../learn/quick-create-workspace.md)를 참조 하세요. Log Analytics 작업 영역은 [지원 지역](vminsights-enable-overview.md#log-analytics) 중 하나에 속해야 합니다.

6. 구성이 수행 되 면 상태 메시지가 표시 됩니다.

    >[!NOTE]
    >확장 집합에 수동 업그레이드 모델을 사용 하는 경우 인스턴스를 업그레이드 하 여 설치를 완료 합니다. **인스턴스** 페이지의 **설정** 섹션에서 업그레이드를 시작할 수 있습니다.
    
    ![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

이제 VM 또는 가상 머신 확장 집합에 대 한 모니터링을 사용 하도록 설정 했으므로 모니터링 정보는 VM용 Azure Monitor에서 분석할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* 검색 된 응용 프로그램 종속성을 보려면 [VM용 Azure Monitor 매핑 사용](vminsights-maps.md)을 참조 하세요. 
* 병목 상태, 전반적인 사용률 및 VM의 성능을 식별 하려면 [AZURE VM 성능 보기](vminsights-performance.md)를 참조 하세요.
