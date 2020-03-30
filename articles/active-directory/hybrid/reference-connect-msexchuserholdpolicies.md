---
title: 'Azure AD 연결: msExchUser홀드정책 및 클라우드MsExchUser홀드정책 | 마이크로 소프트 문서'
description: 이 항목에서는 msExchUserHoldPolicy 및 cloudMsExchUserHoldPolicy 특성의 특성 동작에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213084"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD 연결 - msExchUser홀드정책 및 클라우드MsExchUser홀드정책
다음 참조 문서에서는 Exchange에서 사용하는 이러한 특성과 기본 동기화 규칙을 편집하는 적절한 방법을 설명합니다.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>msExchUser홀드정책 및 클라우드MsExchUser홀드정책은 무엇입니까?
Exchange 서버에 사용할 수 있는 [보류에는](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) 소송 보류와 내부 보류의 두 가지 유형이 있습니다. 소송 보류를 사용하도록 설정하면 모든 사서함이 보류됩니다.  인소 보류는 인소 eDiscovery 도구를 사용하여 정의한 검색 쿼리 의 기준을 충족하는 항목만 보존하는 데 사용됩니다.

MsExchUserHoldPolcies 및 cloudMsExchUserHoldPolicy 특성을 사용하면 온-프레미스 AD 및 Azure AD가 온-프레미스 Exchange 또는 Exchange 온라인 을 사용하는지 여부에 따라 보류 중인 사용자를 확인할 수 있습니다.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUser홀드정책 동기화 흐름
기본적으로 MsExchUserHoldPolcies는 azure AD Connect에 의해 동기화됩니다 msExchUserHoldPolicy 메타버스의 다음 msExchUserHoldPolices 속성에 직접 Azure AD

다음 표는 흐름을 설명합니다.

온-프레미스 활성 디렉토리에서 인바운드:

|Active 디렉토리 특성|특성 이름|흐름 형식|메타버스 속성|동기화 규칙|
|-----|-----|-----|-----|-----|
|온-프레미스 Active Directory|msExchUserHoldPolicies|직접|msExch사용자보류경찰|AD에서 - 사용자 교환|

Azure AD로의 아웃바운드:

|메타버스 속성|특성 이름|흐름 형식|Azure AD 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|직접|msExchUserHoldPolicies|AAD로 아웃 – 사용자 교환온라인|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicy 동기화 흐름
기본적으로 cloudMsExchUserHoldPolicy는 Azure ADConnect에 의해 동기화되어 메타버스의 cloudMsExchUserHoldPolicy 특성에 직접 연결됩니다. 그런 다음 msExchUserHoldPolices가 메타버스에서 null이 아닌 경우 의 특성이 Active Directory로 흘러나오게 됩니다.

다음 표는 흐름을 설명합니다.

Azure AD에서 인바운드:

|Active 디렉토리 특성|특성 이름|흐름 형식|메타버스 속성|동기화 규칙|
|-----|-----|-----|-----|-----|
|온-프레미스 Active Directory|클라우드엑세이치유저보류정책|직접|클라우드엑세이치유저보류정책|AAD에서 - 사용자 교환|

온-프레미스 활성 디렉토리로의 아웃바운드:

|메타버스 속성|특성 이름|흐름 형식|Azure AD 특성|동기화 규칙|
|-----|-----|-----|-----|-----|
|Azure Active Directory|클라우드엑세이치유저보류정책|IF(NULL 아님)|msExchUserHoldPolicies|AD로 나가기 - 사용자 교환온라인|

## <a name="information-on-the-attribute-behavior"></a>특성 동작에 대한 정보
msExchangeUserHoldPolicy는 단일 기관 특성입니다.  온-프레미스 디렉터리 또는 클라우드 디렉터리에서 개체(이 경우 사용자 개체)에서 단일 권한 특성을 설정할 수 있습니다.  권한 시작 규칙에 따라 특성이 온-프레미스에서 동기화되면 Azure AD는 이 특성을 업데이트할 수 없습니다.

사용자가 클라우드의 사용자 개체에 대해 보류 정책을 설정할 수 있도록 cloudMSExchangeUserHoldPolicy 특성이 사용됩니다. 이 특성은 Azure AD가 위에서 설명한 규칙에 따라 직접 msExchangeUserHoldPolicy를 설정할 수 없기 때문에 사용됩니다.  이 특성은 msExchangeUserHoldPolicy가 null이 아닌 경우 온-프레미스 디렉터리로 다시 동기화되고 msExchangeUserHoldPolicy의 현재 값을 대체합니다.

예를 들어 특정 상황에서 온-프레미스와 Azure에서 동시에 둘 다 변경된 경우 일부 문제가 발생할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
