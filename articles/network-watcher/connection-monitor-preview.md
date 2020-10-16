---
title: 연결 모니터 (미리 보기) | Microsoft Docs
description: 연결 모니터 (미리 보기)를 사용 하 여 분산 환경에서 네트워크 통신을 모니터링 하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 80934dca73d7f8a205c62a49c418828cab1820e7
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123735"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>연결 모니터를 사용 하 여 네트워크 연결 모니터링 (미리 보기)

연결 모니터 (미리 보기)는 Azure Network Watcher의 통합 종단 간 연결 모니터링을 제공 합니다. 연결 모니터 (미리 보기) 기능은 하이브리드 및 Azure 클라우드 배포를 지원 합니다. Network Watcher는 Azure 배포에 대 한 연결 관련 메트릭을 모니터링 하 고, 진단 하 고, 볼 수 있는 도구를 제공 합니다.

다음은 연결 모니터 (미리 보기)에 대 한 몇 가지 사용 사례입니다.

- 프런트 엔드 웹 서버 VM은 다중 계층 응용 프로그램에서 데이터베이스 서버 VM과 통신 합니다. 두 Vm 간의 네트워크 연결을 확인 하려고 합니다.
- 미국 동부 지역의 Vm에서 미국 중부 지역의 vm을 ping 하 고 지역 간 네트워크 대기 시간을 비교 하려고 합니다.
- 시애틀, 워싱턴 및 Ashburn (버지니아)에 여러 온-프레미스 사무실 사이트가 있습니다. Office 사이트는 Microsoft 365 Url에 연결 됩니다. Microsoft 365 Url의 사용자에 대해 시애틀와 Ashburn 사이의 대기 시간을 비교 합니다.
- 하이브리드 응용 프로그램은 Azure Storage 끝점에 연결 해야 합니다. 온-프레미스 사이트와 Azure 응용 프로그램은 동일한 Azure Storage 끝점에 연결 됩니다. 온-프레미스 사이트의 대기 시간을 Azure 응용 프로그램의 대기 시간과 비교 하려고 합니다.
- 온-프레미스 설정과 클라우드 응용 프로그램을 호스트 하는 Azure Vm 간의 연결을 확인 하려고 합니다.

미리 보기 단계에서 연결 모니터는 Network Watcher [연결 모니터](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) 기능과 네트워크 성능 모니터 (NPM) [서비스 연결 모니터](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity), [Express 경로 모니터링](https://docs.microsoft.com/azure/expressroute/how-to-npm)및 [성능 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-performance-monitor) 기능과 같은 두 가지 기능을 결합 합니다.

연결 모니터 (미리 보기)의 몇 가지 이점은 다음과 같습니다.

* Azure 및 하이브리드 모니터링 요구 사항에 대 한 통합 된 직관적인 환경
* 영역 간, 작업 영역 간 연결 모니터링
* 검색 빈도가 높고 네트워크 성능에 대 한 가시성 향상
* 하이브리드 배포에 대 한 더 빠른 경고
* HTTP, TCP 및 ICMP를 기반으로 하는 연결 확인 지원 
* Azure 및 비 Azure 테스트에 대 한 메트릭 및 Log Analytics 지원

![연결 모니터가 Azure Vm, 비 Azure 호스트, 끝점 및 데이터 저장소 위치와 상호 작용 하는 방법을 보여 주는 다이어그램](./media/connection-monitor-2-preview/hero-graphic.png)

모니터링을 위해 연결 모니터 (미리 보기) 사용을 시작 하려면 다음 단계를 수행 합니다. 

1. 모니터링 에이전트를 설치 합니다.
1. 구독에서 Network Watcher를 사용 하도록 설정 합니다.
1. 연결 모니터를 만듭니다.
1. 데이터 분석 및 경고를 설정 합니다.
1. 네트워크의 문제를 진단 합니다.

다음 섹션에서는 이러한 단계에 대 한 세부 정보를 제공 합니다.

## <a name="install-monitoring-agents"></a>모니터링 에이전트 설치

연결 모니터는 간단한 실행 파일을 사용 하 여 연결 확인을 실행 합니다.  Azure 환경 및 온-프레미스 환경에서 연결 확인을 지원 합니다. 사용 하는 실행 파일은 VM이 Azure 또는 온-프레미스에서 호스트 되는지에 따라 달라 집니다.

### <a name="agents-for-azure-virtual-machines"></a>Azure virtual machines 용 에이전트

연결 모니터가 Azure Vm을 모니터링 원본으로 인식 하도록 하려면 에이전트에 Network Watcher 에이전트 가상 머신 확장을 설치 합니다. 이 확장을 *Network Watcher 확장*이 라고도 합니다. Azure virtual machines에는 종단 간 모니터링 및 기타 고급 기능을 트리거하기 위해 확장이 필요 합니다. 

[VM을 만들](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)때 Network Watcher 확장을 설치할 수 있습니다. 또한 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) 및 [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)용 Network Watcher 확장을 별도로 설치, 구성 및 문제 해결할 수 있습니다.

