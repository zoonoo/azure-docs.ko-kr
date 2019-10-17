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
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41c1c102e88e1712d561874aef87a6f22ed250a9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430223"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>상태 서비스 데이터가 최신 경고가 아닙니다

## <a name="overview"></a>개요

Azure AD Connect Health 모니터링 하는 온-프레미스 컴퓨터의 에이전트는 데이터를 Azure AD Connect Health 서비스에 주기적으로 업로드 합니다. 서비스가 에이전트에서 데이터를 받지 못하면 포털에서 제공 하는 정보는 오래 된 것입니다. 문제를 강조 하기 위해 서비스에서 **Health service 데이터가** 최신 상태가 아닌 경고를 발생 시킵니다. 이 경고는 지난 2 시간 동안 서비스에서 완전 한 데이터를 받지 못한 경우에 생성 됩니다.  

- 상태 관리 서비스는 지난 2 시간 동안 서버에서 전송 된 **부분** 데이터 형식만 수신 하는 경우 **경고** 상태 경고가 발생 합니다. 경고 상태 경고는 구성 된 받는 사람에 대 한 전자 메일 알림을 트리거하지 않습니다. 
- 상태 관리 서비스 지난 2 시간 동안 서버에서 데이터 형식을 받지 못한 경우 **오류** 상태 경고가 발생 합니다. 오류 상태 경고는 구성 된 받는 사람에 게 전자 메일 알림을 트리거합니다.

서비스는 서비스 유형에 따라 온-프레미스 컴퓨터에서 실행 되는 에이전트에서 데이터를 가져옵니다. 다음 표에서는 컴퓨터에서 실행 되는 에이전트, 수행 하는 작업 및 서비스에서 생성 하는 데이터 형식을 보여 줍니다. 프로세스에 여러 서비스가 관련 되어 있기 때문에 그 중 하나라도 원인일 수 있습니다. 

## <a name="understanding-the-alert"></a>경고 이해

경고 **정보** 블레이드에서 경고가 발생 하 고 마지막으로 검색 된 시간을 표시 합니다. 2 시간 마다 실행 되는 백그라운드 프로세스는 경고를 생성 하 고 다시 계산 합니다. 다음 예제에서는 03/10 오전 9:59에 초기 경고가 발생 했습니다. 경고가 다시 평가 될 때 03/12 오전 10:00에 경고가 계속 발생 했습니다. 또한 상태 관리 서비스 마지막으로 특정 데이터 형식을 받은 시간을 자세히 설명 합니다. 
 
 ![Azure AD Connect Health 경고 정보](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
다음 표에서는 서비스 유형을 해당 하는 필수 데이터 형식에 매핑합니다.

| 서비스 유형 | 에이전트 (Windows 서비스 이름) | 용도 | 생성 된 데이터 형식  |
| --- | --- | --- | --- |  
| Azure AD Connect (동기화) | Azure AD Connect Health Sync Insights Service | AAD Connect 관련 정보 (커넥터, 동기화 규칙 등)를 수집 합니다. | - AadSyncService-SynchronizationRules <br />  -AadSyncService-커넥터 <br /> -AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | AAD Connect 관련 성능 카운터, ETW 추적, 파일을 수집 합니다. | 성능 카운터 |
| AD DS | Azure AD Connect Health AD DS Insights 서비스 | 가상 테스트 수행, 토폴로지 정보 수집, 복제 메타 데이터 |  -TopologyInfo-Json <br /> -TestData (테스트 결과 생성)   | 
|  | Azure AD Connect Health AD DS 모니터링 서비스 | ADDS 전용 perf 카운터, ETW 추적, 파일 수집 | -성능 카운터  <br /> -TestData (테스트 결과 업로드)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | 가상 테스트 수행 | TestResult (테스트 결과 생성) | 
| | Azure AD Connect Health AD FS Insights Service  | ADFS 사용 메트릭 수집 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | ADFS 관련 perf 카운터, ETW 추적, 파일 수집 | TestResult (테스트 결과 업로드) |

## <a name="troubleshooting-steps"></a>문제 해결 단계 

문제를 진단 하는 데 필요한 단계는 다음과 같습니다. 첫 번째는 모든 서비스 유형에 공통적인 기본 검사 집합입니다. 

> [!IMPORTANT] 
> 이 경고는 Connect Health [데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)을 따릅니다.

* 최신 버전의 에이전트가 설치 되어 있는지 확인 합니다. [릴리스 기록을](reference-connect-health-version-history.md)봅니다. 
* Azure AD Connect Health 에이전트 서비스가 컴퓨터에서 **실행** 되 고 있는지 확인 합니다. 예를 들어 AD FS용 Connect Health에 3개의 서비스가 있어야 합니다.
  ![Verify Azure AD Connect Health @ no__t-1

* [요구 사항 섹션](how-to-connect-health-agent-install.md#requirements)을 검토하고 충족하는지 확인합니다.
* [연결 테스트 도구](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)를 사용하여 연결 문제를 검색합니다.
* HTTP 프록시가 있는 경우 이 [구성 단계](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)를 수행합니다. 


## <a name="next-steps"></a>다음 단계
위의 단계 중 하나라도 문제를 식별 한 경우 문제를 해결 하 고 경고가 해결 될 때까지 기다립니다. 경고 백그라운드 프로세스는 2 시간 마다 실행 되므로 경고를 해결 하는 데 최대 2 시간이 소요 됩니다. 

* [Azure AD Connect Health 데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
