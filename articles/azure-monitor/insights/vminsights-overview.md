---
title: VM용 Azure Monitor(미리 보기)란? | Microsoft Docs
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 애플리케이션 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure Monitor의 기능입니다. 이 문서에서는 개요를 제공합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: a60413ee6614b638db58748ee2c0aea5d7ea32ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199926"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>VM용 Azure Monitor(미리 보기)란?

VM용 Azure Monitor는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다. 

VM용 Azure Monitor 솔루션에는 온-프레미스 또는 다른 클라우드 공급자에 호스트되는 VM의 성능 및 애플리케이션 종속성 모니터링에 대한 지원이 포함됩니다. 다음 세 가지 주요 기능이 심층적인 인사이트를 제공합니다.

* **Windows 및 Linux를 실행하는 Azure VM의 논리적 구성 요소**: 미리 구성된 상태 기준에 따라 측정되고 평가 조건이 충족되면 경고합니다.  

* **미리 정의 된 최신 성능 차트**: 게스트 VM 운영 체제의 핵심 성능 메트릭을 표시합니다.

* **종속성 맵**: 다양한 리소스 그룹 및 구독에서 VM과 상호 연결된 구성 요소를 표시합니다.  

이러한 기능은 다음과 같은 세 가지 큐브 뷰로 구성됩니다.

* 상태
* 성능
* 매핑

>[!NOTE]
>현재 상태 기능을 Azure 가상 머신에 대해서만 제공 됩니다. 성능 및 지도 기능 환경 또는 다른 클라우드 공급자의 Azure Vm, Azure VM scale sets 및 호스팅되는 virtual machines를 지원 합니다.

Azure Monitor 로그와 통합하면 강력한 집계, 필터링을 제공하고, 시간에 따른 데이터 추세를 분석할 수 있습니다. Azure Monitor 또는 서비스 맵 단독으로는 워크로드를 포괄적으로 모니터링할 수 없습니다.  

단일 VM의 이러한 데이터를 가상 머신에서 직접 볼 수 있으며, Azure Monitor를 사용하여 VM의 집계 뷰를 제공할 수 있습니다. 이 뷰는 각 기능의 다음 큐브 뷰를 기준으로 합니다.

* **상태**: VM이 리소스 그룹과 관련되어 있습니다.
* **맵** 및 **성능**: VM이 특정 Log Analytics 작업 영역에 보고하도록 구성됩니다.

![Azure Portal의 가상 머신 인사이트 큐브 뷰](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

VM용 Azure Monitor는 중요한 애플리케이션의 예측 가능한 성능 및 가용성을 제공할 수 있습니다. 중요한 운영 체제 이벤트, 성능 병목 상태 및 네트워크 문제를 식별합니다. 또한 VM용 Azure Monitor는 문제가 다른 종속성과 관련이 있는지 여부를 이해하는 데도 도움이 됩니다.  

## <a name="data-usage"></a>데이터 사용량 

VM용 Azure Monitor를 배포하면 VM에서 수집한 데이터가 Azure Monitor에 수집 및 저장됩니다. 시계열 데이터베이스에 상태 조건을 메트릭은 Azure Monitor에 저장 됩니다, 그리고 수집 된 성능 및 종속성 데이터는 Log Analytics 작업 영역에 저장 됩니다. [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시된 가격 책정에 따라, Azure Monitor용 VM에 다음에 대한 요금이 청구됩니다.

* 수집 및 저장된 데이터
* 모니터링되는 상태 조건 메트릭 시계열의 수
* 만들어진 경고 규칙
* 전송되는 알림 

성능 카운터의 문자열 길이 따라 로그 크기 및 개수 논리 디스크 및 VM에 할당 된 네트워크 어댑터를 사용 하 여 늘릴 수 있습니다. 이미 작업 영역이 있고 이러한 카운터를 수집 중인 경우 요금이 중복 적용되지 않습니다. 이미 서비스 맵을 사용 중인 경우 Azure Monitor로 전송되는 추가 연결 데이터만 변경됩니다.

## <a name="next-steps"></a>다음 단계
가상 머신을 모니터링하는 데 도움이 되는 요구 사항과 메서드를 이해하려면 [VM용 Azure Monitor 배포](vminsights-onboard.md)를 검토하세요.
