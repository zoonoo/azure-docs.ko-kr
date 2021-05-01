---
title: 'Azure AD Connect: msExchUserHoldPolicies 및 cloudMsExchUserHoldPolicies | Microsoft Docs'
description: 이 항목에서는 msExchUserHoldPolicies 및 cloudMsExchUserHoldPolicies 특성의 특성 동작에 대해 설명합니다.
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
ms.openlocfilehash: 0fbda588d99de44c77118586519055a8fc474104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861768"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies 및 cloudMsExchUserHoldPolicies
다음 참조 문서에서는 Exchange에서 사용되는 이러한 특성과 기본 동기화 규칙을 편집하는 적절한 방법을 설명합니다.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>msExchUserHoldPolicies 및 cloudMsExchUserHoldPolicies란?
Exchange Server에서는 두 가지 유형의 [보존](/Exchange/policy-and-compliance/holds/holds) 방식인 소송 보존 및 원본 위치 유지를 사용할 수 있습니다. 소송 보존을 사용하도록 설정하면 모든 사서함의 모든 항목이 보존 상태가 됩니다.  원본 위치 유지는 원본 위치 eDiscovery 도구를 사용하여 정의한 검색 쿼리 조건을 충족하는 항목만 보존하는 데 사용됩니다.

MsExchUserHoldPolcies 및 cloudMsExchUserHoldPolicies 특성을 사용하면 온-프레미스 AD 및 Azure AD에서 온-프레미스 Exchange를 사용하는지 아니면 Exchange Online을 사용하는지에 따라 보류되는 사용자를 결정할 수 있습니다.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies 동기화 흐름
기본적으로 Azure AD Connect에서는 MsExchUserHoldPolcies를 메타버스의 msExchUserHoldPolicies 특성과 직접 동기화한 후 Azure AD의 msExchUserHoldPolicies 특성과 동기화합니다.

다음 표에서는 흐름에 대해 설명합니다.

온-프레미스 Active Directory에서 인바운드:

|Active Directory 특성|특성 이름|흐름 형식|메타버스 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|온-프레미스 Active Directory|msExchUserHoldPolicies|직접|msExchUserHoldPolicies|AD에서 들어오기 – User Exchange|

Azure AD로 아웃바운드:

|메타버스 특성|특성 이름|흐름 형식|Azure AD 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|직접|msExchUserHoldPolicies|AAD로 나가기 – User ExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies 동기화 흐름
기본적으로 Azure AD Connect에서는 cloudMsExchUserHoldPolicies를 메타버스의 cloudMsExchUserHoldPolicies 특성과 직접 동기화합니다. 그런 다음, msExchUserHoldPolicies가 메타버스에서 null이 아닌 경우 특성이 Active Directory로 이동됩니다.

다음 표에서는 흐름에 대해 설명합니다.

Azure AD에서 인바운드:

|Active Directory 특성|특성 이름|흐름 형식|메타버스 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|온-프레미스 Active Directory|cloudMsExchUserHoldPolicies|직접|cloudMsExchUserHoldPolicies|AAD에서 들어오기 - User Exchange|

온-프레미스 Active Directory로 아웃바운드:

|메타버스 특성|특성 이름|흐름 형식|Azure AD 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF(NOT NULL)|msExchUserHoldPolicies|AD로 나가기 – User ExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>특성 동작에 대한 정보
msExchangeUserHoldPolicies는 단일 권한 특성입니다.  단일 권한 특성은 온-프레미스 디렉터리 또는 클라우드 디렉터리의 개체(이 경우 사용자 개체)에 대해 설정할 수 있습니다.  권한 시작 규칙의 시작은 특성이 온-프레미스에서 동기화되는 경우 Azure AD에서 이 특성을 업데이트하는 것을 허용하지 않도록 지정합니다.

사용자가 클라우드의 사용자 개체에 대해 보존 정책을 설정할 수 있도록 하려면 cloudMSExchangeUserHoldPolicies 특성이 사용됩니다. 이 특성은 Azure AD에서 위에 설명된 규칙을 기준으로 msExchangeUserHoldPolicies를 직접 설정할 수 없기 때문에 사용됩니다.  msExchangeUserHoldPolicies가 null이 아닌 경우 이 특성은 온-프레미스 디렉터리와 다시 동기된 후 msExchangeUserHoldPolicies의 현재 값을 바꿉니다.

특정 상황에서, 예를 들어 온-프레미스와 Azure에서 모두 동시에 변경되면 몇 가지 문제가 발생할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
