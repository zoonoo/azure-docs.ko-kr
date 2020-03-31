---
title: Azure AD Connect Health - 상태 서비스 데이터가 최신 경고가 아닙니다 | Microsoft Docs
description: 이 문서에서는 "상태 서비스 데이터가 최신이 아닙니다" 경고의 원인과 문제를 해결하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897172"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>상태 서비스 데이터가 최신 경고가 아닙니다

## <a name="overview"></a>개요

Azure AD Connect Health가 모니터링하는 온-프레미스 시스템의 에이전트는 Azure AD Connect 상태 서비스에 데이터를 주기적으로 업로드합니다. 서비스가 에이전트로부터 데이터를 받지 못하면 포털에 제공하는 정보는 부실합니다. 문제를 강조하기 위해 서비스는 최신 경고가 **아닌 상태 서비스 데이터를** 발생시게 됩니다. 이 경고는 서비스가 지난 2시간 동안 전체 데이터를 받지 못한 경우에 생성됩니다.  

- 상태 서비스가 지난 2시간 동안 서버에서 보낸 **부분** 데이터 형식만 수신한 경우 **경고** 상태 경고가 발생합니다. 경고 상태 경고는 구성된 받는 사람에게 전자 메일 알림을 트리거하지 않습니다. 
- 상태 서비스가 지난 2시간 동안 서버에서 데이터 형식을 받지 못한 경우 **오류** 상태 경고가 발생합니다. 오류 상태 경고는 구성된 받는 사람에게 전자 메일 알림을 트리거합니다.

서비스는 서비스 유형에 따라 온-프레미스 컴퓨터에서 실행 중인 에이전트에서 데이터를 가져옵니다. 다음 표에는 컴퓨터에서 실행되는 에이전트, 수행하는 수행 및 서비스가 생성하는 데이터 형식이 나열됩니다. 경우에 따라 프로세스에 여러 서비스가 관련되어 있으므로 그 중 어느 것이라도 범인이 될 수 있습니다. 

## <a name="understanding-the-alert"></a>경고 이해

**경고 세부 정보** 블레이드는 경고가 발생하고 마지막으로 감지된 시기를 표시합니다. 2시간마다 실행되는 백그라운드 프로세스는 경고를 생성하고 다시 평가합니다. 다음 예제에서는 초기 경고가 오전 9시 59분에 03/10에 발생했습니다. 경고가 다시 평가되었을 때 경고는 오전 10:00에 03/12에 계속 존재했습니다. 블레이드는 또한 상태 서비스가 특정 데이터 형식을 마지막으로 받은 시간을 자세히 설명합니다. 
 
 ![Azure AD 연결 상태 경고 세부 정보](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
다음 표는 서비스 유형을 해당 필수 데이터 유형에 매핑합니다.

| 서비스 유형 | 에이전트(Windows 서비스 이름) | 목적 | 생성된 데이터 형식  |
| --- | --- | --- | --- |  
| Azure AD 연결(동기화) | Azure AD Connect Health Sync Insights Service | AAD 연결 관련 정보(커넥터, 동기화 규칙 등) 수집 | - 애드싱크서비스-동기화규칙 <br />  - 애드싱크서비스 커넥터 <br /> - 애드싱크서비스-글로벌구성  <br />  - 애드싱크서비스-런프로필결과 <br /> - AadSync서비스-서비스 구성 <br /> - 애드싱크 서비스-서비스 상태   |
|  | Azure AD Connect Health Sync Monitoring Service | AAD 연결 관련 퍼프 카운터, ETW 추적, 파일 수집 | 성능 카운터 |
| AD DS | Azure AD Connect Health AD DS Insights 서비스 | 합성 테스트 수행, 토폴로지 정보 수집, 복제 메타데이터 |  - 추가 토폴로지정보-Json <br /> - 공통 테스트데이터-Json (테스트 결과 생성)   | 
|  | Azure AD Connect Health AD DS 모니터링 서비스 | ADDS 관련 퍼프 카운터, ETW 트레이스, 파일 수집 | - 성능 카운터  <br /> - 공통 테스트데이터-Json (테스트 결과 업로드)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | 합성 테스트 수행 | TestResult(테스트 결과 생성) | 
| | Azure AD Connect Health AD FS Insights Service  | ADFS 사용 메트릭 수집 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | ADFS 관련 퍼프 카운터, ETW 추적, 파일 수집 | 테스트결과(테스트 결과 업로드) |

## <a name="troubleshooting-steps"></a>문제 해결 단계 

문제를 진단하는 데 필요한 단계는 아래에 있습니다. 첫 번째는 모든 서비스 유형에 공통적인 기본 검사 집합입니다. 

> [!IMPORTANT] 
> 이 경고는 Connect Health [데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)을 따릅니다.

* 에이전트의 최신 버전이 설치되어 있는지 확인합니다. [릴리스 기록](reference-connect-health-version-history.md)보기. 
* Azure AD Connect 상태 에이전트 서비스가 컴퓨터에서 **실행되고** 있는지 확인합니다. 예를 들어 AD FS용 Connect Health에 3개의 서비스가 있어야 합니다.
  ![Azure AD Connect Health 확인](./media/how-to-connect-health-agent-install/install5.png)

* [요구 사항 섹션](how-to-connect-health-agent-install.md#requirements)을 검토하고 충족하는지 확인합니다.
* [연결 테스트 도구](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)를 사용하여 연결 문제를 검색합니다.
* HTTP 프록시가 있는 경우 이 [구성 단계](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)를 수행합니다. 


## <a name="next-steps"></a>다음 단계
위의 단계 중 라도 문제가 확인되면 문제를 수정하고 경고가 해결될 때까지 기다립니다. 경고 백그라운드 프로세스는 2시간마다 실행되므로 경고를 해결하는 데 최대 2시간이 걸립니다. 

* [Azure AD 연결 상태 데이터 보존 정책](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
