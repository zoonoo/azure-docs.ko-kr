---
title: What If 도구를 사용 하 여 조건부 액세스 문제 해결-Azure Active Directory
description: 조건부 액세스 정책을 적용 하는 위치 및 이유
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1d463bc889385f8f157ebb9892acc7a4a8b2a2e
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278520"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>What If 도구를 사용 하 여 조건부 액세스 문제 해결

조건부 액세스의 [What If 도구](what-if-tool.md) 는 특정 환경에서 정책이 사용자에 게 적용 되었거나 적용 되지 않은 이유를 이해 하거나 정책이 알려진 상태에서 적용 되는 경우에 유용 합니다.

What If 도구는 > **조건부 액세스**  > WhatIfAzurePortal > Azure Active Directory에 있습니다.

![조건부 액세스 What If 도구 (기본 상태)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

## <a name="gathering-information"></a>정보 수집

What If 도구를 사용 하려면 **사용자** 만 시작 하면 됩니다. 

다음 추가 정보는 선택 사항 이지만 특정 사례에 대 한 범위를 좁히는 데 도움이 됩니다.

* 클라우드 앱 또는 작업
* IP 주소 
* Country
* 디바이스 플랫폼
* 클라이언트 앱 (미리 보기)
* 장치 상태 (미리 보기) 
* 로그인 위험

이 정보는 사용자, 장치 또는 Azure AD 로그인 로그에서 수집할 수 있습니다.

## <a name="generating-results"></a>결과 생성

이전 섹션에서 수집한 조건을 입력 하 고 **What If** 를 선택 하 여 결과 목록을 생성 합니다. 

언제 든 지 **다시 설정** 을 선택 하 여 모든 조건 입력을 지우고 기본 상태로 돌아갈 수 있습니다.

## <a name="evaluating-results"></a>결과 평가

### <a name="policies-that-will-apply"></a>적용 될 정책

이 목록에서는 조건에 적용 되는 조건부 액세스 정책을 보여 줍니다. 이 목록에는 적용 되는 grant 및 session 컨트롤이 모두 포함 됩니다. 예를 들어 특정 응용 프로그램에 액세스 하기 위해 multi-factor authentication을 요구 하는 경우를 들 수 있습니다.

### <a name="policies-that-will-not-apply"></a>적용 하지 않을 정책

이 목록은 조건이 적용 되는 경우 적용 되지 않는 조건부 액세스 정책을 보여 줍니다. 이 목록에는 정책 및 적용 되지 않는 이유가 포함 됩니다. 예를 들면 정책에서 제외할 수 있는 사용자와 그룹이 있습니다.

## <a name="use-case"></a>사용 사례

많은 조직에서 네트워크 위치에 기반 하 여 정책을 만들어 신뢰할 수 있는 위치 및 액세스가 발생 하지 않아야 하는 위치를 차단 합니다.

구성이 적절 하 게 구성 되었는지 확인 하기 위해 관리자는 What If 도구를 사용 하 여 허용 해야 하는 위치 및 거부 해야 하는 위치에서 액세스를 모방할 수 있습니다.

![블록 액세스를 포함 하는 결과를 보여 주는 What If 도구](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

이 경우 Contoso가 해당 위치에서 액세스를 차단 했기 때문에 사용자는 북미로의 클라우드 앱 액세스를 차단 합니다.

이 테스트를 확장 하 여 범위를 좁힐 수 있는 다른 데이터 요소를 통합할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [조건부 액세스 란?](overview.md)
* [Azure Active Directory Identity Protection 이란?](../identity-protection/overview-v2.md)
* [디바이스 ID란?](../devices/overview.md)
* [작동 방법: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) 문서를 참조하세요
