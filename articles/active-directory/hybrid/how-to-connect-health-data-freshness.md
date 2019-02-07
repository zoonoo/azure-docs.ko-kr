---
title: Azure AD Connect Health - 상태 서비스 데이터가 최신 경고가 아닙니다 | Microsoft Docs
description: 이 문서에서는 "상태 서비스 데이터가 최신이 아닙니다" 경고의 원인과 문제를 해결하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 3f97e9696a5138d9102037103a45c86988a7506c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489278"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>상태 서비스 데이터가 최신 경고가 아닙니다

## <a name="overview"></a>개요
<li>Azure AD Connect Health는 2시간 동안 서버에서 모든 데이터 지점을 받지 못하면 데이터 새로 고침 경고를 생성합니다. 경고 제목은 **상태 서비스 데이터가 최신 경고가 아닙니다**입니다. </li>
<li>Connect Health에서 2시간 동안 서버로부터 전송되는 일부 데이터 요소를 받지 못하면 **경고** 상태 경고가 발생합니다. 경고 상태 경고는 테넌트 관리자에게 이메일 알림을 트리거하지 않습니다. </li>
<li>Connect Health에서 2시간 동안 서버로부터 전송되는 모든 데이터 요소를 받지 못하면 **오류** 상태 경고가 발생합니다. 오류 상태 경고는 테넌트 관리자에게 이메일 알림을 트리거합니다. </li>

>[!IMPORTANT] 
> 이 경고는 Connect Health [데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)을 따릅니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계 
* [요구 사항 섹션](how-to-connect-health-agent-install.md#requirements)을 검토하고 충족하는지 확인합니다.
* [연결 테스트 도구](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)를 사용하여 연결 문제를 검색합니다.
* HTTP 프록시가 있는 경우 [여기의 구성 단계](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)를 따르세요. 

### <a name="connect-health-for-sync"></a>동기화용 Connect Health

| 데이터 요소 | 문제 해결 단계 |
| --- | --- | 
| PerfCounter | - [Azure 서비스 엔드포인트에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [아웃바운드 트래픽에 대한 SSL 조사가 필터링 또는 해제됨](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [에이전트를 실행하는 서버의 방화벽 포트](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [IE 보안 강화를 사용하는 경우 지정된 웹 사이트 허용](https://technet.microsoft.com/windows/ms537180(v=vs.60)) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - IP 주소를 기반으로 하는 아웃바운드 연결. [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 참조 <br /> - [Azure 서비스 엔드포인트에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [에이전트를 실행하는 서버의 방화벽 포트](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>ADFS용 Connect Health

AD FS에 대한 유효성 검사를 수행하고 [AD FS 도움말](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282)의 워크플로를 수행하는 추가 단계입니다.

| 데이터 요소 | 문제 해결 단계 |
| --- | --- | 
| PerfCounter, TestResult | - [Azure 서비스 엔드포인트에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [아웃바운드 트래픽에 대한 SSL 조사가 필터링 또는 해제됨](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [에이전트를 실행하는 서버의 방화벽 포트](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [IE 보안 강화를 사용하는 경우 지정된 웹 사이트 허용](https://technet.microsoft.com/windows/ms537180(v=vs.60)) |
|  Adfs-UsageMetrics | IP 주소를 기반으로 하는 아웃바운드 연결. [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 참조 | 

### <a name="connect-health-for-adds"></a>ADDS용 Connect Health

| 데이터 요소 | 문제 해결 단계 |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Azure 서비스 엔드포인트에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [아웃바운드 트래픽에 대한 SSL 조사가 필터링 또는 해제됨](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [에이전트를 실행하는 서버의 방화벽 포트](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [IE 보안 강화를 사용하는 경우 지정된 웹 사이트 허용](https://technet.microsoft.com/windows/ms537180(v=vs.60)) <br />  - IP 주소를 기반으로 하는 아웃바운드 연결. [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 참조  |


## <a name="next-steps"></a>다음 단계
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
