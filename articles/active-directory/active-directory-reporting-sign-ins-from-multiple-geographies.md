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
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# 여러 지역에서의 로그인

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| <p>한 사용자의 두 로그인이 각각 다른 지역에서 시작된 것으로 보이고 로그인 간격이 사용자가 두 지역 간에 이동하기에는 불가능한 시간인 경우 해당 사용자의 모든 성공적인 로그인 활동이 이 보고서에 포함됩니다. 다음과 같은 경우가 해당될 수 있습니다.</p><ul><li>사용자가 암호를 공유하고 있는 경우</li><li>사용자가 로그인할 웹 브라우저를 원격 데스크톱에서 시작하는 경우</li><li>해커가 다른 국가에서 사용자의 계정에 로그인한 경우</li></ul><p>이 보고서의 결과는 성공적인 로그인 이벤트와 함께 로그인 간의 시간, 로그인이 시작된 것으로 나타난 지역 및 두 지역을 이동하는 데 걸리는 예상 시간을 보여 줍니다.</p><p>표시된 이동 시간은 예측 시간일 뿐이며 두 지역을 실제로 이동하는 데 걸리는 시간과 차이가 날 수 있습니다. 또한 인접 지역 간의 로그인에 대한 이벤트는 생성되지 않습니다.</p> | 디렉터리 > 보고서 탭 |

![여러 지역에서의 로그인](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=August15_HO8-->