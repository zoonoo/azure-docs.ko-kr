<properties
	pageTitle="Azure Active Directory B2C 미리 보기: 서비스 상태 | Microsoft Azure"
	description="사소한 Azure Active Directory B2C 문제, 상태 및 완화에 대한 알림"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: 서비스 상태

이 페이지에서는 사소한 서비스 문제, 상태 및 완화에 대한 알림을 제공합니다. 나중에 참조할 수 있도록 이 페이지에 책갈피를 설정할 수 있습니다. [Azure 상태 대시보드](https://azure.microsoft.com/status/)도 계속 모니터링합니다.

### 2016년 3월 22일: B2C 테넌트의 SSO 버그

2016년 3월 17일 오후 1시(태평양 표준시)에 SSO(Single Sign On) 버그가 유입되었으며 2016년 3월 18일 오전 10시(태평양 표준시)에 완화되었습니다. 이 기간 동안 100명 미만의 소비자가 영향을 받았습니다. 이 문제를 계속해서 면밀히 모니터링하고 있습니다. 이 버그를 경험한 소비자의 상태:

1. "로컬 계정"이 있는 로그인 정책을 유일하게 구성된 ID 공급자로 설정했습니다.
2. 소비자가 처음으로 Azure AD B2C에 로그인에 성공했습니다.
3. 소비자가 다시 로그인하려고 하자 SSO가 진행되지 않고 오류 메시지가 표시되었습니다.

3단계 이후 이 소비자를 위한 유일한 해결 방법은 브라우저를 닫았다가 다시 열고 다시 인증을 받는 것이었습니다.

<!---HONumber=AcomDC_0323_2016-->