---
title: Azure AD의 로그인 진단이란? | Microsoft Docs
description: Azure AD의 로그인 진단에 대한 일반적인 개요를 제공합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: e85de1edd94a0430a4b28b332d9e43b967afba76
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608921"
---
# <a name="what-is-sign-in-diagnostic-in-azure-ad"></a>Azure AD의 로그인 진단이란?

Azure AD는 사용자가 관리되는 리소스로 수행할 수 있는 작업을 제어하는 유연한 보안 모델을 제공합니다. 이러한 리소스에 대한 액세스는 액세스하는 **사용자** 및 **방법** 에 따라 제어됩니다. 일반적으로 유연성은 구성 옵션의 수로 인해 특정 수준의 복잡성과 함께 제공됩니다. 복잡성은 오류 위험을 높일 수 있습니다.

IT 관리자에게는 문제가 발생하는 경우 쉽게 진단하고 해결할 수 있도록 시스템 활동에 대한 적절한 수준의 인사이트를 제공하는 솔루션이 필요합니다. Azure AD의 로그인 진단은 이러한 솔루션에 대한 예입니다. 진단을 사용하여 Microsoft 지원 없이 로그인 중에 발생한 상황 및 문제를 해결하기 위해 수행할 수 있는 작업을 분석합니다.

이 문서에서는 솔루션에서 수행하는 작업 및 이를 사용하는 방법에 대해 간략히 설명합니다.


## <a name="requirements"></a>요구 사항

로그인 진단은 Azure AD의 모든 버전에서 사용할 수 있습니다.<br> 이를 사용하려면 Azure AD의 전역 관리자여야 합니다.

## <a name="how-it-works"></a>작동 방식

Azure AD에서 로그인 시도에 대한 응답은 테넌트에 액세스하는 **사용자** 및 **방법** 과 관련이 있습니다. 예를 들어 관리자는 일반적으로 회사 네트워크에서 로그인할 때 테넌트의 모든 측면을 구성할 수 있습니다. 그러나 신뢰할 수 없는 네트워크에서 동일한 계정으로 로그인하면 차단될 수도 있습니다.
 
로그인 시도에 응답할 수 있는 시스템의 유연성이 높으므로 로그인 문제를 해결해야 하는 시나리오가 될 수 있습니다. 로그인 진단은 다음과 같은 기능입니다.

- 로그인 데이터를 분석합니다. 

- 발생한 상황 및 문제를 해결하는 방법에 대한 추천 사항을 표시합니다. 

Azure AD용 로그인 진단은 로그인 오류를 자체 진단할 수 있도록 설계되었습니다. 진단 프로세스를 완료하려면 다음을 수행해야 합니다.

![로그인 진단 프로세스](./media/overview-sign-in-diagnostics/process.png)
 
1. 관심 있는 로그인 이벤트의 범위 **정의**

2. 검토하려는 로그인 **선택**

3. 진단 결과 **검토**

4. 작업 **수행**

 
### <a name="define-scope"></a>범위 정의

이 단계에서는 조사하려는 로그인의 범위를 정의합니다. 범위는 사용자 또는 식별자(correlationId, requestId) 및 시간 범위를 기준으로 합니다. 범위를 더 좁히기 위해 앱 이름을 지정할 수도 있습니다. Azure AD는 범위 정보를 사용하여 사용자에게 적절한 이벤트를 찾습니다.  

### <a name="select-sign-in"></a>로그인 선택  

Azure AD는 검색 조건에 따라 일치하는 모든 로그인을 검색하여 인증 요약 목록 보기에 표시합니다. 

