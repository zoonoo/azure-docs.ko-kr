---
title: 인증 방법 사용 및 통찰력 보고 (미리 보기)-Azure Active Directory
description: Reporting에서 Azure AD 셀프 서비스 암호 재설정 및 Multi-factor Authentication 인증 메서드 사용
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561036"
---
# <a name="authentication-methods-usage--insights-preview"></a>인증 방법 사용 및 insights (미리 보기)

사용량 및 insights 조직에서 Azure Multi-factor Authentication 등 셀프 서비스 암호 재설정 기능에 대 한 인증 방법을 작업 하는 방법을 이해할 수 있습니다. 이 보고 기능은 사용자 조직에 어떤 메서드를 등록 하는 및 사용 되는 방법을 이해 하는 방법 제공 합니다.

## <a name="permissions-and-licenses"></a>사용 권한 및 라이선스

다음 역할 사용 현황 및 정보에 액세스할 수 있습니다.

- 전역 관리자
- 보안 판독기
- 보안 관리자
- 보고서 구독자

액세스 사용 현황 및 insights 하는 데 필요한 경우 추가 라이선스 없음 Azure Multi-factor Authentication 및 셀프 서비스 암호 재설정 (SSPR)에 대 한 라이선스 정보에서 확인할 수 있습니다 합니다 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)합니다.

## <a name="how-it-works"></a>작동 방법

에 액세스 하려면 인증 방법 사용 하 고 insights:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 이동할 **Azure Active Directory** > **암호 재설정** > **사용량 및 insights**합니다.
1. **등록** 또는 **사용량** 개요, 미리 필터링 된 열을 선택할 수 있습니다 필요에 따라 보고서를 필터링 합니다.

![사용량 및 insights 개요](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

사용량 및 insights 직접 액세스로 이동 [ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)합니다. 이 링크는 다음 등록 개요로 표시 됩니다.

사용자가 등록 하 고 사용할 수 있는 사용자를 사용자에 대 한 다음 등록 데이터를 표시 하는 사용자 수 있는 타일:

- 등록: 사용자는 (또는 관리자)에 조직의 Multi-factor Authentication 또는 SSPR 정책에 맞게 충분 한 인증 방법을 등록 하는 경우 등록 간주 됩니다.
- 사용: 사용자는 SSPR 정책에 대 한 범위에 있을 경우 활성화 간주 됩니다. 그룹에 대 한 SSPR 사용 하는 경우 해당 그룹을 사용 하도록 설정 사용자 간주 됩니다. SSPR 사용 하는 경우 모든 사용자에 대해 (게스트 제외) 하는 테 넌 트의 모든 사용자가 사용 하도록 설정 것으로 간주 됩니다.
- 지원: 사용자는 모두 등록 및 사용 하도록 설정 하는 경우 지원 간주 됩니다. 이 상태는 수행할 수 있다고 SSPR 언제 든 지 필요한 경우를 의미 합니다.

이러한 타일에 표시 된 insights의 하나를 클릭 하면 미리 필터링 된 목록을 등록 세부 정보를 이동 됩니다.

합니다 **등록** 에서 차트의 **등록** 탭 수를 보여 줍니다 성공 및 실패 한 인증 방법을 등록 인증 방법으로 합니다. **다시 설정** 에서 차트를 **사용량** 성공한 수를 표시 하는 탭 및 인증 방법으로 다시 설정 흐름의 비밀 중 실패 한 인증 합니다.

차트 중 하나를 클릭할 등록 미리 필터링 된 목록을 이동 되거나 이벤트를 다시 설정 됩니다.

컨트롤을 사용 하 여 위, 오른쪽 모서리에 있는, 24 시간, 7 일 또는 30 일에 등록 및 재설정 차트에 표시 되는 감사 데이터에 대 한 날짜 범위를 변경할 수 있습니다.

등록 데이터를 

### <a name="registration-details"></a>등록 세부 정보

클릭 하는 **등록 된 사용자**, **사용할 수 있는 사용자**, 또는 **가능 사용자** 타일 또는 insights 이동 됩니다 등록 세부 정보.

등록 세부 정보 보고서는 각 사용자에 대해 다음 정보를 보여 줍니다.

- 이름
- 사용자 이름
- 등록 상태 (모든, 등록, 등록 되지 않은)
- 상태를 사용 하도록 설정 (모두를 사용 하도록 설정, 해제)
- 지원 상태 (모든, 지원, Not 가능)
- 방법 (앱 알림, 앱 코드, 전화 통화, SMS, 전자 메일, 보안 질문)

컨트롤을 사용 하 여 목록의 맨 위에 있는, 사용자를 검색할 수 있으며 표시 된 열을 기반으로 하는 사용자 목록을 필터링 수도 있습니다.

### <a name="reset-details"></a>세부 정보를 다시 설정

등록 또는 재설정 차트를 클릭 하면 재설정 세부 정보를 이동 됩니다.

다시 설정 세부 정보 보고서는 포함 하 여 지난 30 일의 등록 및 재설정 이벤트를 보여 줍니다.

- Name
- 사용자 이름
- (모두, 등록, 재설정) 기능
- 인증 방법 (앱 알림, 앱 코드, 전화, 사무실 통화, SMS, 전자 메일, 보안 질문)
- (모든, 성공, 실패) 상태

컨트롤을 사용 하 여 목록의 맨 위에 있는, 사용자를 검색할 수 있으며 표시 된 열을 기반으로 하는 사용자 목록을 필터링 수도 있습니다.

## <a name="limitations"></a>제한 사항

이러한 보고서에 표시 된 데이터는 최대 60 분으로 지연 됩니다. 어떻게 최근 데이터를 식별 하는 Azure 포털에서 "마지막 새로 고침" 필드가 있습니다.

사용량 및 insights 데이터를 Azure AD 로그인 보고서에 포함 된 정보를 Azure Multi-factor Authentication 활동 보고서에 대 한 대체 아닙니다.

## <a name="next-steps"></a>다음 단계

- [인증 방법 사용 현황 보고서 API 사용](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [조직에 대 한 인증 방법 선택](concept-authentication-methods.md)
- [등록 환경을 결합](concept-registration-mfa-sspr-combined.md)
