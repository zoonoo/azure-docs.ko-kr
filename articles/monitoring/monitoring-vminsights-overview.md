---
title: VM용 Azure Monitor란? | Microsoft Docs
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 응용 프로그램 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure Monitor의 기능입니다. 이 문서에서는 개요를 제공합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 79f507c342f5a13c4d3784cf312f0bf8aeffa3e3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957258"
---
## <a name="what-is-azure-monitor-for-vms"></a>VM용 Azure Monitor란?

VM용 Azure Monitor는 Windows 및 Linux VM의 성능과 상태를 분석하여 대규모 Azure VM(가상 머신)을 모니터링하며, 여기에는 VM의 여러 프로세스, 그리고 다른 리소스 및 외부 프로세스에 대한 상호 연결된 종속성이 포함됩니다. 이 솔루션에는 온-프레미스 또는 다른 클라우드 공급자에 호스트되는 VM의 성능 및 응용 프로그램 종속성 모니터링에 대한 지원이 포함됩니다.  심층 인사이트를 제공하는 세 가지 핵심 기능이 포함되어 있습니다.

* 평가 조건이 충족되면 미리 구성된 상태 조건 집합과 경고에 따라 Windows 및 Linux 운영 체제를 실행하는 Azure VM의 논리적 구성 요소를 측정합니다.  
* 게스트 VM 운영 체제의 프로세서, 메모리, 디스크 및 네트워크 어댑터에서 핵심 성능 메트릭을 수집하여 미리 정의된 추세 성능 차트에 표시합니다.
* 종속성 맵은 여러 리소스 그룹 및 구독에서 해당 VM과 상호 연결된 것으로 발견된 구성 요소를 표시합니다.  

이러한 기능은 다음과 같은 세 가지 관점으로 구성됩니다.

* 상태
* 성능
* Map

>[!NOTE]
>현재 상태 기능은 Azure 가상 머신에만 제공됩니다.
>

Log Analytics와 통합하면 강력한 집계, 필터링 및 시간에 따른 데이터 추세 분석을 수행하는 기능이 제공됩니다. Azure Monitor, 서비스 맵 또는 Log Analytics 단독으로는 워크로드를 포괄적으로 모니터링할 수 없습니다.  

가상 머신에서 직접 단일 VM의 컨텍스트에서 이 데이터를 볼 수도 있고, 각 기능에 대한 다음 큐브 뷰에 따라 VM의 집계 보기를 제공하는 Azure Monitor를 사용할 수도 있습니다.

* 상태 - 리소스 그룹과 관련된 VM
* 맵 및 성능 - 특정 Log Analytics 작업 영역에 보고하도록 구성된 VM

![포털의 가상 머신 인사이트 큐브 뷰](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps는 중요한 운영 체제 이벤트, 성능 병목 상태 및 네트워크 문제를 식별하고 문제가 다른 종속성과 관련되어 있는지 확인하여 중요한 응용 프로그램의 예측 가능한 성능 및 가용성을 효과적으로 제공할 수 있습니다.  

## <a name="data-usage"></a>데이터 사용량 

VM용 Azure Monitor를 등록하는 즉시, VM에서 수집한 데이터가 Azure Monitor에 수집 및 저장됩니다.  VM용 Azure Monitor는 Azure Monitor [가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시된 가격 책정에 따라 수집 및 보존되는 데이터, 모니터링되는 상태 조건 메트릭 시계열의 수, 생성된 경고 규칙, 전송된 알림에 대한 요금이 청구됩니다.

로그 크기는 카운터의 문자열 길이에 따라 달라지며, 논리 디스크 및 네트워크 어댑터 수를 늘릴 수 있습니다.  이미 작업 영역이 있고 이러한 카운터를 수집 중인 경우 요금이 중복 적용되지 않습니다.  이미 서비스 맵을 사용 중인 경우 Azure Monitor로 전송되는 추가 연결 데이터만 변경됩니다.

## <a name="next-steps"></a>다음 단계
[VM용 Azure Monitor를 등록](monitoring-vminsights-onboard.md)하여 Azure 가상 머신 모니터링을 시작합니다.
