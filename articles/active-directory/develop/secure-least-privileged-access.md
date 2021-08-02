---
title: Azure AD의 최소 권한 액세스에 대한 모범 사례 - Microsoft ID 플랫폼
description: 최소 권한에 대한 모범 사례 및 일반적인 지침 모음에 대해 알아봅니다.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/26/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: db2eb2dcda894b997f485795ab62d983966a7f2f
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372779"
---
# <a name="best-practices-for-least-privileged-access-for-applications"></a>애플리케이션에 대한 최소 권한 액세스를 위한 모범 사례

최소 권한의 원칙은 사용자 및 애플리케이션에 필요한 작업을 수행하는 데 필요한 최소 수준의 액세스 권한을 부여해야 한다는 논리를 적용하는 정보 보안 개념입니다. 최소 권한의 원칙을 이해하면 고객을 위해 신뢰할 수 있는 애플리케이션을 빌드하는 데 도움이 됩니다.

최소 권한 도입은 좋은 보안 방법 이상입니다. 이 개념은 데이터의 무결성과 보안을 유지하는 데 도움이 됩니다. 또한 애플리케이션에서 절대적으로 필요한 수준 이상의 데이터에 액세스할 수 없도록 하여 데이터의 개인 정보를 보호하고 위험을 줄여줍니다. 좀 더 광범위한 수준에서 바라보면 최소 권한 원칙을 채택하는 것은 조직이 [제로 트러스트](https://www.microsoft.com/security/business/zero-trust)로 사전 보안을 수용하는 방법 중 하나입니다.

이 문서에서는 최종 사용자를 위해 애플리케이션의 보안을 강화하기 위해 최소 권한 원칙을 채택하는 데 사용할 수 있는 모범 사례 모음을 설명합니다. 다음과 같은 최소 권한 측면을 이해하게 됩니다.
- 권한에 대해 동의가 진행되는 방법
- 앱이 과도 권한 또는 최소 권한으로 사용된다는 것은 무엇을 의미하나요?
- 개발자로서 최소 권한에 근접하는 방법
- 조직으로서 최소 권한에 근접하는 방법

## <a name="using-consent-to-control-access-permissions-to-data"></a>동의를 사용하여 데이터에 대한 액세스 권한 제어

보호된 데이터에 액세스하려면 최종 사용자의 [동의](../develop/application-consent-experience.md#consent-and-permissions)를 받아야 합니다. 사용자의 디바이스에서 실행되는 애플리케이션이 보호된 데이터에 대한 액세스를 요청할 때마다 앱은 보호된 데이터에 대한 액세스 권한을 부여하기 전에 사용자의 동의를 요청해야 합니다. 최종 사용자는 애플리케이션 진행을 위해 요청된 권한에 대한 동의를 허용하거나 거부해야 합니다. 애플리케이션 개발자는 최소 권한을 사용하여 액세스 권한을 요청하는 것이 가장 좋습니다.

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="앱 등록의 API 권한 창을 보여 주는 Azure Portal 스크린샷":::

## <a name="overprivileged-and-least-privileged-applications"></a>과도 권한 및 최소 권한 애플리케이션

과도 권한 애플리케이션에는 다음 특성 중 하나가 있을 수 있습니다.
- **사용되지 않는 권한**: 애플리케이션이 부여된 모든 권한을 활용하는 API 호출을 수행하지 못할 경우 사용되지 않는 권한 상태가 될 수 있습니다. 예를 들어, [MS Graph](/graph/overview)에서 앱은 달력 API와 통합되지 않더라도 OneDrive 파일("Files.Read.All" 권한 사용)을 읽는 것 뿐만 아니라 “*Calendars.Read*” 권한도 부여되었습니다.
- **줄일 수 있는 권한**: 부여된 권한에 원하는 API 호출을 완료할 수 있는 더 낮은 대체 권한이 있는 경우 앱에 줄일 수 있는 권한이 있습니다. 예를 들어, 사용자 프로필을 읽을 뿐만 아니라 "user.ReadWrite.All"을 부여 받은 앱은 과도 권한으로 간주할 수 있습니다. 이 경우, 앱에는 대신 요청을 충족하는 데 필요한 최소 권한에 해당하는 "User.Read.All"이 부여되어야 합니다.

애플리케이션이 최소 권한으로 간주되려면 다음이 있어야 합니다.
- **충분한 권한**: 애플리케이션, 서비스 또는 시스템의 최종 사용자에게 필요한 작업을 수행하는 데 필요한 최소 권한 집합만 부여합니다.

## <a name="approaching-least-privilege-as-an-application-developer"></a>애플리케이션 개발자로서 최소 권한에 근접

개발자는 고객 데이터의 보안에 기여할 책임이 있습니다. 애플리케이션을 개발하는 경우 최소 권한 원칙을 채택해야 합니다. 다음 단계에 따라 애플리케이션에 과도 권한이 부여되지 않도록 하는 것이 좋습니다.
- 애플리케이션에서 수행해야 하는 API 호출에 필요한 권한을 완전히 이해
- [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 앱이 수행해야 하는 각 API 호출에 대한 최소 권한 이해
- 최소 권한부터 최대 권한 범위에서 해당 [권한](/graph/permissions-reference) 찾기
- 앱에서 사용 권한이 겹치는 API 호출을 수행하는 경우 모든 중복 권한 집합 제거
- 권한 목록에서 최소 권한을 선택하여 애플리케이션에 최소 권한 집합만 적용

## <a name="approaching-least-privilege-as-an-organization"></a>조직으로서 최소 권한에 근접

조직에서는 비즈니스 작업에 영향을 줄 수 있기 때문에 기존 애플리케이션을 수정하는 데 주저하는 경우가 종종 있지만, 이미 부여된 권한이 과도한 경우 해지해야 하는 문제가 나타납니다. 조직에서는 사용 권한을 정기적으로 확인하고 검토하는 것이 좋습니다. 애플리케이션의 정상 상태를 유지하려면 다음 단계를 수행하는 것이 좋습니다.
- 애플리케이션에서 수행되는 API 호출 평가
- 필요한 권한 및 최소 권한에 대해서는 [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 및 [Microsoft Graph](/graph/overview) 설명서를 사용합니다.
- 사용자 또는 애플리케이션에 부여되는 권한 감사
- 최소 권한 집합으로 애플리케이션 업데이트
- 권한을 정기적으로 검토하여 모든 권한이 여전히 적절한지 확인

## <a name="next-steps"></a>다음 단계

- Azure Active Directory의 동의 및 권한에 대한 자세한 내용은 [Azure AD 애플리케이션 동의 환경 이해](../develop/application-consent-experience.md)를 참조하세요.
- Microsoft ID의 권한 및 동의에 대한 자세한 내용은 [Microsoft ID 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md)를 참조하세요.
- 제로 트러스트에 대한 자세한 내용은 [제로 트러스트 배포 센터](/security/zero-trust/)를 참조하세요.
