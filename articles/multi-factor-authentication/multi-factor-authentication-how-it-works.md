---
title: "Azure Multi-Factor Authentication - 작동 방법"
description: "간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 다양한 쉬운 확인 옵션을 통해 강력한 인증을 제공합니다."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 9132e9bde7403e2868b92cb1149904d926ff5d8e
ms.lasthandoff: 02/23/2017


---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication 작동 방법
다단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 신뢰할 수 있는 장치가 없다면 어찌할 수 없습니다. 사용자가 장치를 분실한 경우에도 습득한 사람이 암호를 모르면 장치를 사용할 수 없습니다.

![검사](./media/multi-factor-authentication-how-it-works/howitworks.png)

간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 응용 프로그램에 대한 액세스를 보호합니다.  두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 다양한 쉬운 확인 옵션을 통해 강력한 인증을 제공합니다.

작동 방법에 대한 자세한 정보는 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]

## <a name="methods-available-for-two-step-verification"></a>2단계 인증을 위한 방법
사용자가 로그인하면 사용자에게 추가 확인이 전송됩니다.  이 두 번째 확인에 사용할 수 있는 방법의 목록은 다음과 같습니다.

| 확인 방법 | 설명 |
| --- | --- |
| 전화 통화 |우물 정자를 누르거나 PIN을 입력하여 로그인을 확인하도록 요청하는 통화가 사용자가 등록한 휴대폰으로 갑니다. |
| 문자 메시지 |6자리 코드가 있는 문자 메시지가 사용자의 휴대폰으로 전송됩니다.  확인 프로세스를 완료하려면 이 코드를 입력합니다. |
| 모바일 앱 알림 |모바일 앱에서 [확인]을 선택하여 확인을 완료하라는 확인 요청이 사용자의 스마트폰에 전송됩니다. 기본 확인 방법으로 앱 알림을 선택한 경우 전송됩니다.  로그인하지 않았을 때 이 알림을 수신하는 경우 사기 행위로 보고하도록 선택할 수 있습니다. |
| 모바일 앱 확인 코드 |사용자의 스마트폰에서 실행되고 있는 모바일 앱에 30초마다 변경되는 6자리 확인 코드가 표시됩니다. 사용자가 가장 최근 코드를 찾아 로그인 페이지에 입력하여 확인 과정을 완료합니다. 기본 확인 방법으로 확인 코드를 선택한 경우 전송됩니다. |
| 타사 OATH 토큰 | 타사 확인 방법을 허용하도록 Azure Multi-Factor Authentication을 구성할 수 있습니다. |

Azure Multi-Factor Authentication은 클라우드와 서버 모두에 대해 선택 가능한 확인 방법을 제공합니다. 즉, 전화 통화, 텍스트, 앱 알림, 앱 코드 중에서 사용자에게 제공할 방법을 선택할 수 있다는 의미입니다. 자세한 내용은 [선택 가능한 확인 방법](multi-factor-authentication-whats-next.md#selectable-verification-methods)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 다른 [Azure Multi-Factor Authentication에 대한 다양한 버전 및 사용량 메서드](multi-factor-authentication-versions-plans.md)에 대해 알아보기

- [클라우드 또는 온-프레미스](multi-factor-authentication-get-started.md)에서 Azure MFA를 배포할지 여부 선택
