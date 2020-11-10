---
title: 'Azure AD Connect: msExchUserHoldPolicies 및 cloudMsExchUserHoldPolicies | Microsoft Docs'
description: 이 항목에서는 msExchUserHoldPolicies 및 cloudMsExchUserHoldPolicies 특성의 특성 동작에 대해 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24820949eb6762caf808c4420ede6f0f59184281
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412598"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies 및 cloudMsExchUserHoldPolicies
다음 참조 문서에서는 Exchange에서 사용 되는 이러한 특성과 기본 동기화 규칙을 편집 하는 적절 한 방법을 설명 합니다.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>MsExchUserHoldPolicies 및 cloudMsExchUserHoldPolicies 이란?
Exchange 서버에 사용할 수 있는 두 가지 유형으로는 소송 보류와 [In-Place 보유이](/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) 있습니다. 소송 보류를 사용 하는 경우 모든 사서함 모든 항목이 보류 중으로 배치 됩니다.  In-Place 보류는 In-Place eDiscovery 도구를 사용 하 여 정의한 검색 쿼리 조건을 충족 하는 항목만 보존 하는 데 사용 됩니다.

MsExchUserHoldPolcies 및 cloudMsExchUserHoldPolicies 특성을 사용 하면 온-프레미스 AD 및 Azure AD에서 온-프레미스 Exchange를 사용 하는지 아니면 Exchange 온라인에서 사용 되는지에 따라 보유 중인 사용자를 확인할 수 있습니다.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies 동기화 흐름
기본적으로 MsExchUserHoldPolcies는 메타 버스의 msExchUserHoldPolicies 특성에 직접 Azure AD Connect 하 여 Azure AD의 msExchUserHoldPolicies 특성으로 동기화 됩니다.

다음 표에서는 흐름에 대해 설명 합니다.

온-프레미스 Active Directory에서 인바운드:

|Active Directory 특성|특성 이름|흐름 형식|메타 버스 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|온-프레미스 Active Directory|msExchUserHoldPolicies|직접|msExchUserHoldPolicies|In from AD-User Exchange|

Azure AD로 아웃 바운드:

|메타 버스 특성|특성 이름|흐름 형식|Azure AD 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|직접|msExchUserHoldPolicies|Out to AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies 동기화 흐름
기본적으로 cloudMsExchUserHoldPolicies는 메타 버스의 cloudMsExchUserHoldPolicies 특성에 직접 Azure AD Connect 의해 동기화 됩니다. 그런 다음 msExchUserHoldPolicies가 메타 버스에서 null이 아닌 경우의 특성이 Active Directory로 이동 합니다.

다음 표에서는 흐름에 대해 설명 합니다.

Azure AD에서 인바운드:

|Active Directory 특성|특성 이름|흐름 형식|메타 버스 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|온-프레미스 Active Directory|cloudMsExchUserHoldPolicies|직접|cloudMsExchUserHoldPolicies|AAD에서 사용자 교환|

온-프레미스로 아웃 바운드 Active Directory:

|메타 버스 특성|특성 이름|흐름 형식|Azure AD 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Out to AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>특성 동작에 대 한 정보
MsExchangeUserHoldPolicies은 단일 기관 특성입니다.  단일 기관 특성은 온-프레미스 디렉터리 또는 클라우드 디렉터리의 개체 (이 경우 사용자 개체)에서 설정할 수 있습니다.  권한 시작 규칙의 시작은 특성이 온-프레미스에서 동기화 되는 경우 Azure AD에서이 특성을 업데이트 하는 것을 허용 하지 않습니다.

사용자가 클라우드의 사용자 개체에 대 한 보류 정책을 설정할 수 있도록 하려면 cloudMSExchangeUserHoldPolicies 특성이 사용 됩니다. 이 특성은 Azure AD에서 위에 설명 된 규칙을 기반으로 msExchangeUserHoldPolicies를 직접 설정할 수 없기 때문에 사용 됩니다.  MsExchangeUserHoldPolicies이 null이 아닌 경우이 특성은 온-프레미스 디렉터리에 다시 동기화 하 고 msExchangeUserHoldPolicies의 현재 값을 바꿉니다.

특정 상황에서는 예를 들어 온-프레미스와 Azure에서 모두 동시에 변경 된 경우 몇 가지 문제가 발생할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.