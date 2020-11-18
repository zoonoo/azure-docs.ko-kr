---
title: Azure Active Directory를 사용한 HR 기반 프로비저닝이란? | Microsoft Docs
description: HR 기반 프로비저닝의 개요를 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaf033f7da7a5bd4882d0c78a2321640aa64d8ac
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646562"
---
# <a name="what-is-hr-driven-provisioning"></a>HR 기반 프로비저닝이란?

![HR 프로비저닝](./media/what-is-hr-driven-provisioning/cloud2a.png)

HR 기반 프로비저닝은 인적 자원 시스템을 기반으로 디지털 ID를 만드는 프로세스입니다.  HR 시스템은 새로 생성된 디지털 ID에 대한 권한이 되기 때문에 많은 프로비저닝 프로세스의 시작점이 됩니다.  예를 들어 신입 직원이 회사에 입사하면 인적 자원 시스템에 생성됩니다.  생성은 Active Directory에 사용자 계정 프로비저닝을 트리거한 다음, Azure AD Connect가 이 계정을 Azure AD 등에 프로비저닝합니다.

HR 기반 프로비저닝은 온-프레미스 기반 또는 클라우드 기반 중 하나일 수 있습니다.

## <a name="on-premises-based-hr-provisioning"></a>온-프레미스 기반 HR 프로비저닝
온-프레미스 기반 HR 프로비저닝은 로컬 HR 시스템과 새 디지털 ID를 프로비저닝하는 수단을 사용하여 수행됩니다.

HR 시스템은 다양한 패키지, 소프트웨어 번들로 제공되며 SQL 서버, LDAP 디렉터리 등을 사용할 수 있습니다.

현재 Microsoft 온-프레미스 HR 프로비저닝 솔루션은 이러한 HR 시스템에서 새 ID를 만들 때 Microsoft Identity Manager를 사용하여 프로비저닝을 트리거합니다.

MIM을 사용하여 온-프레미스 HR 시스템에서 Active Directory 또는 Azure AD로 사용자를 프로비저닝할 수 있습니다.

Microsoft Identity Manager 및 지원되는 시스템에 대한 자세한 내용은 [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) 설명서를 참조하세요.

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>다음 단계 
- [ID 수명 주기 관리란?](what-is-identity-lifecycle-management.md)
- [프로비저닝이란?](what-is-provisioning.md)
- [앱 프로비저닝이란?](what-is-app-provisioning.md)
- [디렉터리 간 프로비저닝이란?](what-is-inter-directory-provisioning.md)
