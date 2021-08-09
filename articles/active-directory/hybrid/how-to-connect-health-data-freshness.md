---
title: Azure AD Connect Health - 상태 서비스 데이터가 최신 경고가 아닙니다 | Microsoft Docs
description: 이 문서에서는 "상태 서비스 데이터가 최신이 아닙니다" 경고의 원인과 문제를 해결하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00518eb91e57efaacb7abc63b6ad4531619be2ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012872"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>상태 서비스 데이터가 최신 경고가 아닙니다

## <a name="overview"></a>개요

Azure AD Connect Health에서 모니터링하는 온-프레미스 머신의 에이전트는 정기적으로 데이터를 Azure AD Connect Health 서비스에 업로드합니다. 서비스가 에이전트로부터 데이터를 수신하지 않으면 포털에 표시된 정보가 부실해집니다. 이 문제를 강조하기 위해 서비스에서 **상태 관리 서비스 데이터가 최신 상태가 아님** 경고가 발생합니다. 이 경고는 서비스가 지난 2시간 동안 전체 데이터를 수신하지 않은 경우에 생성됩니다.  

- 상태 관리 서비스가 지난 2시간 동안 서버에서 보낸 **일부** 데이터 형식만 수신한 경우 **경고** 상태 경고가 발생합니다. 경고 상태 경고는 구성된 수신자에게 메일 알림을 트리거하지 않습니다. 
- 지난 2시간 동안 상태 관리 서비스 서버에서 데이터 형식을 받지 못한 경우 **오류** 상태 경고가 발생합니다. 오류 상태 경고는 구성된 수신자에게 메일 알림을 트리거합니다.

해당 서비스는 서비스 유형에 따라 온-프레미스 머신에서 실행되는 에이전트에서 데이터를 가져옵니다. 다음 표에서는 머신에서 실행되는 에이전트, 에이전트가 수행하는 작업, 서비스에서 생성하는 데이터 형식을 나열합니다. 경우에 따라 프로세스에 여러 서비스가 관련되어 있으므로 그중 어느 것이든 원인이 될 수 있습니다. 

## <a name="understanding-the-alert"></a>경고 이해

**경고 세부 정보** 블레이드는 경고가 발생한 시점과 마지막으로 발견된 시점을 보여 줍니다. 2시간마다 실행되는 백그라운드 프로세스는 경고를 생성하고 다시 평가합니다. 다음 예제에서는 03/10 오전 9시 59분에 초기 경고가 발생했습니다. 경고가 다시 평가되었을 때 03/12 오전 10시에 경고가 계속 존재했습니다. 해당 블레이드는 상태 관리 서비스가 마지막으로 특정 데이터 형식을 받은 시간도 자세히 알려 줍니다. 
 
 ![Azure AD Connect Health 경고 세부 정보](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
다음 표에서는 서비스 형식을 해당하는 필수 데이터 형식에 매핑합니다.

| 서비스 유형 | 에이전트(Windows 서비스 이름) | 목적 | 생성된 데이터 형식  |
| --- | --- | --- | --- |  
| Azure AD Connect(동기화) | Azure AD Connect Health Sync Insights Service | AAD 연결 관련 정보(커넥터, 동기화 규칙 등) 수집 | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | AAD Connect 관련 성능 카운터, ETW 추적, 파일 수집 | 성능 카운터 |
| AD DS | Azure AD Connect Health AD DS Insights 서비스 | 가상 테스트 수행, 토폴로지 정보 수집, 복제 메타데이터 |  - Adds-TopologyInfo-Json <br /> - Common-TestData-Json(테스트 결과 생성)   | 
|  | Azure AD Connect Health AD DS 모니터링 서비스 | ADDS 관련 성능 카운터, ETW 추적, 파일 수집 | - 성능 카운터  <br /> - Common-TestData-Json(테스트 결과 업로드)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | 가상 테스트 수행 | TestResult(테스트 결과 생성) | 
| | Azure AD Connect Health AD FS Insights Service  | ADFS 사용량 메트릭 수집 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | ADFS 관련 성능 카운터, ETW 추적, 파일 수집 | TestResult(테스트 결과 업로드) |

## <a name="troubleshooting-steps"></a>문제 해결 단계 

문제를 진단하는 데 필요한 단계는 다음과 같습니다. 첫 번째는 모든 서비스 유형에 공통적인 기본 검사입니다. 

> [!IMPORTANT] 
> 이 경고는 Connect Health [데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)을 따릅니다.

* 최신 버전의 에이전트가 설치되어 있는지 확인합니다. [릴리스 기록](reference-connect-health-version-history.md)을 봅니다. 
* Azure AD Connect Health 에이전트 서비스가 머신에서 **실행되고** 있는지 확인합니다. 예를 들어 AD FS용 Connect Health에 3개의 서비스가 있어야 합니다.
  ![Azure AD Connect Health 확인](./media/how-to-connect-health-agent-install/install5.png)

* [요구 사항 섹션](how-to-connect-health-agent-install.md#requirements)을 검토하고 충족하는지 확인합니다.
* [연결 테스트 도구](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)를 사용하여 연결 문제를 검색합니다.
* HTTP 프록시가 있는 경우 이 [구성 단계](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)를 수행합니다. 


## <a name="next-steps"></a>다음 단계
위의 단계에서 문제를 식별한 경우, 문제를 해결하고 경고가 해결될 때까지 기다립니다. 경고 백그라운드 프로세스는 2시간마다 실행되므로 경고를 해결하는 데 최대 2시간이 소요됩니다. 

* [Azure AD Connect Health 데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
