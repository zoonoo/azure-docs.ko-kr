<properties
	pageTitle="여러 지역에서의 로그인"
	description="한 사용자의 두 로그인이 각각 다른 지역에서 시작된 것으로 보이고 로그인 간격이 사용자가 두 지역 간에 이동하기에는 불가능한 시간임을 나타내는 보고서입니다."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# 여러 지역에서의 로그인

한 사용자의 두 로그인이 각각 다른 지역에서 시작된 것으로 보이고 로그인 간격이 사용자가 두 지역 간에 이동하기에는 불가능한 시간인 경우 해당 사용자의 모든 성공적인 로그인이 이 보고서에 포함됩니다. 가능한 원인은 다음과 같습니다.

- 사용자가 다른 사용자와 암호를 공유하고 있는 경우

- 사용자가 로그인을 위해 웹 브라우저를 시작하는 데 원격 데스크톱을 사용하고 있는 경우

- 해커가 다른 국가에서 사용자의 계정에 로그인한 경우

- 사용자가 VPN 또는 프록시를 사용하고 있는 경우

- 사용자가 데스크톱, 휴대폰 등 여러 장치에서 동시에 로그인했고 휴대폰의 IP 주소가 일반적이지 않습니다.

이 보고서의 결과에는 로그인 간 시간, 로그인이 발생한 것으로 보이는 지역 및 해당 지역 간 예상되는 이동 시간과 함께 성공적인 로그인 이벤트가 표시됩니다. 표시된 이동 시간은 예상치이며 위치 사이의 실제 이동 시간과 다를 수 있습니다.


![여러 지역에서의 로그인](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=AcomDC_0309_2016-->