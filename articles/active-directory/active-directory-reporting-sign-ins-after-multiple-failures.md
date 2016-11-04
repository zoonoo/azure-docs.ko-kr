---
title: 여러 번의 실패 후 로그인
description: 여러 번의 로그인 시도를 연속해서 실패한 이후 로그인에 성공한 사용자를 나타내는 보고서입니다.
services: active-directory
documentationcenter: ''
author: SSalahAhmed
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff

---
# <a name="sign-ins-after-multiple-failures"></a>여러 번의 실패 후 로그인
이 보고서에는 여러 번의 로그인 시도를 연속해서 실패한 이후 로그인에 성공한 사용자가 표시됩니다. 가능한 원인은 다음과 같습니다.

* 사용자가 암호를 잊어버린 경우</li><li>사용자의 계정이 암호 추측 무차별 암호 대입 공격(brute force attack)에 당한 경우

이 보고서의 결과에는 로그인하기 전에 연속해서 실패한 로그인 시도 수 및 처음 성공한 로그인과 연관된 타임스탬프가 표시됩니다.

**보고서 설정**: 보고서에 표시되기 전에 먼저 발생해야 하는 연속 실패한 최소 로그인 시도 횟수를 설정할 수 있습니다. 이 설정을 변경할 때에는 해당 설정이 현재 기존 보고서에 표시되는 실패한 기존 로그인에 적용되지 않도록 해야 합니다. 그러나 이러한 변경 사항은 향후 모든 로그인에 적용됩니다. 이 보고서에 대한 변경 작업은 허가받은 관리자만 수행할 수 있습니다.

![여러 번의 실패 후 로그인](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!--HONumber=Oct16_HO2-->


