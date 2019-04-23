---
title: Azure AD 애플리케이션 동의 환경 이해 | Microsoft Docs
description: Azure AD에서 애플리케이션을 관리 및 개발할 때 Azure AD 동의 환경을 사용하는 방법을 확인하기 위해 해당 환경에 대해 알아보기
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: celested
ms.reviewer: zachowd
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7de892143e1c69953cc60429ea0d24df194f0df
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795188"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Azure AD 애플리케이션 동의 환경 이해

Azure AD(Azure Active Directory) 애플리케이션 동의 사용자 환경에 대해 알아봅니다. 그러면 조직에 대한 애플리케이션을 지능적으로 관리하고/또는 더 원활한 동의 환경이 포함된 애플리케이션을 개발할 수 있습니다.

## <a name="consent-and-permissions"></a>동의 및 사용 권한

동의란 사용자가 자신을 대신해 보호되는 리소스에 액세스하기 위해 애플리케이션에 권한을 부여하는 프로세스를 말합니다. 관리자 또는 사용자에게 조직/개인 데이터에 대한 액세스를 허용하도록 동의를 요청할 수 있습니다.

동의를 부여하는 실제 사용자 환경은 사용자의 테넌트, 사용자의 권한(또는 역할) 범위 및 클라이언트 애플리케이션에서 요청한 [사용 권한](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) 형식에 따라 달라집니다. 즉, 애플리케이션 개발자와 테넌트 관리자는 동의 환경을 어느 정도 제어할 수 있습니다. 관리자는 테넌트에서 동의 환경을 제어하기 위해 테넌트 또는 앱에서 정책을 유연하게 사용 및 사용하지 않도록 설정할 수 있습니다. 애플리케이션 개발자는 어떤 유형의 사용 권한을 요청하는지 및 사용자 동의 흐름 또는 관리자 동의 흐름을 통해 사용자를 안내하려는지 여부를 결정할 수 있습니다.

- **사용자 동의 흐름** - 애플리케이션 개발자가 현재 사용자만의 동의를 기록하려는 목적으로 권한 부여 엔드포인트로 사용자를 안내하는 경우입니다.
- **관리자 동의 흐름** - 애플리케이션 개발자가 전체 테넌트의 동의를 기록하려는 목적으로 관리자 동의 엔드포인트로 사용자를 안내하는 경우입니다. 관리자 동의 흐름이 제대로 작동되도록 하려면 애플리케이션 개발자는 애플리케이션 매니페스트의 `RequiredResourceAccess` 속성에서 모든 사용 권한을 나열해야 합니다. 자세한 내용은 [애플리케이션 매니페스트](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)를 참조하세요.

## <a name="building-blocks-of-the-consent-prompt"></a>동의 확인 프롬프트의 구성 요소

동의 확인 프롬프트는 사용자가 자신을 대신해 클라이언트 애플리케이션이 보호되는 리소스에 액세스할 정도로 신뢰하는지 판단할 충분한 정보를 확보하도록 설계됩니다. 구성 요소에 대한 이해는 사용자 동의가 더 많은 정보를 확보하고 결정을 내리는 데 도움이 되며, 또한 개발자가 더 나은 사용자 환경을 빌드하는 데도 도움이 됩니다.

다음 다이어그램 및 테이블은 동의 확인 프롬프트의 구성 요소에 대한 정보를 제공합니다.

![동의 확인 프롬프트의 구성 요소](./media/application-consent-experience/consent_prompt.png)

