---
title: 응용 프로그램 모델 | Microsoft
titleSuffix: Microsoft identity platform
description: App-v (Microsoft identity platform)와 통합할 수 있도록 응용 프로그램을 등록 하는 프로세스에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5aca96a9c3bc4e8f1061f677e316565b10014ac9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88117484"
---
# <a name="application-model"></a>애플리케이션 모델

응용 프로그램은 사용자 자체에 로그인 하거나 id 공급자에 로그인을 위임할 수 있습니다. 이 항목에서는 Microsoft id 플랫폼에 응용 프로그램을 등록 하는 데 필요한 단계에 대해 설명 합니다.

## <a name="registering-an-application"></a>애플리케이션 등록

Id 공급자가 사용자에 게 특정 앱에 대 한 액세스 권한이 있음을 알고 있는 경우 사용자와 응용 프로그램을 모두 id 공급자에 등록 해야 합니다. Azure AD에 응용 프로그램을 등록 하면 응용 프로그램에 대 한 id 구성을 제공 하 여 Microsoft id 플랫폼과 통합할 수 있습니다. 앱을 등록 하면 다음 작업도 수행할 수 있습니다.

* 로그인 대화 상자에서 응용 프로그램의 브랜딩을 사용자 지정 합니다. 이는 사용자가 앱과 함께 사용 하는 첫 번째 환경 이기 때문에 중요 합니다.
* 사용자가 조직에 속한 경우에만 로그인 할 수 있도록 할지 결정 합니다. 이 응용 프로그램은 단일 테 넌 트 응용 프로그램입니다. 또는 사용자가 회사 또는 학교 계정을 사용 하 여 로그인 하도록 허용 합니다. 다중 테 넌 트 응용 프로그램입니다. 개인 Microsoft 계정이 나 LinkedIn, Google 등의 소셜 계정도 허용할 수 있습니다.
* 요청 범위 권한입니다. 예를 들어, 로그인 한 사용자의 프로필을 읽을 수 있는 권한을 부여 하는 "사용자 읽기" 범위를 요청할 수 있습니다.
* 웹 API에 대 한 액세스를 정의 하는 범위를 정의 합니다. 일반적으로 앱이 API에 액세스 하려는 경우 사용자가 정의 하는 범위에 대 한 권한을 요청 해야 합니다.
* 앱의 id를 증명 하는 Microsoft id 플랫폼과 암호를 공유 합니다.  이는 앱이 기밀 클라이언트 응용 프로그램 인 경우와 관련이 있습니다. 기밀 클라이언트 응용 프로그램은 자격 증명을 안전 하 게 저장할 수 있는 응용 프로그램입니다. 자격 증명을 저장 하려면 신뢰할 수 있는 백엔드 서버가 필요 합니다.