NSG (네트워크 보안 그룹) 또는 방화벽에 대 한 규칙은 원본과 대상 간의 통신을 차단할 수 있습니다. 연결 모니터는이 문제를 감지 하 고 토폴로지에 진단 메시지로 표시 합니다. 연결 모니터링을 사용 하도록 설정 하려면 NSG 및 방화벽 규칙이 원본 및 대상 간에 TCP 또는 ICMP를 통한 패킷을 허용 하는지 확인 합니다.

### <a name="agents-for-on-premises-machines"></a>온-프레미스 컴퓨터에 대 한 에이전트

연결 모니터가 온-프레미스 컴퓨터를 모니터링 원본으로 인식 하도록 하려면 컴퓨터에 Log Analytics 에이전트를 설치 합니다. 그런 다음 네트워크 성능 모니터 솔루션을 사용 하도록 설정 합니다. 이러한 에이전트는 Log Analytics 작업 영역에 연결 되므로 에이전트가 모니터링을 시작 하려면 먼저 작업 영역 ID 및 기본 키를 설정 해야 합니다.

Windows 컴퓨터용 Log Analytics 에이전트를 설치 하려면 [windows 용 가상 컴퓨터 확장 Azure Monitor](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)을 참조 하세요.

경로에 방화벽 또는 Nva (네트워크 가상 어플라이언스)가 포함 된 경우 대상에 연결할 수 있는지 확인 합니다.

## <a name="enable-network-watcher-on-your-subscription"></a>구독에서 Network Watcher 사용

가상 네트워크가 있는 모든 구독은 Network Watcher 사용 하도록 설정 됩니다. 구독에서 가상 네트워크를 만들 때 가상 네트워크의 지역 및 구독에서 Network Watcher 자동으로 사용 하도록 설정 됩니다. 이러한 자동 사용은 리소스에 영향을 주거나 요금을 부과 하지 않습니다. Network Watcher 구독에서 명시적으로 사용 하지 않도록 설정 되어 있는지 확인 합니다. 

자세한 내용은 [Network Watcher 사용](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)을 참조 하세요.

## <a name="create-a-connection-monitor"></a>연결 모니터 만들기 

연결 모니터는 일정 한 간격으로 통신을 모니터링 합니다. 연결 및 대기 시간에 대 한 변경 내용을 알려줍니다. 원본 에이전트와 대상 끝점 간의 현재 및 과거 네트워크 토폴로지를 확인할 수도 있습니다.

소스는 모니터링 에이전트가 설치 된 Azure Vm 또는 온-프레미스 컴퓨터 일 수 있습니다. 대상 끝점 Microsoft 365 Url, Dynamics 365 Url, 사용자 지정 Url, Azure VM 리소스 Id, IPv4, IPv6, FQDN 또는 임의의 도메인 이름일 수 있습니다.

### <a name="access-connection-monitor-preview"></a>액세스 연결 모니터 (미리 보기)

1. Azure Portal 홈 페이지에서 **Network Watcher**으로 이동 합니다.
1. 왼쪽의 **모니터링** 섹션에서 **연결 모니터 (미리 보기)** 를 선택 합니다.
1. 연결 모니터 (미리 보기)에서 생성 된 모든 연결 모니터가 표시 됩니다. 연결 모니터의 클래식 환경에서 만든 연결 모니터를 보려면 **연결 모니터** 탭으로 이동 합니다.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>연결 모니터 만들기

