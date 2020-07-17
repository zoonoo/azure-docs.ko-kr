---
title: B2B 협업을 위한 셀프 서비스 등록 포털 - Azure AD
description: Azure Active Directory B2B 협업은 비즈니스 파트너가 선택적으로 회사 애플리케이션에 액세스할 수 있게 함으로써 회사 간 관계를 지원합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff18d3d9cae6e887abbd9fb1845de62386062b2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386710"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B 협업 등록을 위한 셀프 서비스

고객은 최종 사용자를 위한 [Azure Portal](https://portal.azure.com) 및 [애플리케이션 액세스 패널](https://myapps.microsoft.com)을 통해 제공되는 기본 제공 기능으로 많은 작업을 수행할 수 있습니다. 하지만 B2B 사용자가 조직의 요구에 맞출 수 있도록 온보딩 워크플로를 사용자 지정해야 할 수도 있습니다.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>B2B 게스트 사용자 등록을 위한 Azure AD 자격 관리

초대하는 조직에서는 개별적인 외부 협력자가 누구인지, 리소스에 액세스해야 하는 사람이 누구인지를 미리 알지 못할 수도 있습니다. 제어하는 정책을 통해 파트너 회사의 사용자가 스스로 등록할 수 있는 방법이 필요합니다. 다른 조직의 사용자가 액세스를 요청할 수 있도록 허용하고, 승인 시 게스트 계정으로 프로비저닝하고 그룹, 앱 및 SharePoint Online 사이트에 할당하려는 경우 [Azure AD 권한 관리](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)를 사용하여 [외부 사용자에 대한 액세스를 관리](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)하는 정책을 구성할 수 있습니다.

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory B2B 초대 API

조직은 [Microsoft Graph 초대 관리자 API](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0)를 사용하여 B2B 게스트 사용자를 위한 자체 온보딩 환경을 빌드할 수 있습니다. 셀프 서비스 B2B 게스트 사용자 등록을 제공하려는 경우 [Azure AD 자격 관리](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)를 사용하는 것이 좋습니다. 그러나 사용자 고유의 환경을 빌드하려는 경우, 예를 들어 [초대 API 만들기](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http)를 사용하여 사용자 지정된 초대 이메일을 B2B 사용자에게 직접 자동으로 보낼 수 있습니다. 또는 앱이 생성 응답에서 반환된 inviteRedeemUrl을 사용하여 초대된 사용자에게 선택한 통신 메커니즘을 통해 자신의 초대를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD B2B 협업이란?](what-is-b2b.md)
* [Azure AD B2B 협업 라이선스](licensing-guidance.md)
* [Azure Active Directory B2B 협업 자주 묻는 질문(FAQ)](faq.md)