| # | 구성 요소 | 목적 |
| ----- | ----- | ----- |
| 1 | 사용자 ID | 이 ID는 클라이언트 애플리케이션이 대신해 보호되는 리소스에 액세스를 요청하는 사용자를 나타냅니다. |
| 2 | 제목 | 제목은 사용자가 사용자 또는 관리자 동의 흐름의 통과 여부에 따라 변경됩니다. 사용자 동의 흐름에서 제목은 "사용 권한 요청"이지만 관리자 동의 흐름의 제목은 "조직에 대한 동의" 줄이 추가됩니다. |
| 3 | 앱 로고 | 이 이미지는 이 앱이 액세스하려던 앱인지 여부를 나타내는 시각적 신호를 사용자가 확보하는 데 도움이 됩니다. 이 이미지는 애플리케이션 개발자가 제공하며 이 이미지의 소유권에 대한 유효성은 검사되지 않습니다. |
| 4 | 앱 이름 | 이 값은 애플리케이션이 사용자의 데이터에 액세스를 요청하는 것을 사용자에게 알려야 합니다. 이 이름은 개발자가 제공하며 이 앱 이름의 소유권에 대한 유효성은 검사되지 않습니다. |
| 5 | 게시자 도메인 | 이 값은 신뢰성을 평가할 수 있는 도메인을 사용자에게 제공해야 합니다. 이 도메인은 개발자가 제공하며 이 게시자 도메인의 소유권에 대한 유효성이 검사됩니다. |
| 6 | 권한 | 이 목록에는 클라이언트 애플리케이션에서 요청하는 사용 권한이 포함됩니다. 사용자는 동의하는 경우 자신을 대신해 액세스 권한이 클라이언트 애플리케이션에 부여되는 데이터를 파악하려면 요청되는 권한 유형을 항상 평가해야 합니다. 애플리케이션 개발자로서 최소 권한이 포함된 사용 권한에 대한 액세스를 요청하는 것이 가장 좋습니다. |
| 7 | 사용 권한 설명 | 이 값은 사용 권한을 노출하는 서비스에서 제공됩니다. 사용 권한 설명을 참조하려면 사용 권한 옆의 펼침 단추를 설정 및 해제해야 합니다. |
| 8 | 앱 사용 약관 | 이러한 사용 약관에는 서비스 및 애플리케이션의 개인정보처리방침 약관에 대한 링크가 포함됩니다. 게시자는 서비스 약관에 해당 규칙의 개요를 표시하는 일을 담당합니다. 또한 게시자는 해당 개인정보처리방침에서 사용자 데이터를 사용하고 공유하는 방법을 공개하는 일도 담당합니다. 게시자가 다중 테넌트 애플리케이션을 위해 이러한 값에 대한 링크를 제공하지 않는 경우 동의 확인 프롬프트에 굵게 표시된 경고가 표시됩니다. |
| 9 | https://myapps.microsoft.com | 사용자가 검토해서 현재 해당 데이터에 액세스 권한이 있는 모든 타사 애플리케이션을 제거할 수 있는 링크입니다. |

## <a name="common-consent-scenarios"></a>일반적인 동의 시나리오

사용자가 일반적인 동의 시나리오에서 볼 수 있는 동의 환경은 다음과 같습니다.

1. 권한 범위 내에 있는 사용 권한 집합을 요구하면서 사용자 동의 흐름으로 안내하는 앱에 액세스하는 개인 사용자.
    
    1. 관리자는 전체 테넌트를 대신하여 동의할 수 있는 기존의 동의 확인 프롬프트에서 추가 컨트롤을 확인할 수 있습니다. 컨트롤은 기본적으로 꺼짐으로 설정되어 있으므로 관리자가 명시적으로 확인란을 체크할 경우에만 전체 테넌트를 대신해 동의가 부여됩니다. 현재로서는 이 확인란에 글로벌 관리자 역할만 표시되므로 클라우드 관리자 및 앱 관리자는 이 확인란에 표시되지 않습니다.

        ![시나리오 1a에 대한 동의 확인 프롬프트](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. 사용자에게는 기존의 동의 확인 프롬프트가 표시됩니다.

        ![시나리오 1b에 대한 동의 확인 프롬프트](./media/application-consent-experience/consent_prompt_1b.png)

2. 권한 범위 밖에 있는 하나 이상의 사용 권한이 필요한 앱에 액세스하는 개인 사용자.
    1. 관리자에게는 위에 표시된 1.i과 동일한 프롬프트가 표시됩니다.
    2. 사용자는 애플리케이션에 대한 동의를 부여하는 것이 차단되므로 앱에 액세스하려면 관리자에게 요청하라는 메시지가 나타납니다. 
                
        ![시나리오 1b에 대한 동의 확인 프롬프트](./media/application-consent-experience/consent_prompt_2b.png)

3. 관리지 동의 흐름으로 이동하거나 안내되는 개인 사용자.
    1. 관리 사용자에게는 관리자 동의 확인 프롬프트가 표시됩니다. 이 프롬프트에서 제목 및 사용 권한 설명이 변경됐습니다. 이 변경 사항은 이 프롬프트에 대한 동의를 통해 전체 테넌트를 대신해 요청된 데이터에 대한 액세스 권한이 앱에 부여된다는 사실을 강조 표시합니다.
        
        ![시나리오 1b에 대한 동의 확인 프롬프트](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. 비관리 사용자에게는 위에 표시된 2.ii와 동일한 화면이 표시됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure AD 동의 프레임워크가 동의를 구현하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)에 대한 단계별 개요 확인
- 좀 더 깊이 있는 이해를 위해 [다중 테넌트 애플리케이션이 동의 프레임워크를 사용하여](active-directory-devhowto-multi-tenant-overview.md) "user" 및 "admin" 동의를 구현하고 좀 더 수준 높은 다중 계층 애플리케이션 패턴을 지원하는 방법 알아보기
- 에 대해 알아봅니다 [앱의 게시자 도메인을 구성 하는 방법](howto-configure-publisher-domain.md)합니다.
