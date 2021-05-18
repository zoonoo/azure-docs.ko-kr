---
title: HYPR을 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 진정한 암호 없는 강력한 고객 인증을 위해 Hypr를 사용하여 Azure Active Directory B2C를 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 357c8d79259986e211dd04580b1d8205c25824da
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257979"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 HYPR을 구성하기 위한 자습서

이 샘플 자습서에서는 [HYPR](https://get.hypr.com)을 사용하여 Azure AD B2C를 구성하는 방법에 대한 참고 자료를 제공합니다. Azure AD B2C를 ID 공급자로 사용하여 HYPR과 고객 애플리케이션을 통합하여 사용자에게 진정한 암호 없는 인증을 제공할 수 있습니다. HYPR은 암호를 공개 키 암호화로 바꿔서, 사기 행위, 피싱 및 자격 증명 재사용을 제거합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md). 테넌트는 Azure 구독과 연결됩니다.

- HYPR 클라우드 테넌트, 체험 [평가판 계정](https://get.hypr.com/free-trial) 받기

- HYPR Rest Api 또는 HYPR 디바이스 관리자를 사용하여 등록한 사용자의 모바일 디바이스가 HYPR 테넌트에 있습니다. 예를 들어, [HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk)를 사용하여 이 작업을 수행할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

HYPR 통합에는 다음 구성 요소가 포함됩니다.

- Azure AD B2C - 사용자의 자격 증명(ID 공급자라고도 함)을 확인하는 권한 부여 서버입니다.

- 웹 및 모바일 애플리케이션 - HYPR과 Azure AD B2C를 사용하여 보호하도록 선택한 모바일 또는 웹 애플리케이션입니다. HYPR은 iOS 및 Android 플랫폼에서 진정한 암호 없는 인증을 수행하는 데 사용할 수 있는 강력한 모바일 SDK도 제공합니다.

- HYPR 모바일 앱 - 사용자의 모바일 애플리케이션에서 모바일 SDK를 사용하지 않으려는 경우, HYPR 모바일 앱을 사용하여 이 샘플을 실행할 수 있습니다.

- HYPR Rest API - HYPR API를 사용하여 사용자 디바이스 등록과 인증을 모두 수행할 수 있습니다. 이러한 API는 [여기](https://apidocs.hypr.com)에서 찾을 수 있습니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![hypr-아키텍처-다이어그램에 대한 스크린샷](media/partner-hypr/hypr-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지를 엽니다. 사용자가 로그인/등록을 선택하고, 페이지에 사용자 이름을 입력합니다.
| 2. | 애플리케이션은 ID 확인을 위해 Azure AD B2C에 사용자 특성을 보냅니다.
| 3. | Azure AD B2C는 사용자 특성을 수집하고 HYPR에 대한 특성을 보내서 HYPR 모바일 앱으로 사용자를 인증합니다.
| 4. | HYPR은 FIDO (Fast Identity Online) 승인 인증을 위해 등록된 사용자 모바일 디바이스에 푸시 알림을 보냅니다. 여기에는 사용자 지문 인식, 생체 인식 또는 분산형 pin이 포함될 수 있습니다.  
| 5. | 사용자가 푸시 알림을 확인한 후에 확인 결과에 따라 고객 애플리케이션에 대한 액세스 권한이 부여되거나 거부됩니다.

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C 정책 구성

1. 정책 폴더의 [Azure AD B2C HYPR 정책](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy)으로 이동합니다.

2. 이 [문서](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)에 따라 [LocalAccounts 스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 다운로드합니다.

3. Azure AD B2C 테넌트에 대한 정책을 구성합니다.

>[!NOTE]
>특정 테넌트와 관련하여 제공된 정책을 업데이트합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 정책에서 **Identity Experience Framework** 를 선택합니다.

2. 이전에 만든 **SignUpSignIn** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   a. **애플리케이션**: 등록된 앱을 선택합니다(JWT는 샘플).

   b. **회신 URL**: **리디렉션 URL** 을 선택합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 따라 계정 만들기

5. 사용자 특성을 만든 후 흐름 중에 HYPR이 호출됩니다. 흐름이 불완전한 경우 사용자가 디렉터리에 저장되지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)