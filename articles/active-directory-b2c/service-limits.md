---
title: Azure AD B2C 서비스 제한 및 제한 사항
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 서비스에 대 한 서비스 제한 및 제한 사항에 대 한 참조입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 866af8b992374492286f47357f108a01f35e560b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051044"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Azure Active Directory B2C 서비스 제한 및 제한 사항

이 문서에는 Azure Active Directory B2C (Azure AD B2C) 서비스에 대 한 사용 제약 조건 및 기타 서비스 제한이 포함 되어 있습니다.

## <a name="end-userconsumption-related-limits"></a>최종 사용자/사용량 관련 제한

다음 최종 사용자 관련 서비스 제한은 SAML, Open ID Connect, OAuth2 및 ROPC를 포함 하 여 Azure AD B2C에서 지 원하는 모든 인증 및 권한 부여 프로토콜에 적용 됩니다.

|범주 |제한    |
|---------|---------|
|Azure AD B2C 테 넌 트 당 IP 요청당 요청 수       |6000/5 분          |
|Azure AD B2C 테 넌 트 당 총 요청 수     |12000/분          |

요청 수는 Azure AD B2C 사용자 경험 중에 발생 하는 디렉터리 읽기 및 쓰기 수에 따라 달라질 수 있습니다. 예를 들어 디렉터리에서 읽는 간단한 로그인은 요청 1 개로 구성 됩니다. 로그인 여행 에서도 디렉터리를 업데이트 해야 하는 경우이 작업은 추가 요청으로 계산 됩니다.

## <a name="azure-ad-b2c-configuration-limits"></a>Azure AD B2C 구성 제한

다음 표에는 Azure AD B2C 서비스의 관리 구성 제한이 나열 되어 있습니다.

|범주  |제한  |
|---------|---------|
|Azure AD B2C 테 넌 트 당 응용 프로그램 수   |250           |
|응용 프로그램당 범위 수        |1000          |
|사용자 당 사용자 [지정 특성](user-profile-attributes.md#extension-attributes)   수 <sup>1</sup>       |100         |
|응용 프로그램당 리디렉션 Url 수       |100         |
|응용 프로그램당 로그 아웃 Url 수        |1          |
|특성 당 문자열 제한      |250 문자          |
|구독 당 B2C 테 넌 트 수      |20         |
|사용자 지정 정책의 [상속](custom-policy-overview.md#inheritance-model) 수준     |10         |
|Azure AD B2C 테 넌 트 당 정책 수      |200          |
|최대 정책 파일 크기      |400 KB          |

<sup>1</sup> [Azure AD 서비스 제한 및 제한 사항](../active-directory/enterprise-users/directory-service-limits-restrictions.md)도 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft Graph의 제한 지침](/graph/throttling.md) 에 대해 알아보기 
- [Azure AD B2C 응용 프로그램의 유효성 검사 차이점](../active-directory/develop/supported-accounts-validation.md) 에 대해 알아봅니다.