![인증 요약](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
이 보기에 표시되는 열을 사용자 지정할 수 있습니다.

### <a name="review-diagnostic"></a>진단 검토 

Azure AD는 선택한 로그인 이벤트에 대한 진단 결과를 제공합니다. 

![진단 결과](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
결과는 평가에서 시작됩니다. 평가는 발생한 상황을 몇 가지 문장으로 설명합니다. 설명은 시스템의 동작을 이해하는 데 도움이 됩니다. 

다음 단계로, 선택한 로그인에 적용된 관련 조건부 액세스 정책에 대한 요약을 얻습니다. 이 부분은 문제를 해결하는 데 추천되는 수정 단계를 통해 완료됩니다. 추가 도움이 있어야 문제를 해결할 수 있는 경우가 있으므로 추천 단계가 지원 티켓을 여는 것일 수 있습니다. 

### <a name="take-action"></a>작업 수행 
이 시점에서 문제를 해결하는 데 필요한 정보가 있어야 합니다.


## <a name="scenarios"></a>시나리오

이 섹션에서는 적용되는 진단 시나리오에 대해 간략히 설명합니다. 구현되는 시나리오는 다음과 같습니다. 
 
- 조건부 액세스를 통해 차단

- 조건부 액세스 실패

- 조건부 액세스의 MFA

- 다른 요구 사항의 MFA

- MFA 증명 필요

- MFA 증명이 필요하지만 사용자 로그인 시도가 보안 위치에서 수행되지 않음

- 로그인 완료


### <a name="blocked-by-conditional-access"></a>조건부 액세스를 통해 차단

이 시나리오는 조건부 액세스 정책으로 차단된 로그인을 기반으로 합니다.

![액세스 차단](./media/overview-sign-in-diagnostics/block-access.png)

이 시나리오의 진단 섹션에는 사용자 로그인 및 적용된 정책에 대한 세부 정보가 표시됩니다.


### <a name="failed-conditional-access"></a>조건부 액세스 실패

이 시나리오는 일반적으로 조건부 액세스 정책의 요구 사항이 충족되지 않아 로그인이 실패한 결과입니다. 일반적인 예제는 다음과 같습니다.

![제어 필요](./media/overview-sign-in-diagnostics/require-controls.png)

- 하이브리드 Azure AD 조인 디바이스 필요

- 승인된 클라이언트 앱 필요

- 앱 보호 정책 필요   


이 시나리오의 진단 섹션에는 사용자 로그인 및 적용된 정책에 대한 세부 정보가 표시됩니다.


### <a name="mfa-from-conditional-access"></a>조건부 액세스의 MFA

이 시나리오는 다단계 인증 집합을 사용하여 로그인해야 하는 조건부 액세스 정책을 기반으로 합니다.

![다단계 인증 필요](./media/overview-sign-in-diagnostics/require-mfa.png)

이 시나리오의 진단 섹션에는 사용자 로그인 및 적용된 정책에 대한 세부 정보가 표시됩니다.



### <a name="mfa-from-other-requirements"></a>다른 요구 사항의 MFA

이 시나리오는 조건부 액세스 정책이 적용되지 않은 다단계 인증 요구 사항을 기반으로 합니다. 예를 들어 사용자별 다단계 인증이 있습니다.


![사용자별 다단계 인증 필요](./media/overview-sign-in-diagnostics/mfa-per-user.png)


이 진단 시나리오는 다음에 대한 자세한 정보를 제공하기 위한 것입니다.

- 다단계 인증 중단의 원본 
- 클라이언트 상호 작용의 결과

또한 이 섹션에서는 사용자 로그인 시도에 대한 모든 세부 정보도 제공합니다. 


### <a name="mfa-proof-up-required"></a>MFA 증명 필요

이 시나리오는 다단계 인증 설정 요청으로 인해 중단된 로그인을 기반으로 합니다. 이 설정을 "증명"이라고도 합니다.

사용자가 다단계 인증을 사용해야 하지만 아직 구성하지 않았거나 관리자가 사용자에게 이를 구성하도록 구성한 경우에 다단계 인증 증명이 발생합니다.

이 진단 시나리오는 다단계 인증 중단이 이를 설정하기 위한 것이라는 인사이트를 제공하고 사용자가 증명을 완료하도록 추천하기 위한 것입니다.

### <a name="mfa-proof-up-required-from-a-risky-sign-in"></a>위험한 로그인의 MFA 증명 필요

이 시나리오는 위험한 로그온에서 다단계 인증을 설정하라는 요청으로 인해 로그인이 중단된 것입니다. 

이 진단 시나리오는 다단계 인증 중단이 이를 설정하기 위한 것이라는 인사이트를 제공하고 사용자가 증명을 완료하지만 위험하지 않은 네트워크 위치에서 수행하도록 추천하기 위한 것입니다. 예를 들어 회사 네트워크가 명명된 위치로 정의된 경우 회사 네트워크에서 증명을 대신 수행하려고 시도합니다.


### <a name="successful-sign-in"></a>로그인 완료

이 시나리오는 조건부 액세스 또는 다단계 인증으로 인해 중단되지 않은 로그인을 기반으로 합니다.

이 진단 시나리오는 적용할 필요가 있는 조건부 액세스 정책 또는 정책이 있거나 사용자 로그인을 중단할 필요가 있는 구성된 다단계 인증이 있는 경우 로그인 중에 사용자가 제공한 항목에 대한 인사이트를 제공하기 위한 것입니다.



## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 보고서란?](overview-reports.md)
* [Azure Active Directory 모니터링이란?](overview-monitoring.md)
