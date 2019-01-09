---
title: Azure Stack에서 azure Monitor | Microsoft Docs
description: Azure Stack에서 Azure Monitor에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: mabrigg
ms.openlocfilehash: cfeb1ce7791db463dc0ac39376baa8228dd96daf
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106293"
---
# <a name="azure-monitor-on-azure-stack"></a>Azure Stack에서 azure Monitor

*적용 대상: Azure Stack 통합 시스템*

이 문서에서는 Azure Stack에서 Azure Monitor 서비스의 개요를 제공 합니다. Azure Monitor의 작업 및 Azure Stack에서 Azure Monitor를 사용 하는 방법에 대 한 추가 정보에 설명 합니다. 

에 대 한 개요 및 Azure Monitor 시작, 전역 Azure 문서를 참조 하는 방법 [Azure Monitor 시작](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)합니다.

![Azure Stack 모니터 블레이드](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor는 Azure 리소스를 모니터링하는 단일 원본이 되는 플랫폼 서비스입니다. Azure에서 Azure Monitor를 통해 리소스의 메트릭과 로그에 대해 시각화, 쿼리, 라우팅, 보관 및 조치를 수행할 수 있습니다. Azure Stack 관리 포털 Monitor PowerShell Cmdlet, Cross-platform CLI 또는 Azure Monitor REST Api를 사용 하 여이 데이터로 작업할 수 있습니다. Azure Stack에서 지원 되는 특정 연결을 참조 하세요. [Azure Stack에서 모니터링 데이터를 사용 하는 방법](azure-stack-metrics-monitor.md)

> [!Note]  
메트릭 및 진단 로그는 Azure Stack 개발 키트에 대해 사용할 수 없는 경우

## <a name="prerequisites"></a>필수 조건

등록 된 **Microsoft.insights** 구독의 제안 리소스 공급자 설정에 대 한 리소스 공급자입니다. 리소스 공급자를 구독에 연결 된 제품에서 사용할 수 있는지 확인할 수 있습니다.

1. Azure Stack 관리 포털을 엽니다.
2. 선택 **제공**합니다.
3. 구독과 연결 된 제품을 선택 합니다.
4. 선택 **리소스 공급자** 아래에서 **설정 합니다.** 
5. 찾을 **Microsoft.Insights** 목록에서 상태 인지 확인 하 고 **Registered.** 합니다.

## <a name="overview"></a>개요

Azure에서 Azure Monitor와 같은 Azure Stack에서 Azure Monitor 대부분의 서비스에 대 한 기본 수준의 인프라 메트릭과 로그 제공합니다.

## <a name="azure-monitor-sources-compute-subset"></a>Azure monitor 원본의: 하위 집합을 계산

![Azure monitor 원본-compute 하위 집합](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

합니다 **Microsoft.Compute** Azure Stack에서 리소스 공급자를 포함 합니다.
 - Virtual Machines 
 - Virtual Machines 확장 집합

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>응용 프로그램-진단 로그, 응용 프로그램 로그 및 메트릭

응용 프로그램 VM의 OS에서 실행할 수 있습니다 사용 하 여 실행 합니다 **Microsoft.Compute** 리소스 공급자입니다. 이러한 응용 프로그램 및 Vm 자체 로그 및 메트릭 집합을 내보냅니다. Azure Monitor는 Azure 진단 확장(Windows 또는 Linux)을 사용하여 대부분의 애플리케이션 수준 메트릭과 로그를 수집합니다. 

측정값의 유형은 다음과 같습니다.
 - 성능 카운터
 - 애플리케이션 로그
 - Windows 이벤트 로그
 - .NET 이벤트 원본
 - IIS 로그
 - 매니페스트 기반 ETW
 - 크래시 덤프
 - 고객 오류 로그

> [!Note]  
> Azure Stack에 Linux 진단 확장을 사용 하는 것이 없습니다.

### <a name="host-and-guest-vm-metrics"></a>호스트 및 게스트 VM 메트릭

앞에 나열 된 계산 리소스에는 전용된 호스트 VM과 게스트 OS에 있습니다. VM 호스트 및 게스트 OS 루트 VM 및 Hyper-v 하이퍼바이저에서 게스트 VM의 동일 합니다. VM 호스트와 게스트 OS에 대 한 메트릭을 수집할 수 있습니다. 또한 게스트 OS에 대 한 진단 로그를 수집할 수 있습니다. Azure Stack에서 호스트 및 게스트 VM 메트릭 수집 가능한 메트릭 목록에서 사용할 수 있습니다 [Azure Stack에서 지원 되는 Azure Monitor 메트릭](azure-stack-metrics-supported.md)합니다. 

### <a name="activity-log"></a>활동 로그

Azure Stack 인프라에서 볼 수 있듯이 계산 리소스에 대 한 자세한 내용은 활동 로그를 검색할 수 있습니다. 이러한 로그는 리소스가 생성되거나 소멸된 시간과 같은 정보를 포함합니다. Azure Stack에서 활동 로그는 Azure와 일치 합니다. 자세한 내용은 참조에 대 한 설명을 [azure 활동 로그 개요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)합니다. 


## <a name="azure-monitor-sources-everything-else"></a>Azure monitor 원본의: 기타 등등

![Azure monitor 소스-기타 등등](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>리소스-메트릭 및 진단 로그

수집 가능한 메트릭 및 진단 로그는 리소스 종류에 따라 달라 집니다. Azure Stack에서 각 리소스에 대 한 수집 가능한 메트릭의 목록은 지원 되는 메트릭에서 제공 됩니다. 자세한 내용은 [Azure Stack에서 지원 되는 Azure Monitor 메트릭](azure-stack-metrics-supported.md)합니다.

### <a name="activity-log"></a>활동 로그

활동 로그에 대 한 동일한 계산 리소스입니다. 

### <a name="uses-for-monitoring-data"></a>모니터링 데이터 사용

**저장 및 보관**  

일부 모니터링 데이터는 이미 설정된 시간 동안 Azure Monitor에 저장되어 사용할 수 있습니다. 
 - 메트릭은 90일 동안 저장됩니다. 
 - 활동 로그 항목은 90일 동안 저장됩니다. 
 - 진단 로그 저장 되지 않습니다.
 - 장기 보존을 위해 저장소 계정에 데이터를 보관 합니다.

**쿼리**  

시스템 또는 Azure storage 데이터에 액세스 하는 Azure Monitor REST API, 플랫폼 간 CLI (명령줄 인터페이스) 명령, PowerShell cmdlet 또는.NET SDK를 사용할 수 있습니다. 

**시각화**

모니터링 데이터를 그래픽과 차트로 시각화하면 데이터 자체를 살펴보는 것보다 훨씬 빠르게 추세를 파악할 수 있습니다. 

몇 가지 시각화 방법은 다음과 같습니다.
 - Azure Stack 사용자 및 관리자 포털 사용
 - Microsoft Power BI로 데이터 라우팅
 - 라이브 스트리밍 또는 Azure 저장소의 보관 파일에서 데이터를 읽는 도구를 사용하여 타사 시각화 도구로 데이터 라우팅

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Azure Stack에서 Azure에 액세스 하는 방법을 모니터링합니다

일반적으로 다음 방법 중 하나를 사용하여 데이터 추적, 라우팅 및 검색을 조작할 수 있습니다. 일부 방법은 일부 동작 및 데이터 형식에 사용할 수 없습니다.

 - [Azure Stack 포털](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [플랫폼 간 명령줄 Interface(CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>다음 단계

문서에서 Azure Stack에서 모니터링 데이터 소비 옵션에 자세히 알아보려면 [Azure Stack에서 모니터링 데이터 사용](azure-stack-metrics-monitor.md)합니다.
