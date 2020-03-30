---
title: What If 도구를 사용하여 조건부 액세스 문제 해결 - Azure Active Directory
description: 어떤 조건부 액세스 정책이 적용되었는지, 그 이유를 찾을 수 있는 위치
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
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175822"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>What If 도구를 사용하여 조건부 액세스 문제 해결

조건부 액세스의 [What If 도구는](what-if-tool.md) 특정 상황에서 정책이 사용자에게 적용된 이유 또는 적용되지 않은 이유를 이해하려고 할 때 또는 정책이 알려진 상태에 적용되는지 에 대해 매우 강력합니다.

What If 도구는 **Azure 포털** > **Azure Active Directory** > **조건부 액세스** > 에 있는**경우**.

![조건부 액세스 기본 상태에서 어떤 경우 도구](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If 도구는 현재 보고서 전용 모드에서 정책을 평가하지 않습니다.

## <a name="gathering-information"></a>정보 수집

What If 도구는 **사용자가** 시작하기만 하면 됩니다. 

다음 추가 정보는 선택 사항이지만 특정 사례의 범위를 좁히는 데 도움이 됩니다.

* 클라우드 앱 또는 작업
* IP 주소 
* 국가
* 디바이스 플랫폼
* 클라이언트 앱(미리 보기)
* 장치 상태(미리 보기) 
* 로그인 위험

이 정보는 사용자, 해당 장치 또는 Azure AD 로그인 로그에서 수집할 수 있습니다.

## <a name="generating-results"></a>결과 생성

이전 섹션에서 수집된 조건을 입력하고 결과 목록을 생성할 **If를** 선택합니다. 

언제든지 **재설정을** 선택하여 모든 조건 입력을 지우고 기본 상태로 돌아갈 수 있습니다.

## <a name="evaluating-results"></a>결과 평가

### <a name="policies-that-will-apply"></a>적용되는 정책

이 목록에는 조건에 따라 적용되는 조건부 액세스 정책이 표시됩니다. 목록에는 적용되는 권한 부여 및 세션 컨트롤이 모두 포함됩니다. 예를 들어 특정 응용 프로그램에 액세스하기 위해 다단계 인증을 요구하는 것이 있습니다.

### <a name="policies-that-will-not-apply"></a>적용되지 않는 정책

이 목록에는 조건이 적용되는 경우 적용되지 않는 조건부 액세스 정책이 표시됩니다. 목록에는 모든 정책과 정책이 적용되지 않는 이유가 포함됩니다. 예를 들어 정책에서 제외될 수 있는 사용자 및 그룹을 예로 들 수 있습니다.

## <a name="use-case"></a>사용 사례

많은 조직에서 네트워크 위치를 기반으로 정책을 만들어 신뢰할 수 있는 위치를 허용하고 액세스가 발생하지 않아야 하는 위치를 차단합니다.

구성이 적절하게 이루어졌는지 확인하기 위해 관리자는 What If 도구를 사용하여 허용되어야 하는 위치 및 거부해야 하는 위치에서 액세스를 모방할 수 있습니다.

![블록 액세스가 있는 결과를 표시하는 도구면 어떻게 해야 합니까?](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

이 경우 Contoso가 해당 위치에서 액세스를 차단했기 때문에 사용자는 북한 여행 시 클라우드 앱에 액세스하지 못하도록 차단됩니다.

이 테스트는 범위를 좁히기 위해 다른 데이터 점을 통합하도록 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [조건부 액세스란 무엇입니까?](overview.md)
* [Azure Active Directory Identity Protection이란?](../identity-protection/overview-v2.md)
* [디바이스 ID란?](../devices/overview.md)
* [작동 방법: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
