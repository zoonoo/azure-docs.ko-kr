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
ms.openlocfilehash: 6c5bc2ea76c558e47eaa5f297ebe36a629aa5754
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702636"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>상태 서비스 데이터가 최신 경고가 아닙니다

## <a name="overview"></a>개요

Azure AD Connect Health를 정기적으로 모니터링 하는 온-프레미스 컴퓨터에서 에이전트는 Azure AD Connect Health Service에 데이터를 업로드 합니다. 서비스 에이전트에서 데이터를 받지 못하는 경우 포털에 표시 하는 정보를 부실 됩니다. 서비스에서 발생 한 문제를 강조 표시 합니다 **상태 서비스 데이터가 최신 상태입니다.** 경고 합니다. 서비스는 지난 2 시간에서 전체 데이터를 받지 않은 경우 경고가 발생 합니다.  

- 합니다 **경고** 상태 관리 서비스에만 수신 상태 경고가 발생 **부분** 지난 2 시간 내에 서버에서 전송 하는 데이터 형식입니다. 경고 상태 경고 구성 된 받는 사람에 게 전자 메일 알림을 트리거하지 않습니다. 
- 합니다 **오류** Health Service가 받지 데이터 유형을 서버에서 지난 2 시간 내에 상태 경고가 발생 합니다. 구성 된 받는 사람에 게 전자 메일 알림 경고를 트리거하기 오류 상태입니다.

서비스는 서비스 형식에 따라 온-프레미스 컴퓨터에서 실행 되는 에이전트에서 데이터를 가져옵니다. 다음 표에서 컴퓨터, 수행할 하는 작업 및 서비스가 생성 하는 데이터 형식에서 실행 되는 에이전트를 나열 합니다. 경우에 따라 여러 서비스가 있습니다 프로세스에 관련 된 원인 중 하나라도 충족할 수 있도록 합니다. 

## <a name="understanding-the-alert"></a>경고 이해

합니다 **경고 세부 정보를** 블레이드에 경고가 발생 한 시점과 마지막으로 검색 되었습니다. 2 시간 마다 실행 하는 백그라운드 프로세스를 생성 하 고 다시 경고를 평가 합니다. 다음 예제에서는 초기 경고 03/10에서 오전 9 시 59 발생 합니다. 경고 오전 10 시 경고를 다시 평가 된 03/12에 여전히 존재 합니다. 블레이드는 또한 특정 데이터 형식 Health Service가 마지막으로 받은 시간 자세히 설명 합니다. 
 
 ![Azure AD Connect Health 경고 세부 정보](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
다음 표에서 해당 필요한 데이터 형식에 서비스 형식을 매핑합니다.

| 서비스 유형 | 에이전트 (Windows 서비스 이름) | 용도 | 생성 된 데이터 형식  |
| --- | --- | --- | --- |  
| Azure AD Connect (동기화) | Azure AD Connect Health Sync Insights Service | AAD Connect 관련 정보 (커넥터, 동기화 규칙 등) 수집 | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> -AadSyncService GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring Service | AAD Connect 관련 성능 카운터, ETW 추적 파일 수집 | 성능 카운터 |
| AD DS | Azure AD Connect Health AD DS Insights 서비스 | 가상 테스트 수행, 토폴로지 정보를 복제 메타 데이터를 수집 합니다. |  추가-TopologyInfo-Json <br /> 일반적인-TestData-Json (테스트 결과 생성 합니다.)   | 
|  | Azure AD Connect Health AD DS 모니터링 서비스 | 추가 관련 성능 카운터, ETW 추적 파일 수집 | 성능 카운터  <br /> 일반적인-TestData-Json (테스트 결과 업로드)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | 가상 테스트를 수행 합니다. | TestResult (테스트 결과 생성 합니다.) | 
| | Azure AD Connect Health AD FS Insights Service  | ADFS 사용 현황 메트릭 수집 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | 파일, ETW 추적, ADFS 관련 성능 카운터를 수집 합니다. | TestResult (테스트 결과 업로드) |

## <a name="troubleshooting-steps"></a>문제 해결 단계 

문제를 진단 하는 데 필요한 단계는 다음과 같습니다. 첫 번째는 일련의 모든 서비스 형식에 공통 되는 기본 검사 합니다. 아래 표에서 각 서비스 형식 및 데이터 형식에 대 한 특정 단계를 나열 합니다. 

> [!IMPORTANT] 
> 이 경고는 Connect Health [데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)을 따릅니다.

* 에이전트의 최신 버전이 설치 되어 있는지 확인 합니다. 뷰 [릴리스 기록](reference-connect-health-version-history.md)합니다. 
* Azure AD Connect Health Agent 서비스는 했는지 **실행** 컴퓨터에 있습니다. 예를 들어 AD FS용 Connect Health에 3개의 서비스가 있어야 합니다.
  ![Azure AD Connect Health 확인](./media/how-to-connect-health-agent-install/install5.png)

* [요구 사항 섹션](how-to-connect-health-agent-install.md#requirements)을 검토하고 충족하는지 확인합니다.
* [연결 테스트 도구](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)를 사용하여 연결 문제를 검색합니다.
* HTTP 프록시가 있는 경우 이 [구성 단계](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)를 수행합니다. 


## <a name="next-steps"></a>다음 단계
위 단계의 모든 문제를 식별 하는 경우 해결 하 고 해결 하는 경고를 기다립니다. 경고 백그라운드 프로세스가 하므로 경고를 해결 하려면 최대 2 시간이 걸립니다 2 시간 마다 실행 됩니다. 

* [Azure AD Connect Health 데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
