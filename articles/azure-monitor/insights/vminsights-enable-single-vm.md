---
title: 평가 위한 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정 | Microsoft Docs
description: 단일 Azure 가상 머신 또는 가상 머신 확장 집합 Vm에 대 한 Azure Monitor를 평가 하는 방법에 알아봅니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122483"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>평가 위한 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정

Azure Monitor (미리 보기) Vm에 대 한 Azure virtual machines (Vm)의 작은 숫자로 평가 하거나 단일 VM 또는 가상 머신 확장에 설정할 수 있습니다. Azure portal에서 모니터링을 사용 하기 쉽고 가장 직접적인 방법은 됩니다. 목표는 Vm을 모니터링 하 고 모든 성능 또는 가용성 문제를 검색 하는 것입니다. 

시작 하기 전에 검토 합니다 [필수 구성 요소](vminsights-enable-overview.md) 구독이 있는지 확인 하 고 리소스 요구 사항을 충족 합니다.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>단일 Azure VM에 대 한 모니터링을 사용 하도록 설정
사용 하려면 Azure VM의 모니터링:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Virtual Machines**를 선택합니다.

1. 목록에서 VM을 선택합니다.

1. VM 페이지에 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.

1. **인사이트(미리 보기)** 페이지에서 **지금 시도해 보기**를 선택합니다.

    ![VM에 대해 VM용 Azure Monitor를 사용하도록 설정](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. **Azure Monitor Insights 등록** 페이지에서 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 드롭다운 목록에서 해당 작업 영역을 선택합니다.  

    기본 작업 영역 및 구독에 VM 배포 되는 위치 목록에 미리 선택 합니다. 

    >[!NOTE]
    >VM에서 모니터링 데이터를 저장할 새 Log Analytics 작업 영역을 참조 하세요 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)합니다. Log Analytics 작업 영역 중 하나에 속해 있어야 합니다 [지원 되는 지역](vminsights-enable-overview.md#log-analytics)합니다.

모니터링을 활성화 한 후 VM에 대 한 상태 메트릭을 보려면 먼저 10 분 정도 대기 해야 합니다.

![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>단일 가상 머신 확장 집합에 대 한 모니터링을 사용 하도록 설정

모니터링을 사용 하도록 Azure 가상 머신 확장 집합입니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 선택 **Virtual Machine Scale Sets**합니다.

3. 목록에서 가상 머신 확장 집합을 선택 합니다.

4. 가상 컴퓨터 확장 설정 페이지에는 **모니터링** 섹션에서 **Insights (미리 보기)** 합니다.

5. 에 **Insights (미리 보기)** 페이지에서, 기존 Log Analytics 작업 영역을 사용 하 여 드롭다운 목록에서 선택 하려는 경우.

    목록에는 기본 작업 영역 및 구독에서 VM에 배포 되는 위치를 미리 선택 합니다. 

    ![가상 머신 확장 집합 Vm에 대 한 Azure Monitor를 사용 하도록 설정](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >가상 머신 확장 집합에서 모니터링 데이터를 저장할 새 Log Analytics 작업 영역을 참조 하세요 [Log Analytics 작업 영역 만들기](../learn/quick-create-workspace.md)합니다. Log Analytics 작업 영역 중 하나에 속해 있어야 합니다 [지원 되는 지역](vminsights-enable-overview.md#log-analytics)합니다.

모니터링을 활성화 한 후에 확장 집합에 대 한 모니터링 데이터를 보려면 먼저 10 분 정도 기다립니다 해야 합니다.

>[!NOTE]
>확장 집합에 대 한 수동 업그레이드 모델을 사용 하는 경우 설치를 완료 하려면 인스턴스를 업그레이드 합니다. 업그레이드를 시작할 수 있습니다 합니다 **인스턴스** 페이지의 **설정** 섹션입니다.

![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

이제 VM 또는 가상 머신 확장 집합에 대 한 모니터링을 활성화 한 모니터링 정보는 Vm에 대 한 Azure Monitor에서 분석에 사용할 수입니다. 

## <a name="next-steps"></a>다음 단계

* 상태 기능을 사용 하는 방법에 알아보려면 참조 [Azure Monitor Vm의 상태를 파악할](vminsights-health.md)합니다. 
* 검색 된 응용 프로그램 종속성을 보려면을 참조 하세요 [Vm 맵에 대 한 Azure Monitor를 사용 하 여](vminsights-maps.md)입니다. 
* 병목 상태, 전체적인 사용률 및 VM의 성능 식별, 참조 [보기 Azure VM의 성능](vminsights-performance.md)합니다.