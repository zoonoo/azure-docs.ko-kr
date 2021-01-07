---
title: Azure 센티널 UEBA 강화 참조 | Microsoft Docs
description: 이 문서에서는 Azure 센티널의 엔터티 동작 분석에 의해 생성 된 엔터티 강화를 표시 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901728"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Azure 센티널 UEBA 강화 참조

이러한 표에서는 보안 인시던트에 대 한 조사를 집중 하 고 선명 하 게 하는 데 사용할 수 있는 엔터티 강화 나열 하 고 설명 합니다.

처음 두 테이블, **사용자 정보** 및 **장치** 정보에는 Active Directory/Azure AD 및 Microsoft 위협 인텔리전스 원본의 엔터티 정보가 포함 되어 있습니다.

<a name="baseline-explained"></a>**활동 정보 테이블** 아래에 있는 나머지 테이블에는 Azure 센티널의 엔터티 동작 분석에서 빌드된 동작 프로필을 기반으로 하는 엔터티 정보가 포함 되어 있습니다. 활동은 사용 될 때마다 동적으로 컴파일되는 기준선에 대해 분석 됩니다. 각 활동에는이 동적 기준이 파생 되는 정의 된 lookback 기간이 있습니다. 이 기간은이 테이블의 [**기준**](#activity-insights-tables) 열에 지정 됩니다.

> [!NOTE] 
> 세 테이블 모두의 **보강 name** 필드는 두 개의 정보 행을 표시 합니다. **굵게 표시** 되는 첫 번째는 보강의 "식별 이름"입니다. 두 번째 *(기울임꼴 및 괄호)* 는 [**동작 분석 테이블**](identify-threats-with-entity-behavior-analytics.md#data-schema)에 저장 된 보강의 필드 이름입니다.

## <a name="user-insights-table"></a>사용자 정보 테이블

| 보강 이름 | Description | 샘플 값 |
| --- | --- | --- | --- |
| **계정 표시 이름**<br>*(AccountDisplayName)* | 사용자의 계정 표시 이름입니다. | 관리자, Hayden 쿡 |
| **계정 도메인**<br>*(AccountDomain)* | 사용자의 계정 도메인 이름입니다. |  |
| **계정 개체 ID**<br>*(AccountObjectID)* | 사용자의 계정 개체 ID입니다. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **폭발 반경**<br>*(BlastRadius)* | 폭발 반지름은 조직 트리의 사용자 위치와 사용자 Azure Active Directory 역할 및 사용 권한에 따라 여러 가지 요소를 기준으로 계산 됩니다. | 낮음, 보통, 높음 |
| **유휴 계정**<br>*(IsDormantAccount)* | 지난 180 일간 계정이 사용 되지 않았습니다. | True, False |
| **로컬 관리자**<br>*(IsLocalAdmin)* | 계정에 로컬 관리자 권한이 있습니다. | True, False |
| **새 계정**<br>*(IsNewAccount)* | 계정이 지난 30 일 이내에 만들어졌습니다. | True, False |
| **온-프레미스 SID**<br>*(OnPremisesSID)* | 작업과 관련 된 사용자의 온-프레미스 SID입니다. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Device insights 테이블

| 보강 이름 | Description | 샘플 값 |
| --- | --- | --- | --- |
| **브라우저**<br>*브라우저가* | 작업에 사용 되는 브라우저입니다. | Edge, Chrome |
| **디바이스 패밀리**<br>*(DeviceFamily)* | 작업에 사용 되는 장치 패밀리입니다. | Windows |
| **디바이스 유형**<br>*DeviceType* | 작업에 사용 되는 클라이언트 장치 유형입니다. | 데스크톱 |
| **P**<br>*P* | 작업에 사용 되는 인터넷 서비스 공급자입니다. |  |
| **운영 체제**<br>*OperatingSystem* | 작업에 사용 된 운영 체제입니다. | Windows 10 |
| **위협에 대 한 기술 지표 설명**<br>*(ThreatIntelIndicatorDescription)* | 작업에 사용 된 IP 주소에서 확인 된 관찰 된 위협 표시기의 설명입니다. | 호스트는 봇 넷: azorult의 멤버입니다. |
| **위협 intel 표시기 유형**<br>*(ThreatIntelIndicatorType)* | 작업에 사용 된 IP 주소에서 확인 된 위협 표시기의 유형입니다. | 봇 넷, C2, CryptoMining, Darknet, Ddos, MaliciousUrl, 맬웨어, 피싱, 프록시, PUA, 관심 목록 |
| **사용자 에이전트**<br>*UserAgent* | 작업에 사용 되는 사용자 에이전트입니다. | Microsoft Azure Graph Client Library 1.0,<br>Swagger-Codegen/1.4.0.0/csharp,<br>EvoSTS |
| **사용자 에이전트 제품군**<br>*(UserAgentFamily)* | 작업에 사용 되는 사용자 에이전트 패밀리입니다. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>활동 insights 테이블

#### <a name="action-performed"></a>수행한 작업

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 처음으로 작업을 수행 하는 시간**<br>*(FirstTimeUserPerformedAction)* | 180 | 사용자가 작업을 처음으로 수행 했습니다. | True, False |
| **사용자가 일반적으로 수행 하지 않은 작업**<br>*(ActionUncommonlyPerformedByUser)* | 10 | 사용자가 작업을 일반적으로 수행 하지 않습니다. | True, False |
| **피어 사이에서 일반적으로 수행 되지 않는 작업**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | 작업은 일반적으로 사용자의 피어 간에 수행 되지 않습니다. | True, False |
| **테 넌 트에서 처음으로 수행 되는 작업**<br>*(FirstTimeActionPerformedInTenant)* | 180 | 작업은 조직의 모든 사용자가 처음으로 수행 했습니다. | True, False |
| **테 넌 트에서 일반적으로 수행 되지 않는 작업**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | 작업은 일반적으로 조직에서 수행 되지 않습니다. | True, False |
|

#### <a name="app-used"></a>앱 사용 됨

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 앱을 처음으로 사용**<br>*(FirstTimeUserUsedApp)* | 180 | 사용자가 앱을 처음으로 사용 했습니다. | True, False |
| **사용자가 일반적으로 사용 하지 않는 앱**<br>*(AppUncommonlyUsedByUser)* | 10 | 앱은 일반적으로 사용자가 사용 하지 않습니다. | True, False |
| **피어 간에 일반적으로 사용 되지 않는 앱**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | 앱은 일반적으로 사용자의 피어 간에 사용 되지 않습니다. | True, False |
| **앱이 테 넌 트에서 처음 관찰 된 시간**<br>*(FirstTimeAppObservedInTenant)* | 180 | 조직에서 처음으로 앱을 관찰 했습니다. | True, False |
| **테 넌 트에서 일반적으로 사용 되지 않는 앱**<br>*(AppUncommonlyUsedInTenant)* | 180 | 앱은 일반적으로 조직에서 사용 되지 않습니다. | True, False |
| 

#### <a name="browser-used"></a>사용 되는 브라우저

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 브라우저를 통해 처음으로 연결한 시간**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | 사용자가 처음으로 브라우저를 관찰 했습니다. | True, False |
| **사용자가 일반적으로 사용 하지 않는 브라우저**<br>*(BrowserUncommonlyUsedByUser)* | 10 | 브라우저는 일반적으로 사용자가 사용 하지 않습니다. | True, False |
| **피어 간에 일반적으로 사용 되지 않는 브라우저**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | 브라우저는 일반적으로 사용자의 피어 간에 사용 되지 않습니다. | True, False |
| **처음으로 테 넌 트에서 브라우저가 관찰 된 시간**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | 조직에서 처음으로 브라우저가 관찰 되었습니다. | True, False |
| **테 넌 트에서 일반적으로 사용 되지 않는 브라우저**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | 브라우저는 조직에서 일반적으로 사용 되지 않습니다. | True, False |
| 

#### <a name="country-connected-from"></a>국가 연결 원본

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 country에서 처음 연결한 시간**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | IP 주소에서 확인 된 지리적 위치가 사용자에 의해 처음으로 연결 되었습니다. | True, False |
| **사용자가 일반적으로 연결 하지 않은 국가**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | IP 주소에서 확인 된 지리적 위치는 일반적으로 사용자가 연결 하지 않습니다. | True, False |
| **피어에서 일반적으로 연결 되지 않은 국가**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | IP 주소에서 확인 된 지리적 위치는 일반적으로 사용자의 피어에서 연결 되지 않습니다. | True, False |
| **테 넌 트에서 관찰 된 국가에서의 첫 번째 연결**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | 이 국가는 조직의 누구나 처음으로 연결 되었습니다. | True, False |
| **테 넌 트에서 일반적으로 연결 되지 않은 국가**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | IP 주소에서 확인 된 지리적 위치는 일반적으로 조직의에서 연결 되지 않습니다. | True, False |
| 

#### <a name="device-used-to-connect"></a>연결에 사용 되는 장치

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 장치에서 처음으로 연결한 시간**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | 사용자가 처음으로 원본 장치를 연결 했습니다. | True, False |
| **사용자가 일반적으로 사용 하지 않는 장치**<br>*(DeviceUncommonlyUsedByUser)* | 10 | 사용자가 장치를 일반적으로 사용 하지 않습니다. | True, False |
| **피어 간에 일반적으로 사용 되지 않는 장치**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | 장치는 일반적으로 사용자의 피어 간에 사용 되지 않습니다. | True, False |
| **장치가 테 넌 트에서 처음 관찰 된 시간**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | 조직에서 처음으로 장치를 관찰 했습니다. | True, False |
| **테 넌 트에서 일반적으로 사용 되지 않는 장치**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | 장치는 조직에서 일반적으로 사용 되지 않습니다. | True, False |
| 

#### <a name="other-device-related"></a>기타 장치 관련

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **처음으로 사용자가 장치에 로그온 한 시간**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | 사용자가 처음으로 대상 장치에 연결 했습니다. | True, False |
| **테 넌 트에서 일반적으로 사용 되지 않는 장치 패밀리**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | 장치 제품군은 일반적으로 조직에서 사용 되지 않습니다. | True, False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>연결에 사용 되는 인터넷 서비스 공급자

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **ISP를 통해 사용자를 처음으로 연결한 시간**<br>*(FirstTimeUserConnectedViaISP)* | 30 | 사용자가 처음으로 ISP를 관찰 했습니다. | True, False |
| **사용자가 일반적으로 사용 하지 않는 ISP**<br>*(ISPUncommonlyUsedByUser)* | 10 | ISP는 일반적으로 사용자가 사용 하지 않습니다. | True, False |
| **피어 간에 일반적으로 사용 되지 않는 ISP**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | ISP는 일반적으로 사용자의 피어 간에 사용 되지 않습니다. | True, False |
| **테 넌 트에서 ISP를 통해 첫 번째 연결**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | ISP는 조직에서 처음으로 관찰 되었습니다. | True, False |
| **테 넌 트에서 일반적으로 사용 되지 않는 ISP**<br>*(ISPUncommonlyUsedInTenant)* | 30 | ISP는 조직에서 일반적으로 사용 되지 않습니다. | True, False |
| 

#### <a name="resource-accessed"></a>액세스한 리소스

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 처음으로 리소스에 액세스 한 시간**<br>*(FirstTimeUserAccessedResource)* | 180 | 사용자가 리소스를 처음으로 액세스 했습니다. | True, False |
| **사용자가 일반적으로 액세스 하지 않는 리소스**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | 일반적으로 사용자가 리소스에 액세스 하지 않습니다. | True, False |
| **피어 간에 일반적으로 액세스 되지 않는 리소스**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | 리소스는 일반적으로 사용자의 피어 간에 액세스 되지 않습니다. | True, False |
| **테 넌 트에서 처음으로 액세스 한 시간**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | 조직의 모든 사용자가 처음으로 리소스에 액세스 했습니다. | True, False |
| **테 넌 트에서 일반적으로 액세스 되지 않는 리소스**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | 리소스는 일반적으로 조직에서 액세스 되지 않습니다. | True, False |
| 

#### <a name="miscellaneous"></a>기타

| 보강 이름 | [기준](#baseline-explained) (일) | Description | 샘플 값 |
| --- | --- | --- | --- |
| **사용자가 작업을 마지막으로 수행한 시간**<br>*(LastTimeUserPerformedAction)* | 180 | 사용자가 동일한 작업을 마지막으로 수행한 시간입니다. | <Timestamp> |
| **이전에 유사한 작업이 수행 되지 않았습니다.**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | 사용자가 동일한 리소스 공급자에서 작업을 수행 하지 않았습니다. | True, False |
| **원본 IP 위치**<br>*(SourceIPLocation)* | *해당 사항 없음* | 작업의 원본 IP에서 확인 되는 국가입니다. | [Surrey, 영국] |
| **일반적이 지 않은 대량 작업**<br>*(UncommonHighVolumeOfOperations)* | 7 | 사용자가 같은 공급자 내에서 유사한 작업을 수행 합니다. | True, False |
| **비정상적인 수의 Azure AD 조건부 액세스 실패 수**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | 조건부 액세스로 인해 비정상적인 수의 사용자를 인증 하지 못했습니다. | True, False |
| **비정상적으로 많은 수의 장치를 추가 했습니다.**<br>*(UnusualNumberOfDevicesAdded)* | 5 | 사용자가 비정상적으로 많은 수의 장치를 추가 했습니다. | True, False |
| **비정상적으로 삭제 된 장치 수**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | 사용자가 비정상적으로 많은 수의 장치를 삭제 했습니다. | True, False |
| **그룹에 추가 된 비정상적인 사용자 수**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | 사용자가 그룹에 비정상적인 수의 사용자를 추가 했습니다. | True, False |
|