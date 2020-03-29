---
title: 인증 방법 사용 & 인사이트 - Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정 및 다단계 인증 인증 방법 사용에 대한 보고
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b448e6ce7c8b4522d5e7bdbafb39eccca982fdee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848513"
---
# <a name="authentication-methods-usage--insights-preview"></a>인증 방법 사용 & 인사이트(미리 보기)

사용 & 인사이트를 사용하면 Azure 다단계 인증 및 셀프 서비스 암호 재설정과 같은 기능에 대한 인증 방법이 조직에서 어떻게 작동하는지 이해할 수 있습니다. 이 보고 기능을 통해 조직은 등록중인 방법과 사용 방법을 이해할 수 있습니다.

## <a name="permissions-and-licenses"></a>권한 및 라이센스

다음 역할은 사용 및 통찰력에 액세스할 수 있습니다.

- 전역 관리자
- 보안 판독기
- 보안 관리자
- 보고서 구독자

사용 및 통찰력에 액세스하기 위해 추가 라이선스가 필요하지 않습니다. Azure 다단계 인증 및 셀프 서비스 암호 재설정(SSPR) 라이선스 정보는 [Azure Active Directory 가격 책정 사이트에서](https://azure.microsoft.com/pricing/details/active-directory/)찾을 수 있습니다.

## <a name="how-it-works"></a>작동 방법

인증 방법 사용 및 통찰력에 액세스하려면 다음을 수행하십시오.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. Azure **Active Directory** > **암호 재설정** > **사용 & 인사이트로**이동합니다.
1. **등록** 또는 **사용** 개요에서 미리 필터링된 보고서를 열어 필요에 따라 필터링하도록 선택할 수 있습니다.

![사용 & 인사이트 개요](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

사용 & 인사이트에 직접 [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)액세스하려면 로 이동하십시오. 이 링크를 통해 등록 개요를 확인할 수 있습니다.

등록된 사용자, 사용자 사용 가능 및 사용자 사용 가능한 타일에는 사용자에 대한 다음 등록 데이터가 표시됩니다.

- 등록: 사용자가 조직의 SSPR 또는 다단계 인증 정책을 충족하기에 충분한 인증 방법을 등록한 경우 등록된 것으로 간주됩니다.
- 사용 가능: 사용자가 SSPR 정책의 범위에 있는 경우 사용하도록 설정된 것으로 간주됩니다. 그룹에 대해 SSPR을 사용하도록 설정하면 해당 그룹에 있는 경우 사용자가 사용하도록 설정된 것으로 간주됩니다. 모든 사용자에 대해 SSPR을 사용하도록 설정하면 테넌트의 모든 사용자(게스트 제외)가 활성화된 것으로 간주됩니다.
- 가능: 사용자가 모두 등록되어 있고 활성화된 경우 사용할 수 있는 것으로 간주됩니다. 이 상태는 필요한 경우 언제든지 SSPR을 수행할 수 있음을 의미합니다.

이러한 타일 또는 타일에 표시된 인사이트를 클릭하면 미리 필터링된 등록 세부 정보 목록으로 이동합니다.

**등록** 탭의 **등록** 차트에는 인증 방법으로 성공한 인증 방법 등록 수가 표시됩니다. **사용** 탭의 **재설정** 차트에는 인증 방법으로 암호 재설정 흐름 중에 성공한 인증 및 실패한 인증 수가 표시됩니다.

차트 중 하나를 클릭하면 미리 필터링된 등록 목록 또는 이벤트 재설정 으로 이동합니다.

오른쪽 상단 모서리의 컨트롤을 사용하여 등록 및 재설정 차트에 표시된 감사 데이터의 날짜 범위를 24시간, 7일 또는 30일로 변경할 수 있습니다.

### <a name="registration-details"></a>등록 세부 정보

**등록된 사용자**, **사용자가 활성화한**사용자 또는 **사용자 사용** 타일 또는 인사이트를 클릭하면 등록 세부 정보로 이동합니다.

등록 세부 정보 보고서에는 각 사용자에 대한 다음 정보가 표시됩니다.

- 이름
- 사용자 이름
- 등록 상태(모두, 등록됨, 등록되지 않음)
- 사용 상태(모두, 활성화 됨, 활성화 되지 않음)
- 가능한 상태(모두, 가능, 불가능)
- 방법 (앱 알림, 앱 코드, 전화 통화, SMS, 이메일, 보안 질문)

목록 맨 위에 있는 컨트롤을 사용하여 사용자를 검색하고 표시된 열을 기반으로 사용자 목록을 필터링할 수 있습니다.

### <a name="reset-details"></a>세부 정보 재설정

등록 또는 재설정 차트를 클릭하면 재설정 세부 정보로 이동합니다.

리셋 세부 정보 보고서에는 다음을 포함하여 지난 30일 간의 등록 및 재설정 이벤트가 표시됩니다.

- 이름
- 사용자 이름
- 기능(모두, 등록, 재설정)
- 인증 방법 (앱 알림, 앱 코드, 전화 통화, 사무실 통화, SMS, 이메일, 보안 질문)
- 상태(모두, 성공, 실패)

목록 맨 위에 있는 컨트롤을 사용하여 사용자를 검색하고 표시된 열을 기반으로 사용자 목록을 필터링할 수 있습니다.

## <a name="limitations"></a>제한 사항

이러한 보고서에 표시된 데이터는 최대 60분까지 지연됩니다. Azure 포털에 "마지막으로 새로 고쳐진" 필드가 존재하여 데이터의 최근 을 식별합니다.

사용 및 인사이트 데이터는 Azure AD 로그인 보고서에 포함된 Azure 다단계 인증 활동 보고서 또는 정보를 대체하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [인증 방법 사용 보고서 API 작업](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [조직에 대한 인증 방법 선택](concept-authentication-methods.md)
- [통합 등록 경험](concept-registration-mfa-sspr-combined.md)
