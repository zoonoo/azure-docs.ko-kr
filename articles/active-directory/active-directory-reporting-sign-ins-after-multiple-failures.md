<properties
	pageTitle="여러 번의 실패 후 로그인"
	description="여러 번의 로그인 시도를 연속해서 실패한 이후 로그인에 성공한 사용자를 나타내는 보고서입니다."
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

# 여러 번의 실패 후 로그인

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 이 보고서에는 여러 번의 로그인 시도를 연속해서 실패한 이후 로그인에 성공한 사용자가 표시됩니다. 다음과 같은 경우가 해당될 수 있습니다. <ul><li>사용자가 암호를 잊어버린 경우</li><li>사용자의 계정이 무차별 암호 추측 공격에 당한 경우</li></ul><p>이 보고서의 결과는 로그인하기 전에 연속해서 실패한 로그인 시도 수 및 처음 성공한 로그인과 연관된 타임스탬프를 보여 줍니다.</p><p><b>보고서 설정</b>: 보고서에 표시되기 전에 먼저 발생해야 하는 연속 실패한 최소 로그인 시도 횟수를 설정할 수 있습니다. 이 설정을 변경할 때에는 해당 설정이 현재 기존 보고서에 표시되는 실패한 기존 로그인에 적용되지 않도록 해야 합니다. 그러나 이러한 변경 사항은 향후 모든 로그인에 적용됩니다. 이 보고서에 대한 변경 작업은 허가받은 관리자만 수행할 수 있습니다. | 디렉터리 > 보고서 탭 |

![여러 번의 실패 후 로그인](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!---HONumber=August15_HO8-->