등록 된 응용 프로그램에는 토큰을 요청할 때 앱이 Microsoft id 플랫폼과 공유 하는 고유 식별자가 제공 됩니다. 앱이 [기밀 클라이언트 응용 프로그램](developer-glossary.md#client-application)인 경우 인증서 또는 암호의 사용 여부에 따라 비밀 또는 공개 키도 공유 합니다.

Microsoft id 플랫폼은 다음 두 가지 주요 함수를 사용 하는 모델을 사용 하는 응용 프로그램을 나타냅니다.

* 지 원하는 인증 프로토콜을 통해 앱 식별
* 인증에 필요한 모든 식별자, Url, 암호 및 관련 정보를 제공 합니다.

Microsoft id 플랫폼:

* 런타임에 인증을 지 원하는 데 필요한 모든 데이터를 저장 합니다.
* 앱이 액세스 하는 데 필요한 리소스를 결정 하 고 지정 된 요청을 수행 해야 하는 상황에 따라 모든 데이터를 저장 합니다.
* 앱 개발자 테 넌 트 및 다른 Azure AD 테 넌 트 내에서 앱 프로 비전을 구현 하기 위한 인프라를 제공 합니다.
* 토큰 요청 시 사용자 동의를 처리 하 고 테 넌 트 간에 앱을 동적으로 프로 비전 합니다.

**동의** 는 리소스 소유자를 대신 하 여 클라이언트 응용 프로그램이 특정 권한으로 보호 되는 리소스에 액세스할 수 있는 권한 부여를 부여 하는 프로세스입니다. Microsoft id 플랫폼:

* 앱이 대신 리소스에 액세스하는 데 관해 사용자 및 관리자가 동적으로 권한을 부여하거나 동의를 거부할 수 있도록 설정합니다.
* 관리자가 궁극적으로 앱이 수행할 수 있는 작업, 특정 앱을 사용할 수 있는 사용자 및 디렉터리 리소스에 액세스하는 방식을 결정하도록 설정합니다.

## <a name="multi-tenant-apps"></a>다중 테넌트 앱

Microsoft id 플랫폼에서 응용 프로그램 [개체](developer-glossary.md#application-object) 는 응용 프로그램을 설명 합니다. 배포 시 Microsoft id 플랫폼은 응용 프로그램 개체를 청사진으로 사용 하 여 디렉터리 또는 테 넌 트 내에서 응용 프로그램의 구체적인 인스턴스를 나타내는 [서비스 주체](developer-glossary.md#service-principal-object)를 만듭니다. 서비스 주체는 앱이 특정 대상 디렉터리에서 실제로 수행할 수 있는 작업, 사용할 수 있는 리소스, 액세스 권한이 있는 리소스 등을 정의 합니다. Microsoft ID 플랫폼은 [동의](developer-glossary.md#consent)를 통해 애플리케이션 개체에서 서비스 주체를 만듭니다.

다음 다이어그램에는 동의를 통해 구동되는 간소화된 Microsoft ID 플랫폼 프로비저닝 흐름이 나와 있습니다. *A* 와 *B*라는 두 개의 테 넌 트가 표시 됩니다.

* *테 넌 트 A* 는 응용 프로그램을 소유 합니다.
* *테 넌 트 B* 는 서비스 주체를 통해 응용 프로그램을 인스턴스화합니다.

![동의를 통해 구동되는 간소화된 프로비전 흐름](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

이 프로비전 흐름에서

1. 테넌트 B의 사용자가 앱에 로그인하려고 시도하면 권한 부여 엔드포인트에서 애플리케이션의 토큰을 요청합니다.
1. 사용자 자격 증명을 획득 하 여 인증을 확인 합니다.
1. 사용자에 게 테 넌 트 B에 대 한 액세스 권한을 얻기 위해 앱에 대 한 동의를 제공 하 라는 메시지가 표시 됩니다.
1. Microsoft id 플랫폼은 테 넌 트 B에서 서비스 주체를 만들기 위한 청사진으로 테 넌 트 A의 응용 프로그램 개체를 사용 합니다.
1. 사용자가 요청 된 토큰을 받습니다.

추가 테 넌 트에 대해이 프로세스를 반복할 수 있습니다. 테넌트 A에서 앱(애플리케이션 개체)에 대한 청사진을 유지합니다. 앱이 승인 된 다른 모든 테 넌 트의 사용자 및 관리자는 응용 프로그램이 각 테 넌 트의 해당 서비스 주체 개체를 통해 수행할 수 있는 작업을 계속 제어 합니다. 자세한 내용은 [Microsoft ID 플랫폼의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

인증 및 권한 부여 기본 사항을 설명하는 기타 항목:

* Microsoft ID 플랫폼의 인증 및 권한 부여의 기본 사항에 대해서는 [인증과 권한 부여의 차이점](authentication-vs-authorization.md)을 참조하세요.
* 인증 및 권한 부여에 액세스 토큰, 새로 고침 토큰 및 ID 토큰을 사용하는 방법에 대한 자세한 내용은 [보안 토큰](security-tokens.md)을 참조하세요.
* Microsoft ID 플랫폼에서 웹, 데스크톱 및 모바일 앱의 로그인 흐름에 대해 알아보려면 [앱 로그인 흐름](app-sign-in-flow.md)을 참조하세요.

응용 프로그램 모델에 대 한 자세한 내용은 다음과 같습니다.

* Microsoft id 플랫폼의 응용 프로그램 개체 및 서비스 사용자에 대 한 자세한 내용은 [AZURE AD에 응용 프로그램을 추가 하는 방법과 이유](active-directory-how-applications-are-added.md) 를 참조 하세요.
* 단일 테 넌 트 앱 및 다중 테 넌 트 앱에 대 한 자세한 내용은 [Azure Active Directory의 테 넌](single-and-multi-tenant-apps.md) 트를 참조 하세요.
* 조직에서 사용자에 게 로그인 할 수 있도록 Azure AD에서 Azure Active Directory B2C 제공 하는 방법에 대 한 자세한 내용은 [Azure Active Directory B2C 설명서](../../active-directory-b2c/index.yml) 를 참조 하세요.