연결 모니터 (미리 보기)에서 만든 연결 모니터에서 온-프레미스 컴퓨터와 Azure Vm을 모두 원본으로 추가할 수 있습니다. 이러한 연결 모니터는 끝점에 대 한 연결을 모니터링할 수도 있습니다. 끝점은 Azure 또는 다른 모든 URL 또는 IP에 있을 수 있습니다.

연결 모니터 (미리 보기)에는 다음 엔터티가 포함 됩니다.

* **연결 모니터 리소스** – 지역별 Azure 리소스입니다. 다음 엔터티는 모두 연결 모니터 리소스의 속성입니다.
* **끝점** – 연결 확인에 참여 하는 원본 또는 대상입니다. 끝점의 예로는 Azure Vm, 온-프레미스 에이전트, Url 및 Ip가 있습니다.
* **테스트 구성** – 테스트에 대 한 프로토콜 관련 구성입니다. 선택한 프로토콜에 따라 포트, 임계값, 테스트 빈도 및 기타 매개 변수를 정의할 수 있습니다.
* **테스트 그룹** – 소스 끝점, 대상 끝점 및 테스트 구성이 포함 된 그룹입니다. 연결 모니터에는 두 개 이상의 테스트 그룹이 포함 될 수 있습니다.
* **Test** – 소스 끝점, 대상 끝점 및 테스트 구성의 조합입니다. 테스트는 모니터링 데이터를 사용할 수 있는 가장 세분화 된 수준입니다. 모니터링 데이터는 실패 한 검사 비율과 RTT (왕복 시간)를 포함 합니다.

 ![테스트 그룹 및 테스트 간의 관계를 정의 하는 연결 모니터를 보여 주는 다이어그램](./media/connection-monitor-2-preview/cm-tg-2.png)

[Azure Portal](connection-monitor-preview-create-using-portal.md) 또는 [ARMClient](connection-monitor-preview-create-using-arm-client.md) 를 사용 하 여 연결 모니터 미리 보기를 만들 수 있습니다.

테스트 그룹에 추가 하는 모든 소스, 대상 및 테스트 구성은 개별 테스트로 분류 됩니다. 원본 및 대상을 분할 하는 방법의 예는 다음과 같습니다.

* 테스트 그룹: TG1
* 원본: 3 (A, B, C)
* 대상: 2 (D, E)
* 테스트 구성: 2 (구성 1, 구성 2)
* 만든 총 테스트: 12

| 테스트 번호 | 원본 | 대상 | 테스트 구성 |
| --- | --- | --- | --- |
| 1 | A | D | 구성 1 |
| 2 | A | D | 구성 2 |
| 3 | A | E | 구성 1 |
| 4 | A | E | 구성 2 |
| 5 | b | D | 구성 1 |
| 6 | b | D | 구성 2 |
| 7 | b | E | 구성 1 |
| 8 | b | E | 구성 2 |
| 9 | C | D | 구성 1 |
| 10 | C | D | 구성 2 |
| 11 | C | E | 구성 1 |
| 12 | C | E | 구성 2 |

### <a name="scale-limits"></a> 확장 한도

연결 모니터의 크기 제한은 다음과 같습니다.

* 지역별 구독 당 최대 연결 모니터 수: 100
* 연결당 최대 테스트 그룹 수 모니터: 20
* 연결 당 최대 원본 및 대상 모니터: 100
* 연결 모니터 당 최대 테스트 구성 수: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>모니터링 데이터 분석 및 경고 설정

연결 모니터를 만든 후에는 원본 테스트 구성에 따라 대상에 대 한 연결을 확인 합니다.

### <a name="checks-in-a-test"></a>테스트 체크 인

테스트 구성에서 선택한 프로토콜에 따라 연결 모니터 (미리 보기)는 원본-대상 쌍에 대 한 일련의 검사를 실행 합니다. 선택한 테스트 빈도에 따라 검사가 실행 됩니다.

HTTP를 사용 하는 경우 서비스는 유효한 응답 코드를 반환 하는 HTTP 응답 수를 계산 합니다. PowerShell 및 CLI를 사용 하 여 유효한 응답 코드를 설정할 수 있습니다. 결과는 실패 한 검사의 백분율을 결정 합니다. RTT를 계산 하기 위해 서비스는 HTTP 호출 및 응답 사이의 시간을 측정 합니다.

