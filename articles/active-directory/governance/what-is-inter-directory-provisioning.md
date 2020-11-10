---
title: Azure Active Directory를 사용한 디렉터리 간 프로비저닝이란? | Microsoft Docs
description: ID 디렉터리 간 프로비저닝에 대해 간략히 설명합니다.
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
ms.openlocfilehash: 4cbca2d00d10b6595b8a1d13251dd88c65667541
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135005"
---
# <a name="what-is-inter-directory-provisioning"></a>디렉터리 간 프로비저닝이란?

디렉터리는 항목 및 네트워크 리소스를 찾고, 관리하고, 운영하고, 구성하는 데 사용되는 공유 정보 인프라입니다.  디렉터리 서비스를 사용하는 애플리케이션의 예로는 Microsoft Active Directory 및 Azure AD가 있습니다.  ID는 디렉터리 시스템에서 누가 무엇에 액세스할 수 있는지, 누가 특정 리소스를 사용할 수 있는지 등을 결정하는 데 도움이 됩니다.

디렉터리 간 프로비저닝은 서로 다른 두 디렉터리 서비스 시스템 간에 ID를 프로비저닝하고 있습니다.   디렉터리 간 프로비저닝에 대한 가장 일반적인 시나리오는 이미 Active Directory에 있는 사용자가 Azure AD에 프로비저닝되는 경우입니다. Azure AD Connect 동기화 또는 Azure AD Connect 클라우드 프로비저닝과 같은 에이전트에서 이 프로비저닝을 수행할 수 있습니다.

디렉터리 간 프로비저닝을 통해 [하이브리드 ID](../hybrid/whatis-hybrid-identity.md) 환경을 만들 수 있습니다.


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Azure AD에서 지원하는 디렉터리 간 프로비저닝 유형

Azure AD는 현재 디렉터리 간 프로비저닝을 수행하는 세 가지 방법을 지원합니다. 이러한 메서드는 다음과 같습니다.

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) - Active Directory에서 Azure AD로의 디렉터리 간 프로비저닝을 포함하여 하이브리드 ID를 충족하고 달성하도록 설계된 Microsoft 도구입니다.

- [Azure AD Connect 클라우드 프로비저닝](../cloud-provisioning/what-is-cloud-provisioning.md) - 하이브리드 ID 목표를 충족하고 달성하도록 설계된 새로운 Microsoft 에이전트입니다.  Active Directory와 Azure AD 간에 간단한 디렉터리 간 프로비저닝 환경을 제공합니다.

- [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) - 조직 내에서 사용자, 자격 증명, 정책 및 액세스를 관리하는 데 도움이 되는 Microsoft의 온-프레미스 ID 및 액세스 관리 솔루션입니다. 또한 MIM은 Active Directory, Azure AD 및 기타 디렉터리에 대한 하이브리드 ID 환경을 달성하기 위한 고급 디렉터리 간 프로비저닝을 제공합니다.

### <a name="key-benefits"></a>주요 이점

디렉터리 간 프로비저닝의 이 기능은 다음과 같은 중요한 비즈니스 이점을 제공합니다.

- [암호 해시 동기화](../hybrid/whatis-phs.md) - 사용자 온-프레미스 AD 암호의 해시를 Azure AD와 동기화하는 로그인 메서드입니다.
- [통과 인증](../hybrid/how-to-connect-pta.md) - 사용자가 온-프레미스 및 클라우드에서 동일한 암호를 사용할 수 있지만 페더레이션 환경의 추가 인프라가 필요하지 않은 로그인 메서드입니다
- [페더레이션 통합](../hybrid/how-to-connect-fed-whatis.md) - 온-프레미스 AD FS 인프라를 사용하여 하이브리드 환경을 구성하는 데 사용할 수 있습니다. 또한 인증서 갱신 및 추가 AD FS 서버 배포와 같은 AD FS 관리 기능을 제공합니다.
- [동기화](../hybrid/how-to-connect-sync-whatis.md) - 사용자, 그룹 및 기타 개체를 생성합니다.  뿐만 아니라 온-프레미스 사용자 및 그룹의 ID 정보가 클라우드와 일치하도록 합니다.  이 동기화에는 암호 해시도 포함되어 있습니다.
- [상태 모니터링](../hybrid/whatis-hybrid-identity-health.md) - 강력한 모니터링을 제공하고 Azure Portal에서 중앙 위치를 제공하여 이 작업을 볼 수 있습니다. 


## <a name="next-steps"></a>다음 단계 
- [ID 수명 주기 관리란?](what-is-identity-lifecycle-management.md)
- [프로비저닝이란?](what-is-provisioning.md)
- [HR 기반 프로비저닝이란?](what-is-hr-driven-provisioning.md)
- [앱 프로비저닝이란?](what-is-app-provisioning.md)
