---
title: Virtual Machines에서 변동 패턴 모니터링
description: Azure Site Recovery를 사용하여 보호되는 Virtual Machines의 변동 패턴을 모니터링하는 방법 알아보기
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359869"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>Virtual Machines에서 변동 패턴 모니터링

이 문서에서는 Virtual Machines에서 변동 패턴을 모니터링하는 데 사용할 수 있는 다양한 도구에 대한 개요를 제공합니다. 적절한 도구를 사용하여 높은 변동의 원인이 되는 애플리케이션을 정확하게 확인하고 해당 애플리케이션에 대한 추가 작업을 용이하게 수행할 수 있습니다.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>Azure Virtual Machines(Windows 또는 Linux)의 경우

컴퓨터가 Azure에서 호스트되고 스토리지에 대해 관리되거나 관리되지 않는 디스크를 사용하는 경우 디스크 메트릭을 추적하여 성능을 쉽게 추적할 수 있습니다. 이를 통해 애플리케이션 사용 패턴에 맞게 적절한 디스크 선택을 면밀하게 모니터링하고 만들 수 있습니다. 또한 경고, 진단 및 컴파일 자동화를 만드는 데 사용할 수 있습니다. [자세히 알아봅니다](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

Azure Site Recovery를 사용하여 컴퓨터를 보호한 후에는 Azure Monitor 로그 및 Log Analytics를 사용하여 컴퓨터를 모니터링할 수 있습니다. [자세히 알아봅니다](./monitor-log-analytics.md).

또한 사용할 수 있는 운영 체제 별 도구도 있습니다.

## <a name="for-windows-machines"></a>Windows 컴퓨터의 경우

컴퓨터가 온-프레미스에 있거나 Windows 운영 체제를 실행하는 컴퓨터가 없는 경우 몇 가지 도구를 사용할 수 있습니다.

작업 관리자에서 디스크 사용을 확인하는 것 외에는 항상 **리소스 모니터** 및 **성능 모니터** 를 참조할 수 있습니다. 이러한 도구는 Windows 컴퓨터에 이미 존재합니다.

### <a name="resource-monitor"></a>리소스 모니터

**리소스 모니터** 는 하드웨어 및 소프트웨어 리소스 사용에 대한 정보를 실시간으로 표시합니다. Windows 컴퓨터에서 리소스 모니터를 실행하려면 다음 단계를 수행합니다

1. Win + R을 누르고 _resmon_ 을 입력합니다.
1. Resmon, 즉 리소스 모니터 창이 열리면 디스크 탭으로 전환됩니다. 다음 뷰를 제공합니다

    ![리소스 모니터 디스크 탭](./media/monitoring-high-churn/resmon-disk-tab.png)

1. 이 탭은 명확한 그림을 가져오기 위해 특정 시간 동안 계속 모니터링하여야 합니다. 위의 예에서는 _wmiprv.exe_ 변동이 높습니다.

컴퓨터에서 이탈이 높은 애플리케이션을 식별한 후에는 필요한 작업을 수행하여 해당 애플리케이션과 관련된 변동 사항을 다룰 수 있습니다.

### <a name="performance-monitor"></a>성능 모니터링

**성능 모니터링** 은 CPU 또는 메모리 사용량과 같은 컴퓨터의 다양한 작업을 모니터링합니다. Windows 컴퓨터에서 성능 모니터링을 실행하려면 다음 단계를 수행합니다

1. Win + R을 누르고 _perfmon_ 을 입력합니다.
1. Perfmon, 즉 성능 모니터 창이 열리면 다음 보기를 제공합니다

    ![성능 모니터 1단계](./media/monitoring-high-churn/perfmon-step1.png)

1. 오른쪽의 **모니터링 도구** 폴더를 확장하고 성능 모니터를 클릭합니다. 그러면 현재 성능에 대한 실시간 정보를 제공하는 아래 뷰가 열립니다

    ![성능 모니터 2단계](./media/monitoring-high-churn/perfmon-step1.png)

1. 이 그래프는 현재 모니터 하나(즉, '% Processor Time ')를 모니터링하고 있습니다. 그래프 바로 아래에 있는 테이블에 표시됩니다. 도구 맨 위에 있는 **'+'** 를 클릭하여 모니터링할 항목을 더 추가할 수 있습니다.
1. 다음은 카운터를 추가하면 성능 모니터가 어떻게 표시하는지에 대한 시각적 개체입니다

    ![성능 모니터 3단계](./media/monitoring-high-churn/perfmon-step3.png)

[여기](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data)에서 성능 모니터에 대한 자세한 정보를 알아봅니다.

## <a name="for-linux-machines"></a>Linux 컴퓨터의 경우

Linux 운영 체제를 실행하는 컴퓨터가 온-프레미스에 있거나, 그렇지 않은 경우 변동 패턴을 모니터링하는 데 사용할 수 있는 몇 가지 도구가 있습니다.

### <a name="iotop"></a>Iotop

가장 많이 사용되는 도구 중 하나는 _iotop_ 입니다. 실시간 디스크 작업을 표시하는 유틸리티입니다. I/o를 수행하는 프로세스를 사용 중인 디스크 대역폭과 함께 나열할 수 있습니다.

명령 프롬프트를 열고 다음 명령`iotop`을 실행합니다.

### <a name="iostat"></a>Iostat

IoStat는 시스템 입력 및 출력 저장 디바이스 통계를 수집하고 표시하는 간단한 도구입니다. 이 도구는 디바이스, 로컬 디스크, 원격 디스크 등의 저장디바이스 성능 문제를 추적하는 데 주로 사용됩니다.

명령 프롬프트를 열고 다음 명령`iostat`을 실행합니다.

## <a name="next-steps"></a>다음 단계

[Azure Monitor](monitor-log-analytics.md)를 사용하여 모니터링하는 방법을 알아봅니다.