TCP 또는 ICMP를 사용 하는 경우 서비스는 패킷 손실 비율을 계산 하 여 실패 한 검사의 백분율을 확인 합니다. RTT를 계산 하기 위해 서비스는 보낸 패킷에 대 한 승인 (ACK)을 수신 하는 데 걸린 시간을 측정 합니다. 네트워크 테스트에 경로 추적 데이터를 사용 하도록 설정한 경우 온-프레미스 네트워크에 대 한 홉 단위 손실 및 대기 시간을 확인할 수 있습니다.

### <a name="states-of-a-test"></a>테스트 상태

검사에서 반환 하는 데이터에 따라 테스트에는 다음과 같은 상태가 있을 수 있습니다.

* **Pass** – 실패 한 검사 및 RTT의 백분율에 대 한 실제 값은 지정 된 임계값 내에 있습니다.
* **Fail** – 실패 한 검사 또는 RTT의 백분율에 대 한 실제 값이 지정 된 임계값을 초과 했습니다. 임계값을 지정 하지 않으면 실패 한 검사의 백분율이 100 일 때 테스트가 실패 상태에 도달 합니다.
* **경고** - 
     * Threshold를 지정 하 고 연결 모니터 (미리 보기)가 임계값의 80%를 초과 하 여 검사를 수행 하지 못한 경우, 테스트는 경고로 표시 됩니다.
     * 지정 된 임계값이 없으면 연결 모니터 (미리 보기)는 임계값을 자동으로 할당 합니다. 임계값을 초과 하면 테스트 상태가 경고로 변경 됩니다.TCP 또는 ICMP 테스트에서 라운드트립 시간에 대 한 임계값은 750msec입니다. 실패 한 검사 비율의 경우 임계값은 10%입니다. 
* **비활성화**   – Log Analytics 작업 영역에 데이터가 없습니다.메트릭을 확인 합니다. 
* **실행 중 아님**   – 테스트 그룹을 사용 하지 않도록 설정 하 여 사용 안 함  

### <a name="data-collection-analysis-and-alerts"></a>데이터 수집, 분석 및 경고

연결 모니터 (미리 보기)가 수집 하는 데이터는 Log Analytics 작업 영역에 저장 됩니다. 연결 모니터를 만들 때이 작업 영역을 설정 합니다. 

모니터링 데이터는 Azure Monitor 메트릭에도 제공 됩니다. Log Analytics를 사용 하 여 원하는 기간 동안 모니터링 데이터를 유지할 수 있습니다. Azure Monitor은 기본적으로 30 일 동안만 메트릭을 저장 합니다. 

[데이터에 대 한 메트릭 기반 경고를 설정할](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)수 있습니다.

#### <a name="monitoring-dashboards"></a>대시보드 모니터링

모니터링 대시보드에는 구독, 지역, 타임 스탬프, 원본 및 대상 유형에 액세스할 수 있는 연결 모니터의 목록이 표시 됩니다.

Network Watcher에서 연결 모니터 (미리 보기)로 이동 하는 경우 다음과 같은 방법으로 데이터를 볼 수 있습니다.

* **연결 모니터** – 구독, 지역, 타임 스탬프, 원본 및 대상 유형에 대해 생성 된 모든 연결 모니터의 목록입니다. 이 보기는 기본값입니다.
* **테스트 그룹** – 구독, 지역, 타임 스탬프, 원본 및 대상 유형에 대해 만들어진 모든 테스트 그룹의 목록입니다. 이러한 테스트 그룹은 연결 모니터를 기준으로 필터링 되지 않습니다.
* **테스트** – 구독, 지역, 타임 스탬프, 원본 및 대상 유형에 대해 실행 되는 모든 테스트의 목록입니다. 이러한 테스트는 연결 모니터 또는 테스트 그룹을 기준으로 필터링 되지 않습니다.

다음 그림에서 3 개의 데이터 뷰는 화살표 1로 표시 됩니다.

대시보드에서 각 연결 모니터를 확장 하 여 해당 테스트 그룹을 볼 수 있습니다. 그런 다음 각 테스트 그룹을 확장 하 여 해당 테스트 그룹에서 실행 되는 테스트를 볼 수 있습니다. 

다음을 기준으로 목록을 필터링 할 수 있습니다.

