---
title: Azure AD에서 암호 해시 동기화란? | Microsoft Docs
description: 암호 해시 동기화를 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e172e61411c7c1c098706b5ff4566f565d6bf1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66253851"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Azure AD에서 암호 해시 동기화란?
암호 해시 동기화는 하이브리드 ID를 설정하기 위해 사용된 로그인 메서드 중 하나입니다. Azure AD Connect는 사용자 암호 해시의 해시를 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure AD 인스턴스로 동기화합니다.

암호 해시 동기화는 Azure AD Connect 동기화를 통한 디렉터리 동기화 기능 구현의 확장판입니다. 이 기능을 사용하여 Office 365와 같은 Azure AD 서비스에 로그인할 수 있습니다. 온-프레미스 Active Directory 인스턴스에 로그인하기 위해 사용하는 암호와 동일한 암호를 사용하여 서비스에 로그인합니다.

![Azure AD Connect의 정의](./media/how-to-connect-password-hash-synchronization/arch1.png)

암호 동기화를 통해 암호의 수를 줄여서 사용자가 하나의 암호를 유지할 수 있습니다. 암호 해시 동기화는 다음을 수행할 수 있습니다.

* 사용자의 생산성 향상.
* 지원 센터 비용 절감.  

필요에 따라 로그인 메서드처럼 [AD FS(Active Directory Federation Services)와 페더레이션](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)을 사용하도록 선택하는 경우 백업으로 암호 해시 동기화를 설정할 수 있습니다.

사용자 환경에서 암호 해시 동기화를 사용하려면 다음을 수행해야 합니다.

* Azure AD Connect 설치.  
* 온-프레미스 Active Directory 인스턴스 및 Azure Active Directory 인스턴스 간에 디렉터리 동기화 구성.
* 암호 해시 동기화 사용.



자세한 내용은 [하이브리드 ID란?](whatis-hybrid-identity.md)을 참조하세요.




## <a name="next-steps"></a>다음 단계

- [하이브리드 ID란?](whatis-hybrid-identity.md)
- [Azure AD Connect 및 Connect Health란?](whatis-azure-ad-connect.md)
- [PTA(통과 인증)란?](how-to-connect-pta.md)
- [페더레이션이란?](whatis-fed.md)
- [Single Sign-On이란?](how-to-connect-sso.md)
- [암호 해시 동기화 작동 방식](how-to-connect-password-hash-synchronization.md)
