---
title: VM용 Azure Monitor(미리 보기)란? | Microsoft Docs
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 애플리케이션 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure Monitor의 기능입니다. 이 문서에서는 개요를 제공합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 84ebf92f795dfe1a21570d782bf24fad27f50eda
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109080"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>VM용 Azure Monitor(미리 보기)란?

VM용 Azure Monitor는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다. 

여기에는 온-프레미스 또는 다른 클라우드 공급자에서 호스트 되는 Vm에 대 한 성능 및 응용 프로그램 종속성 모니터링에 대 한 지원이 포함 됩니다. 다음과 같은 주요 기능에 대 한 자세한 정보를 제공 합니다.

- **미리 정의 된 추세 성능 차트**: 게스트 VM 운영 체제에서 코어 성능 메트릭을 표시 합니다.

- **종속성 맵**: 다양 한 리소스 그룹 및 구독의 VM과 상호 연결 된 구성 요소를 표시 합니다.  

>[!NOTE]
>최근에 공개 미리 보기 고객 으로부터 받은 피드백에 따라 상태 기능에 대 한 [변경을 발표](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) 했습니다. 변경 되는 내용 수를 고려 하 여 새 고객을 위한 상태 기능 제공을 중지할 예정입니다. 기존 고객은 상태 기능을 계속 사용할 수 있습니다. 자세한 내용은 [일반적인 가용성 FAQ](vminsights-ga-release-faq.md)를 참조 하세요.  

Azure Monitor 로그와 통합하면 강력한 집계, 필터링을 제공하고, 시간에 따른 데이터 추세를 분석할 수 있습니다. Azure Monitor 또는 서비스 맵 단독으로는 워크로드를 포괄적으로 모니터링할 수 없습니다.  

가상 컴퓨터에서 직접 단일 VM의이 데이터를 보거나, Azure Monitor를 사용 하 여 뷰가 Azure 리소스 컨텍스트 또는 작업 영역 컨텍스트 모드를 지 원하는 Vm의 집계 된 보기를 제공할 수 있습니다. 자세한 내용은 [액세스 모드 개요](../platform/design-logs-deployment.md#access-mode)를 참조 하세요.

![Azure Portal의 가상 머신 인사이트 큐브 뷰](./media/vminsights-overview/vminsights-azmon-directvm.png)

VM용 Azure Monitor는 중요한 애플리케이션의 예측 가능한 성능 및 가용성을 제공할 수 있습니다. 성능 병목 현상 및 네트워크 문제를 식별 합니다. 또한 VM용 Azure Monitor는 문제가 다른 종속성과 관련이 있는지 여부를 이해하는 데도 도움이 됩니다.  

## <a name="data-usage"></a>데이터 사용량

VM용 Azure Monitor를 배포하면 VM에서 수집한 데이터가 Azure Monitor에 수집 및 저장됩니다. 수집 된 성능 및 종속성 데이터는 Log Analytics 작업 영역에 저장 됩니다. [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 게시된 가격 책정에 따라, Azure Monitor용 VM에 다음에 대한 요금이 청구됩니다.

- 수집 및 저장된 데이터
- 만들어진 경고 규칙
- 전송되는 알림 

로그 크기는 성능 카운터의 문자열 길이에 따라 달라 지 며 VM에 할당 된 논리 디스크 및 네트워크 어댑터의 수에 따라 증가할 수 있습니다. 이미 작업 영역이 있고 이러한 카운터를 수집 중인 경우 요금이 중복 적용되지 않습니다. 이미 서비스 맵을 사용 중인 경우 Azure Monitor로 전송되는 추가 연결 데이터만 변경됩니다.

## <a name="next-steps"></a>다음 단계

가상 머신을 모니터링하는 데 도움이 되는 요구 사항과 메서드를 이해하려면 [VM용 Azure Monitor 배포](vminsights-enable-overview.md)를 검토하세요.