* **최상위 필터** – 텍스트, 엔터티 형식 (연결 모니터, 테스트 그룹 또는 테스트) 타임 스탬프 및 범위를 기준으로 목록을 검색 합니다. 범위에는 구독, 지역, 원본 및 대상 유형이 포함 됩니다. 다음 그림의 상자 1을 참조 하세요.
* **상태 기반 필터** – 연결 모니터, 테스트 그룹 또는 테스트의 상태별로 필터링 합니다. 다음 그림의 상자 2를 참조 하세요.
* **경고 기반 필터** -연결 모니터 리소스에 대해 발생 하는 경고로 필터링 합니다. 다음 그림의 상자 3을 참조 하세요.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
예를 들어 연결 모니터 (미리 보기)에서 원본 IP가 10.192.64.56 인 모든 테스트를 확인 하려면 다음을 수행 합니다.
1. **테스트**를 위해 보기를 변경 합니다.
1. 검색 필드에 *10.192.64.56* 을 입력 합니다.
1. 최상위 수준 필터의 **범위** 에서 **원본**을 선택 합니다.

원본 IP가 10.192.64.56 인 연결 모니터 (미리 보기)에서 실패 한 테스트만 표시 하려면 다음을 수행 합니다.
1. **테스트**를 위해 보기를 변경 합니다.
1. 상태 기반 필터에 대해 **실패**를 선택 합니다.
1. 검색 필드에 *10.192.64.56* 을 입력 합니다.
1. 최상위 수준 필터의 **범위** 에서 **원본**을 선택 합니다.

연결 모니터 (미리 보기)에서 대상이 outlook.office365.com 실패 한 테스트만 표시 하려면 다음을 수행 합니다.
1. 뷰를 **테스트**로 변경 합니다.
1. 상태 기반 필터에 대해 **실패**를 선택 합니다.
1. 검색 필드에 *outlook.office365.com* 를 입력 합니다.
1. 최상위 수준 필터의 **범위** 에서 **대상**을 선택 합니다.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

연결 모니터 또는 테스트 그룹이 나 테스트의 실패 이유를 확인 하려면 reason 이라는 열을 클릭 합니다.  이는 임계값 (실패 한% 또는 RTT 검사)을 위반 하 고 관련 진단 메시지를 나타냅니다.
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
RTT 추세 및 연결 모니터에 대해 실패 한 검사 비율을 보려면 다음을 수행 합니다.
1. 조사할 연결 모니터를 선택 합니다.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. 다음 섹션이 표시 됩니다.  
    1. Essentials-선택한 연결 모니터의 리소스 관련 속성 
    1. 정리 
        1. 연결 모니터의 모든 테스트에 대해 RTT의 집계 된 추세선 및 실패 한 검사 비율입니다. 특정 시간을 설정 하 여 세부 정보를 볼 수 있습니다.
        1. RTT 또는 실패 한 검사의 백분율에 따라 테스트 그룹, 원본 및 대상 전체에서 상위 5 개 
    1. 테스트 그룹, 소스, 대상 및 테스트 구성에 대 한 탭-연결 모니터의 테스트 그룹, 원본 또는 대상을 나열 합니다. 검사 테스트가 실패 했습니다. RTT를 집계 하 고 실패 한% 값을 확인 합니다.  뒤로 이동 하 여 데이터를 볼 수도 있습니다. 
    1. 문제-연결 모니터의 각 테스트에 대해 발생 하는 홉 수준 문제입니다. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. 이 문서의 설명에 따라 Azure Automation Hybrid Runbook Worker를 제거할 수 있습니다.
    * 모든 테스트 보기를 클릭 하 여 연결 모니터의 모든 테스트를 봅니다.
    * 모든 테스트 그룹, 테스트 구성, 원본 및 대상 보기를 클릭 하 여 각 항목에 대 한 세부 정보를 봅니다. 
    * 테스트 그룹, 테스트 구성, 소스 또는 대상을 선택 하 여 엔터티의 모든 테스트를 봅니다.

1. 모든 테스트 보기 보기에서 다음을 수행할 수 있습니다.
    * 테스트를 선택 하 고 비교를 클릭 합니다.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * 클러스터를 사용 하 여 VNET, 서브넷과 같은 복합 리소스를 자식 리소스에 확장
    * 토폴로지를 클릭 하 여 모든 테스트에 대 한 토폴로지를 표시 합니다.

