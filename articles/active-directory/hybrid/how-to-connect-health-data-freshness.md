---
title: Azure AD Connect Health - 상태 서비스 데이터가 최신 경고가 아닙니다 | Microsoft Docs
description: 이 문서에서는 "상태 서비스 데이터가 최신이 아닙니다" 경고의 원인과 문제를 해결하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429019"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>상태 서비스 데이터가 최신 경고가 아닙니다

## <a name="overview"></a>개요
Azure AD Connect Health에서 모니터링하는 온-프레미스 머신의 에이전트는 정기적으로 데이터를 Azure AD Connect Health 서비스에 업로드합니다. 서비스가 에이전트로부터 데이터를 수신하지 않으면 포털에 표시된 정보가 부실해집니다. 이 문제를 강조하기 위해 서비스에서 **상태 서비스 데이터가 최신 상태가 아님** 경고가 발생합니다. 이 경고는 서비스가 지난 2시간 동안 데이터를 수신하지 않은 경우에 생성됩니다.  

* Connect Health에서 2시간 동안 서버로부터 전송되는 일부 데이터 요소를 받지 못하면 **경고** 상태 경고가 발생합니다. 경고 상태 경고는 테넌트 관리자에게 이메일 알림을 트리거하지 않습니다.
* Connect Health에서 2시간 동안 서버로부터 전송되는 모든 데이터 요소를 받지 못하면 **오류** 상태 경고가 발생합니다. 오류 상태 경고는 테넌트 관리자에게 이메일 알림을 트리거합니다.


## <a name="troubleshooting-steps"></a>문제 해결 단계 

> [!IMPORTANT] 
> 이 경고는 Connect Health [데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)을 따릅니다.

* Azure AD Connect Health 에이전트 서비스가 머신에서 실행되고 있는지 확인합니다. 예를 들어 AD FS용 Connect Health에 3개의 서비스가 있어야 합니다.  
  ![Azure AD Connect Health 확인](./media/how-to-connect-health-agent-install/install5.png)

* [요구 사항 섹션](how-to-connect-health-agent-install.md#requirements)을 검토하고 충족하는지 확인합니다.
* [연결 테스트 도구](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)를 사용하여 연결 문제를 검색합니다.
* HTTP 프록시가 있는 경우 이 [구성 단계](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)를 수행합니다. 

경고 세부 정보 블레이드에 서버에서 누락된 데이터 요소가 나열됩니다. 다음 표는 문제 범위를 좁히는 데 도움이 됩니다. 
### <a name="connect-health-for-sync"></a>동기화용 Connect Health

| 데이터 요소 | 문제 해결 단계 |
| --- | --- | 
| PerfCounter | - [Azure 서비스 엔드포인트에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [아웃바운드 트래픽에 대한 SSL 조사가 필터링 또는 해제됨](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [에이전트를 실행하는 서버의 방화벽 포트](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - [Azure 서비스 엔드포인트에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [에이전트를 실행하는 서버의 방화벽 포트](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>ADFS용 Connect Health

AD FS에 대한 유효성 검사를 수행하고 [AD FS 도움말](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282)의 워크플로를 수행하는 추가 단계입니다.

| 데이터 요소 | 문제 해결 단계 |
| --- | --- | 
| PerfCounter, TestResult | - [Azure 서비스 엔드포인트에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [아웃바운드 트래픽에 대한 SSL 조사가 필터링 또는 해제됨](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [에이전트를 실행하는 서버의 방화벽 포트](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | IP 주소를 기반으로 하는 아웃바운드 연결. [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=41653) 참조 | 

### <a name="connect-health-for-adds"></a>ADDS용 Connect Health

| 데이터 요소 | 문제 해결 단계 |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Azure 서비스 엔드포인트에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [에이전트를 실행하는 서버의 방화벽 포트](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>다음 단계
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
