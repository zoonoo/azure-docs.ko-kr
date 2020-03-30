---
title: Azure 포털에서 VM에 대한 Azure 모니터 사용
description: 단일 Azure 가상 컴퓨터 또는 가상 시스템 규모 집합에서 VM에 대한 Azure 모니터를 평가하는 방법을 알아봅니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480711"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Azure 포털에서 VM에 대한 Azure 모니터 사용

이 문서에서는 Azure 포털을 사용하여 소수의 Azure 가상 시스템(VM)에서 VM에 대한 Azure 모니터를 사용하도록 설정하는 방법을 설명합니다. 목표는 VM을 모니터링하고 성능 또는 가용성 문제를 발견하는 것입니다. 

시작하기 전에 필수 [구성 조건을](vminsights-enable-overview.md) 검토하고 구독 및 리소스가 요구 사항을 충족하는지 확인합니다.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>단일 Azure VM에 대한 모니터링 사용
Azure VM을 모니터링하려면 다음을 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. **Virtual Machines**를 선택합니다.

1. 목록에서 VM을 선택합니다.

1. VM 페이지에서 **모니터링** 섹션에서 **인사이트를** 선택한 다음 **을 사용하도록 설정합니다.**

    ![VM에 대해 VM용 Azure Monitor를 사용하도록 설정](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. **Azure Monitor Insights 등록** 페이지에서 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 드롭다운 목록에서 해당 작업 영역을 선택합니다.  

    이 목록은 구독에서 VM이 배포된 기본 작업 영역 및 위치를 미리 선택합니다. 

    >[!NOTE]
    >VM의 모니터링 데이터를 저장할 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요. Log Analytics 작업 영역은 [지원 지역](vminsights-enable-overview.md#log-analytics) 중 하나에 속해야 합니다.

6. 구성이 수행될 때 상태 메시지가 나타납니다.

    ![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>단일 가상 시스템 규모 집합에 대한 모니터링 지원

Azure 가상 시스템 규모 집합을 모니터링하려면 다음을 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. **가상 컴퓨터 축척 집합을 선택합니다.**

3. 목록에서 가상 시스템 축척 집합을 선택합니다.

4. 가상 컴퓨터 규모 집합 페이지에서 **모니터링** 섹션에서 **인사이트를** 선택한 다음 **을 사용하도록 설정합니다.**

5. **인사이트** 페이지에서 기존 로그 분석 작업 영역을 사용하려면 드롭다운 목록에서 선택합니다.

    이 목록은 VM이 구독에 배포되는 기본 작업 영역 및 위치를 미리 선택합니다. 

    ![가상 시스템 규모 집합에 대해 VM용 Azure 모니터 사용](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >가상 시스템 규모 집합에서 모니터링 데이터를 저장하는 새 Log Analytics 작업 영역을 만들려면 [로그 분석 작업 영역 만들기를](../learn/quick-create-workspace.md)참조하십시오. Log Analytics 작업 영역은 [지원 지역](vminsights-enable-overview.md#log-analytics) 중 하나에 속해야 합니다.

6. 구성이 수행될 때 상태 메시지가 나타납니다.

    >[!NOTE]
    >축척 세트에 수동 업그레이드 모델을 사용하는 경우 인스턴스를 업그레이드하여 설정을 완료합니다. **설정** 섹션에서 **인스턴스** 페이지에서 업그레이드를 시작할 수 있습니다.
    
    ![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

VM 또는 가상 시스템 규모 집합에 대한 모니터링을 사용하도록 설정되었으므로 VM용 Azure 모니터에서 모니터링 정보를 분석할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* 검색된 응용 프로그램 종속성을 보려면 [VM 맵에 Azure 모니터 사용을](vminsights-maps.md)참조하십시오. 
* 병목 현상, 전반적인 사용률 및 VM 성능을 식별하려면 [Azure VM 성능 보기를](vminsights-performance.md)참조하십시오.