RTT 추세와 테스트 그룹에 대해 실패 한 검사 비율을 보려면 다음을 수행 합니다.
1. 조사할 테스트 그룹을 선택 합니다. 
1. 연결 모니터-필수 구성, 요약, 테스트 그룹, 소스, 대상 및 테스트 구성에 대 한 테이블을 볼 수 있습니다. 연결 모니터에 대해 수행 하는 것 처럼 탐색

RTT 추세와 테스트에 대 한 실패 한 검사 비율을 보려면 다음을 수행 합니다.
1. 조사할 테스트를 선택 합니다. 네트워크 토폴로지 및 종단 간 추세 차트에서 실패 한%와 왕복 시간을 확인할 수 있습니다. 식별 된 문제를 확인 하려면 토폴로지에서 경로에 있는 홉을 선택 합니다. 이러한 홉은 Azure 리소스입니다. 이 기능은 현재 온-프레미스 네트워크에서 사용할 수 없습니다.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Log Analytics의 로그 쿼리

Log Analytics를 사용 하 여 모니터링 데이터의 사용자 지정 보기를 만들 수 있습니다. UI에 표시 되는 모든 데이터는 Log Analytics에서 가져온 것입니다. 리포지토리의 데이터를 대화형으로 분석할 수 있습니다. 에이전트 상태 또는 Log Analytics 기반으로 하는 다른 솔루션의 데이터를 상호 연결 합니다. 데이터를 Excel 또는 Power BI으로 내보내거나 공유 가능한 링크를 만듭니다.

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor의 메트릭

연결 모니터 (미리 보기) 환경 전에 만들어진 연결 모니터에서 4 가지 메트릭을 모두 사용할 수 있습니다 .% 프로브 실패, AverageRoundtripMs, ChecksFailedPercent (미리 보기) 및 RoundTripTimeMs (미리 보기). 연결 모니터 (미리 보기) 환경에서 만든 연결 모니터에서는 *(미리 보기)* 태그가 지정 된 메트릭에 대해서만 데이터를 사용할 수 있습니다.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

메트릭을 사용 하는 경우 리소스 종류를 Microsoft. Network/networkWatchers/connectionMonitors로 설정 합니다.

| 메트릭 | 표시 이름 | 단위 | 집계 유형 | 설명 | 차원 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 실패한 프로브 % | 백분율 | 평균 | 연결 모니터링 프로브 비율이 실패 했습니다. | 차원 없음 |
| AverageRoundtripMs | 평균 왕복 시간(ms) | 밀리초 | 평균 | 원본 및 대상 간에 전송 되는 연결 모니터링 프로브에 대 한 평균 네트워크 RTT입니다. |             차원 없음 |
| ChecksFailedPercent (미리 보기) | % 검사 실패 (미리 보기) | 백분율 | 평균 | 테스트에 대해 실패 한 검사의 백분율입니다. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Sourceresourceid 여야 <br>SourceType <br>프로토콜 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>지역 |
| RoundTripTimeMs (미리 보기) | 왕복 시간 (ms) (미리 보기) | 밀리초 | 평균 | 원본 및 대상 간에 전송 되는 검사에 대 한 RTT 이 값은 평균이 아닙니다. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Sourceresourceid 여야 <br>SourceType <br>프로토콜 <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>지역 |

#### <a name="metric-based-alerts-for-connection-monitor"></a>연결 모니터에 대 한 메트릭 기반 경고

아래 방법을 사용 하 여 연결 모니터에 대 한 메트릭 경고를 만들 수 있습니다. 

