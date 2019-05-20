---
title: 평가 위한 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 단일 Azure 가상 컴퓨터 또는 가상 머신 확장 집합 평가 목적에 대 한 Vm에 대 한 Azure Monitor을 사용 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524087"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>평가 위한 Vm (미리 보기)에 대 한 Azure Monitor를 사용 하도록 설정

적은 수의 Azure virtual machines 또는 단일 가상 머신 또는 가상 머신 확장 집합의 Vm (미리 보기)에 대 한 Azure Monitor를 평가 하려면 모니터링을 사용 하도록 설정 하는 쉽고 가장 직접적인 방법은 Azure portal에서 됩니다. 이 프로세스의 끝은 성공적으로 셨을 모니터링 하 고 하는 경우 이러한 문제가 발생 한 성능 또는 가용성에 알아봅니다. 

가져오기 시작 검토 해야 하기 전에 합니다 [필수 구성 요소](vminsights-enable-overview.md) 구독 및 리소스 요구 사항을 충족 확인 합니다.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>단일 Azure VM에 대 한 모니터링을 사용 하도록 설정
Azure Portal에서 Azure VM의 모니터링을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Virtual Machines**를 선택합니다.

1. 목록에서 VM을 선택합니다.

1. VM 페이지에 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.

1. **인사이트(미리 보기)** 페이지에서 **지금 시도해 보기**를 선택합니다.

    ![VM에 대해 VM용 Azure Monitor를 사용하도록 설정](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. **Azure Monitor Insights 등록** 페이지에서 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 드롭다운 목록에서 해당 작업 영역을 선택합니다.  

    목록에는 구독에서 가상 머신이 배포된 기본 작업 영역 및 위치가 미리 선택되어 있습니다. 

    >[!NOTE]
    >VM에서 모니터링 데이터를 저장 하기 위한 새 Log Analytics 작업 영역을 만들려는 경우의 지침을 따릅니다 [Log Analytics 작업 영역을 만들](../../azure-monitor/learn/quick-create-workspace.md) 지원 되는 지역 중 하나에 나열 된 [여기](vminsights-enable-overview.md#log-analytics)합니다.

모니터링을 사용하도록 설정하고 약 10분 후에 가상 머신에 대한 상태 메트릭을 볼 수 있습니다.

![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>단일 가상 머신 확장 집합에 대 한 모니터링을 사용 하도록 설정

Azure portal에서 설정 하 여 Azure 가상 머신 확장의 모니터링을 사용 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 선택 **Virtual Machine Scale Sets**합니다.

3. 목록에서 가상 머신 확장 집합을 선택 합니다.

4. 가상 컴퓨터 확장 설정 페이지에는 **모니터링** 섹션에서 **Insights (미리 보기)** 합니다.

5. 에 **Insights (미리 보기)** 페이지를 사용 하 여 드롭다운 목록에서 선택 하려는 기존 Log Analytics 작업 영역에 있는 경우.

    목록에는 구독에서 가상 머신이 배포된 기본 작업 영역 및 위치가 미리 선택되어 있습니다. 

    ![가상 머신 확장 집합 Vm에 대 한 Azure Monitor를 사용 하도록 설정](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >VM에서 모니터링 데이터를 저장 하기 위한 새 Log Analytics 작업 영역을 만들려는 경우의 지침을 따릅니다 [Log Analytics 작업 영역을 만들](../learn/quick-create-workspace.md) 지원 되는 지역 중 하나에 나열 된 [여기](vminsights-enable-overview.md#log-analytics)합니다.

모니터링을 활성화 한 후 확장 집합에 대 한 모니터링 데이터를 보려면 먼저 10 분 정도 걸릴 수 있습니다.

>[!NOTE]
>확장 집합 수동 업그레이드 모델을 사용 하는 경우 설치를 완료 하려면 인스턴스를 업그레이드 해야 합니다.  인스턴스 페이지 아래에서이 작업을 수행할 수 있습니다 합니다 **설정을** 섹션입니다.

![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>다음 단계

가상 머신 또는 가상 머신 확장 집합에 대 한 모니터링이 활성화 했으므로이 정보는 Vm에 대 한 Azure Monitor를 사용 하 여 분석을 위해 사용할 수 있습니다. 상태 기능을 사용하는 방법을 알아보려면 [VM용 Azure Monitor 상태 보기](vminsights-health.md)를 참조하세요. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요. 병목 상태 및 VM 성능에 대한 전반적인 사용률을 확인하려면 [Azure VM 성능 보기](vminsights-performance.md)를 참조하세요. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.