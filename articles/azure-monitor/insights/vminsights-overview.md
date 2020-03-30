---
title: VM용 Azure Monitor란?
description: 응용 프로그램 구성 요소 및 해당 종속성을 자동으로 검색하고 매핑하는 것 외에도 Azure VM의 상태 및 성능을 모니터링하는 VM용 Azure 모니터 의 개요입니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480490"
---
# <a name="what-is-azure-monitor-for-vms"></a>VM용 Azure Monitor란?

VM용 Azure Monitor는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다. 여기에는 온-프레미스 또는 다른 클라우드 공급자에서 호스팅되는 VM에 대한 성능 및 응용 프로그램 종속성을 모니터링하는 지원이 포함됩니다. 다음 주요 기능은 심층적인 통찰력을 제공합니다.

- **미리 정의된 추세 성능 차트**: 게스트 VM 운영 체제의 핵심 성능 메트릭을 표시합니다.

- **종속성 맵**: 다양한 리소스 그룹 및 구독에서 VM과 상호 연결된 구성 요소를 표시합니다.  

>[!NOTE]
>최근 공개 미리 보기 고객으로부터 받은 피드백을 기반으로 건강 기능에 [대한 변경 사항을 발표했습니다.](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) 변경 횟수를 감안할 때 신규 고객을 위한 Health 기능 제공을 중단할 예정입니다. 기존 고객은 상태 기능을 계속 사용할 수 있습니다. 자세한 내용은 일반 사용 [가능 여부 FAQ를](vminsights-ga-release-faq.md)참조하십시오.  

Azure Monitor Logs와의 통합은 강력한 집계 및 필터링을 제공하므로 VM용 Azure Monitor가 시간에 따라 데이터 추세를 분석할 수 있습니다. 가상 컴퓨터에서 직접 단일 VM에서 이 데이터를 보거나 Azure Monitor를 사용하여 뷰가 Azure 리소스 컨텍스트 또는 작업 영역 컨텍스트 모드를 지원하는 VM의 집계된 보기를 제공할 수 있습니다. 자세한 내용은 [액세스 모드 개요를](../platform/design-logs-deployment.md#access-mode)참조하십시오.

![Azure Portal의 가상 머신 인사이트 큐브 뷰](media/vminsights-overview/vminsights-azmon-directvm.png)

VM용 Azure Monitor는 중요한 애플리케이션의 예측 가능한 성능 및 가용성을 제공할 수 있습니다. 성능 병목 현상 및 네트워크 문제를 식별하고 문제가 다른 종속성과 관련이 있는지 여부를 이해하는 데도 도움이 될 수 있습니다.  

## <a name="data-usage"></a>데이터 사용량

VM용 Azure Monitor를 배포하면 VM에서 수집한 데이터가 Azure Monitor에 수집 및 저장됩니다. 수집된 성능 및 종속성 데이터는 Log Analytics 작업 영역에 저장됩니다. [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시된 가격 책정에 따라, Azure Monitor용 VM에 다음에 대한 요금이 청구됩니다.

- 수집 및 저장된 데이터
- 만들어진 경고 규칙
- 전송되는 알림 

로그 크기는 성능 카운터의 문자열 길이에 따라 다르며 VM에 할당된 논리 디스크 및 네트워크 어댑터 의 수에 따라 증가할 수 있습니다. 이미 작업 영역이 있고 이러한 카운터를 수집 중인 경우 요금이 중복 적용되지 않습니다. 이미 서비스 맵을 사용하고 있는 경우 Azure Monitor로 전송되는 추가 연결 데이터만 표시됩니다.

## <a name="next-steps"></a>다음 단계

가상 머신을 모니터링하는 데 도움이 되는 요구 사항과 메서드를 이해하려면 [VM용 Azure Monitor 배포](vminsights-enable-overview.md)를 검토하세요.