1. 연결 모니터 (미리 보기)에서 [Azure Portal를 사용 하 여](connection-monitor-preview-create-using-portal.md#) 연결 모니터를 만드는 동안 
1. 대시보드에서 "경고 구성"을 사용 하 여 연결 모니터 (미리 보기) 사용 
1. Azure Monitor에서 Azure Monitor으로 경고를 만듭니다. 
    1. 연결 모니터 (미리 보기)에서 만든 연결 모니터 리소스를 선택 합니다.
    1. **메트릭이** 연결 모니터에 대 한 신호 유형으로 표시 되는지 확인 합니다.
    1. **조건 추가**에서 **신호 이름**에 대해 **checksfailedpercent (미리 보기)** 또는 **RoundTripTimeMs (미리 보기)** 를 선택 합니다.
    1. **신호 유형**에서 **메트릭**을 선택 합니다. 예를 들어 **Checksfailedpercent (미리 보기)** 를 선택 합니다.
    1. 메트릭에 대 한 모든 차원이 나열 됩니다. 차원 이름 및 차원 값을 선택 합니다. 예를 들어 **원본 주소** 를 선택 하 고 연결 모니터에 모든 원본의 IP 주소를 입력 합니다.
    1. **경고 논리**에서 다음 세부 정보를 입력 합니다.
        * **조건 형식**: **Static**
        * **조건** 및 **임계값**.
        * **집계 세분성 및 평가 빈도**: 연결 모니터 (미리 보기)는 1 분 마다 데이터를 업데이트 합니다.
    1. **작업**에서 작업 그룹을 선택 합니다.
    1. 경고 세부 정보를 제공 합니다.
    1. 경고 규칙을 만듭니다.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="연결 모니터 (미리 보기)에서 만든 연결 모니터를 보여 주는 스크린샷" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>네트워크에서 문제 진단

연결 모니터 (미리 보기)를 사용 하면 연결 모니터와 네트워크에서 발생 하는 문제를 진단할 수 있습니다. 하이브리드 네트워크의 문제는 이전에 설치한 Log Analytics 에이전트에 의해 검색 됩니다. Azure의 문제는 Network Watcher 확장에 의해 검색 됩니다. 

네트워크 토폴로지의 Azure 네트워크에서 문제를 볼 수 있습니다.

원본 온-프레미스 Vm을 포함 하는 네트워크의 경우 다음 문제를 검색할 수 있습니다.

* 요청 시간이 초과되었습니다.
* 끝점이 DNS에서 확인 되지 않음-임시 또는 영구입니다. URL이 잘못 되었습니다.
* 호스트를 찾을 수 없습니다.
* 원본에서 대상에 연결할 수 없습니다. ICMP를 통해 대상에 연결할 수 없습니다.
* 인증서 관련 문제: 
    * 에이전트를 인증 하는 데 필요한 클라이언트 인증서입니다. 
    * 인증서 재배치 목록에 액세스할 수 없습니다. 
    * 끝점의 호스트 이름이 인증서의 주체 또는 주체 대체 이름과 일치 하지 않습니다. 
    * 원본 로컬 컴퓨터의 신뢰할 수 있는 인증 기관 저장소에 루트 인증서가 없습니다. 
    * SSL 인증서가 만료 되었거나, 잘못 되었거나, 해지 되었거나, 호환 되지 않습니다.

소스가 Azure Vm 인 네트워크의 경우 다음 문제를 검색할 수 있습니다.

* 에이전트 문제:
    * 에이전트가 중지 되었습니다.
    * DNS 확인에 실패 했습니다.
    * 대상 포트에서 수신 대기 하는 응용 프로그램 또는 수신기가 없습니다.
    * 소켓을 열 수 없습니다.
* VM 상태 문제: 
    * 시작 중
    * 중지 중
    * 중지됨
    * 할당 취소 중
    * 할당 취소됨
    * 재부트
    * 할당 되지 않음
* ARP 테이블 항목이 없습니다.
* 로컬 방화벽 문제 또는 NSG 규칙으로 인해 트래픽이 차단 되었습니다.
* 가상 네트워크 게이트웨이 문제: 
    * 경로가 없습니다.
    * 두 게이트웨이 간의 터널이 끊어졌거나 누락 되었습니다.
    * 터널에서 두 번째 게이트웨이를 찾지 못했습니다.
    * 피어 링 정보를 찾을 수 없습니다.
* Microsoft Edge에서 경로가 누락 되었습니다.
* 시스템 경로 또는 UDR 때문에 트래픽이 중지 되었습니다.
* BGP가 게이트웨이 연결에서 사용 하도록 설정 되지 않았습니다.
* 부하 분산 장치에서 DIP 프로브가 다운 되었습니다.

## <a name="next-steps"></a>다음 단계
    
   * [Azure Portal를 사용 하 여 연결 모니터 (미리 보기)를 만드는 방법을](connection-monitor-preview-create-using-portal.md) 알아봅니다.  
   * [ARMClient를 사용 하 여 연결 모니터 (미리 보기)를 만드는 방법](connection-monitor-preview-create-using-arm-client.md) 알아보기  
