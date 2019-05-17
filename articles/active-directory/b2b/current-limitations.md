---
title: B2B 공동 작업-Azure Active Directory의 제한 사항 | Microsoft Docs
description: Azure Active Directory B2B 협업의 현재 제한 사항
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4aae8eb29b9e90bd1cb84949e97e21ed68c04c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812777"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 협업의 제한 사항
Azure AD(Azure Active Directory) B2B 협업에는 이 문서에 설명된 제한 사항이 적용됩니다.

## <a name="possible-double-multi-factor-authentication"></a>가능한 이중 다단계 인증
Azure AD B2B를 통해 리소스 조직(초대하는 조직)에서 다단계 인증을 적용할 수 있습니다. 이 방법을 사용하는 이유는 [B2B 협업 사용자에 대한 조건부 액세스](conditional-access.md)에서 설명합니다. 파트너가 이미 Multi-Factor Authentication을 설정하여 적용 중인 경우 해당 사용자는 소속된 조직에서 인증을 한 번 수행한 후 여러분의 조직에서 다시 한 번 인증을 수행해야 할 수도 있습니다.

## <a name="instant-on"></a>인스턴트 온
B2B 협업 흐름에서 해당 디렉터리에 사용자를 추가하고 초대 상환, 앱 할당 등에서 사용자를 동적으로 업데이트합니다. 업데이트 및 쓰기는 일반적으로 한 디렉터리 인스턴스에서 발생하며 모든 인스턴스에 걸쳐 복제해야 합니다. 모든 인스턴스가 업데이트되면 복제가 완료됩니다. 개체가 한 인스턴스에서 기록 또는 업데이트되고 이 개체를 검색하기 위한 호출이 다른 인스턴스에 대해 수행될 경우 복제 대기 시간이 발생할 수 있습니다. 이 문제가 발생할 경우 새로 고치거나 다시 시도하면 도움이 됩니다. API를 사용하여 앱을 작성할 경우 백오프를 사용하여 다시 시도하는 것은 이 문제를 완화하기 위한 좋은 방어 사례입니다.

## <a name="azure-ad-directories"></a>Azure AD 디렉터리
Azure AD B2B에는 Azure AD 서비스 디렉터리 제한이 적용됩니다. 사용자가 만들 수 있는 디렉터리 수와 사용자 또는 게스트 사용자가 속할 수 있는 디렉터리 수에 대한 자세한 내용은 [Azure AD 서비스 한도 및 제한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
- [B2B 공동 작업 초대 위임](delegate-invitations.md)

