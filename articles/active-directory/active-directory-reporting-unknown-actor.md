<properties
   pageTitle="Azure Active Directory Reporting '알 수 없는 작업자' | Microsoft Azure"
   description="Azure Active Directory 보고서의 '알 수 없는 작업자' 이벤트에 대한 설명"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/30/2015"
   ms.author="kenhoff"/>

# Azure Active Directory Reporting '알 수 없는 작업자' 이벤트

## Reporting 설명서

 - [Reporting API](active-directory-reporting-api-getting-started.md)
 - [감사 이벤트](active-directory-reporting-audit-events.md)
 - [보존](active-directory-reporting-retention.md)
 - [미리 보기](active-directory-reporting-previews.md)
 - [이를 통해 검색](active-directory-reporting-search.md)
 - [백필](active-directory-reporting-backfill.md)
 - [대기 시간](active-directory-reporting-latencies.md)
 - ["알 수 없는 작업자" 이벤트](active-directory-reporting-unknown-actor.md)

가끔 Azure AD 보고서의 "작업자" 또는 "사용자" 필드에 비정상적인 값이 표시될 수 있습니다. 이 동작은 예상되었으며 다음 두 이벤트 중 하나 때문에 발생합니다.

## 서비스 주체가 사용자 컨텍스트 없이 디렉터리에서 작업함

이 경우 서비스 주체(응용 프로그램)는 실제로 사용자로 로그인하지 않고 디렉터리 업데이트를 수행합니다. 이렇게 하면 서비스 주체의 ID는 UPN 대신 작업자로 표시됩니다. 예를 들면 다음과 같습니다.

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

이 문제는 알려진 버그이며 이 문제를 해결하기 위해 열심히 노력 중입니다.

## 이벤트가 처리되기 전에 사용자가 디렉터리에서 삭제됨

이 경우 이벤트를 처리하고 사용자 이름과 연결하기 전에 사용자가 디렉터리에서 삭제되었습니다. 예를 들면 다음과 같습니다.

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

이 문제는 알려진 버그이며 이 문제를 해결하기 위해 열심히 노력 중입니다.

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=AcomDC_1203_2015-->