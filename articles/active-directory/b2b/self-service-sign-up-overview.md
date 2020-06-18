---
title: 외부 ID에 대한 셀프 서비스 등록 - Azure AD
description: 외부 사용자가 셀프 서비스 등록을 활성화하여 직접 사용자 애플리케이션에 등록할 수 있도록 허용하는 방법을 알아봅니다. 셀프 서비스 등록 사용자 흐름을 사용자 지정하여 맞춤형 등록 환경을 만듭니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98456f26fbc7ca3955883eb283b54084bd86d503
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83737761"
---
# <a name="self-service-sign-up-preview"></a>셀프 서비스 등록(미리 보기)
|     |
| --- |
| 셀프 서비스 등록은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

외부 사용자와 애플리케이션을 공유하는 경우 애플리케이션에 액세스해야 하는 사용자를 항상 미리 알 수 있는 것은 아닙니다. 개인에게 직접 초대를 보내는 대신, 외부 사용자가 셀프 서비스 등록을 사용하도록 설정하여 특정 애플리케이션에 직접 등록하도록 허용할 수 있습니다. 셀프 서비스 등록 사용자 흐름을 사용자 지정하여 맞춤형 등록 환경을 만들 수 있습니다. 예를 들어 Azure AD 또는 소셜 ID 공급자에 등록하고 등록 프로세스 중에 사용자에 대한 정보를 수집하는 옵션을 제공할 수 있습니다.

> [!NOTE]
> 사용자 흐름을 조직에서 빌드한 앱과 연결할 수 있습니다. 사용자 흐름은 SharePoint 또는 팀과 같은 Microsoft 앱에 사용할 수 없습니다.

## <a name="user-flow-for-self-service-sign-up"></a>셀프 서비스 등록을 위한 사용자 흐름

셀프 서비스 등록 사용자 흐름은 공유하려는 애플리케이션을 통해 외부 사용자에 대한 등록 환경을 만듭니다. 사용자 흐름은 하나 이상의 애플리케이션에 연결할 수 있습니다. 먼저 테넌트에 셀프 서비스 등록을 사용하도록 설정하고 외부 사용자가 로그인에 사용할 수 있도록 허용하려는 ID 공급자와 페더레이션합니다. 그런 다음, 등록 사용자 흐름을 만들고 사용자 지정한 후 애플리케이션을 할당합니다.
사용자 흐름 설정을 구성하여 사용자가 애플리케이션에 등록하는 방법을 제어할 수 있습니다.

- Facebook 등의 소셜 계정 또는 Azure AD 계정과 같은 로그인에 사용되는 계정 유형
- 등록하는 사용자로부터 수집할 특성(예: 이름, 우편 번호 또는 거주 국가/지역)

웹, 모바일, 데스크톱 또는 SPA(단일 페이지 애플리케이션)인지 여부에 관계없이 사용자가 애플리케이션에 로그인하려는 경우 애플리케이션에서 사용자 흐름 제공 엔드포인트에 대한 권한 부여 요청을 시작합니다. 사용자 흐름은 사용자의 환경을 정의하고 제어합니다. 사용자가 등록 사용자 흐름을 완료하면 Azure AD에서 토큰을 생성하고 사용자를 애플리케이션으로 다시 리디렉션합니다. 등록이 완료되면 디렉터리에서 사용자에 대한 게스트 계정이 프로비저닝됩니다. 여러 애플리케이션이 동일한 정책을 사용할 수 있습니다.

## <a name="example-of-self-service-sign-up"></a>셀프 서비스 등록의 예제

다음 예제에서는 게스트 사용자를 위한 셀프 서비스 등록 기능을 통해 소셜 ID 공급자를 Azure AD로 가져오는 방법을 보여줍니다.  
Woodgrove의 파트너는 Woodgrove 앱을 엽니다. 공급자 계정에 등록하기로 결정하면 공급자 계정 요청을 선택하여 셀프 서비스 등록 흐름을 시작합니다.

![셀프 서비스 등록 시작 페이지의 예제](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

선택한 이메일을 사용하여 등록합니다.

![로그인에 Facebook을 선택한 예제](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD는 파트너의 Facebook 계정을 사용하여 Woodgrove와의 관계를 만들고 등록 후 사용자를 위해 새 게스트 계정을 만듭니다.

Woodgrove에서 이름, 비즈니스 이름, 비즈니스 등록 코드, 전화 번호 등 사용자에 대한 정보를 더 알고자 합니다.

![사용자 등록 특성을 보여 주는 예제](media/self-service-sign-up-overview/example-enter-user-attributes.png)

사용자가 정보를 입력하면 등록 흐름이 계속 진행되어 필요한 리소스에 대한 액세스 권한을 가져옵니다.

![로그인한 사용자를 보여 주는 예제](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>다음 단계

 자세한 내용은 [앱에 셀프 서비스 등록을 추가](self-service-sign-up-user-flow.md)하는 방법을 참조하세요.