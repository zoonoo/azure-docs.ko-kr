---
title: "Log Analytics의 네트워크 모니터링 | Microsoft Docs"
description: "클라우드, 온-프레미스 및 하이브리드 환경에 걸친 네트워크를 관리하는, NPM을 비롯한 네트워크 모니터링 솔루션의 개요입니다."
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 6d93821b59e1f69a48c3d5eeda96dad2edddb188
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2018
---
# <a name="network-monitoring-solutions"></a>네트워크 모니터링 솔루션 

Azure는 네트워킹 자산을 모니터링하는 솔루션 호스트를 제공합니다. Azure에는 네트워크 연결, ExpressRoute 회로 상태를 모니터링하고 클라우드의 네트워크 트래픽을 분석하는 솔루션 및 유틸리티가 있습니다.

## <a name="network-performance-monitor-npm"></a>NPM(네트워크 성능 모니터)

NPM(네트워크 성능 모니터)은 응용 프로그램에 대한 네트워크 연결인 네트워크의 상태를 모니터링하도록 맞춰진, 네트워크 성능에 대한 정보를 제공하는 기능 모음입니다. NPM은 클라우드를 기반으로 하며, 다음의 연결을 모니터링하는 하이브리드 네트워크 모니터링 솔루션을 제공합니다.
 
* 클라우드 배포 및 온-프레미스 위치
* 여러 데이터 센터 및 지사
* 중요 업무용 다중 계층 응용 프로그램/마이크로 서비스
* 사용자 위치 및 웹 기반 응용 프로그램(HTTP/HTTPS) 

## <a name="performance-monitor"></a>성능 모니터링

성능 모니터는 NPM의 일부이며 클라우드, 하이브리드 및 온-프레미스 환경에 대한 네트워크 모니터링입니다. 원격 분기와 지사, 상점 위치, 데이터 센터 및 클라우드 전반의 네트워크 연결을 모니터링할 수 있습니다. 사용자가 불만을 제기하기 전에 네트워크 문제를 감지할 수 있습니다. 주요 이점은 다음과 같습니다.

* 다양한 서브넷에서 손실 및 대기 시간 모니터링, 경고 설정
* 네트워크의 모든 경로(중복 경로 포함) 모니터링
* 복제하기 어려운 일시적 및 특정 시점의 네트워크 문제 해결
* 성능 저하를 야기한 네트워크의 특정 세그먼트 확인
* SNMP 없이 네트워크의 상태 모니터링

![NPM 토폴로지 맵](./media/network-monitoring-overview/npm-topology-map.png) 

자세한 내용은 다음 문서를 참조하세요.

* [Log Analytics의 네트워크 성능 모니터 솔루션 구성](../log-analytics/log-analytics-network-performance-monitor.md) 
* [사용 사례](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  제품 업데이트: [2017년 2월](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [2017년 8월](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute 모니터링

ExpressRoute를 위한 NPM은 개인 피어링 연결에 대한 종합적인 ExpressRoute 모니터링을 제공합니다. ExpressRoute를 통해 지사와 Azure 간의 E2E 연결 및 성능을 모니터링할 수 있습니다. 주요 기능은 다음과 같습니다.

* 구독에 연결된 ER 회로의 자동 검색
* 온-프레미스에서 클라우드 응용 프로그램까지 네트워크 토폴로지 검색
* 용량 계획, 사용률 분석
* 기본 및 보조 경로에서 모니터링 및 경고
* VNet에 대한 연결에서 성능 저하 감지

자세한 내용은 다음 문서를 참조하세요.

* [ExpressRoute에 대한 네트워크 성능 모니터 구성](../expressroute/how-to-npm.md)
* [블로그 게시물](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>서비스 엔드포인트 모니터링

서비스 엔드포인트 모니터링을 사용하면 이제 응용 프로그램의 연결을 테스트하고, 온-프레미스, 통신 회사 네트워크 및 클라우드/개인 데이터 센터에서 성능 병목 상태를 검색할 수 있습니다.

* 응용 프로그램에 대한 종단 간 네트워크 연결 모니터링
* 응용 프로그램 전달을 네트워크 성능과 연결, 사용자와 응용 프로그램 간의 경로에 따라 성능 저하의 정확한 위치를 감지
* 전 세계 여러 사용자 위치에서 응용 프로그램 연결 테스트
* 비즈니스 및 SaaS 응용 프로그램에 대한 네트워크 대기 시간 및 패킷 손실 파악
* 잘못된 응용 프로그램 성능을 일으킬 수 있는 네트워크상 핫 스폿 확인
* Microsoft Office 365, Dynamics 365, Skype for Business 및 기타 Microsoft 서비스를 위한 기본 테스트를 사용하여 Office 365 응용 프로그램에 대한 연결 가능성 모니터링

자세한 내용은 다음 문서를 참조하세요.

* [서비스 엔드포인트를 모니터링하기 위한 네트워크 성능 모니터 구성](https://aka.ms/applicationconnectivitymonitorguide)
* [블로그 게시물](https://aka.ms/svcendptmonitor)

## <a name="next-steps"></a>다음 단계

* [네트워크 성능 모니터 구성](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [ExpressRoute에 대한 네트워크 성능 모니터 구성](../expressroute/how-to-npm.md)
