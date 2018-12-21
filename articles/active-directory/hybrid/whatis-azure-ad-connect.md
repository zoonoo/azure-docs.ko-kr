---
title: Azure AD Connect 및 Connect Health란? | Microsoft Docs
description: Azure AD로 온-프레미스 환경을 동기화하고 모니터링하는 데 사용된 도구를 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2fbd6edb02dfc4080d7692e43324a5b3981091f9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110403"
---
# <a name="what-is-azure-ad-connect"></a>Azure AD Connect란?

Azure AD Connect는 하이브리드 ID 목표를 충족하고 달성하도록 설계된 Microsoft 도구입니다.  다음과 같은 기능을 제공합니다.
    
- [암호 해시 동기화](whatis-phs.md) - 사용자 온-프레미스 AD 암호의 해시를 Azure AD와 동기화하는 로그인 메서드입니다.
- [통과 인증](how-to-connect-pta.md) - 사용자가 온-프레미스 및 클라우드에서 동일한 암호를 사용할 수 있지만 페더레이션 환경의 추가 인프라가 필요하지 않은 로그인 메서드입니다
- [페더레이션 통합](how-to-connect-fed-whatis.md) - 페더레이션은 Azure AD Connect의 선택적 부분이며 온-프레미스 AD FS 인프라를 사용하여 하이브리드 환경을 구성하는 데 사용할 수 있습니다. 또한 인증서 갱신 및 추가 AD FS 서버 배포와 같은 AD FS 관리 기능을 제공합니다.
- [동기화](how-to-connect-sync-whatis.md) - 사용자, 그룹 및 기타 개체를 생성합니다.  뿐만 아니라 온-프레미스 사용자 및 그룹의 ID 정보가 클라우드와 일치하도록 합니다.  이 동기화에는 암호 해시도 포함되어 있습니다.
-   [상태 모니터링](whatis-hybrid-identity-health.md) - Azure AD Connect Health는 강력한 모니터링을 제공하고 Azure Portal에서 중앙 위치를 제공하여 이 작업을 볼 수 있습니다. 


![Azure AD Connect의 정의](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Azure AD Connect Health란?

Azure AD(Azure Active Directory) Connect Health에서는 온-프레미스 ID 인프라에 대한 강력한 모니터링을 제공합니다. 이 기능을 사용하면 Office 365 및 Microsoft Online Services에 대한 신뢰할 수 있는 연결을 유지 관리할 수 있습니다.  핵심 ID 구성 요소에 대한 모니터링 기능을 통해 이러한 신뢰성을 보장합니다. 또한 이러한 구성 요소에 대한 핵심 데이터 요소에 쉽게 액세스할 수 있습니다.

정보는 [Azure AD Connect Health 포털](https://aka.ms/aadconnecthealth)에 표시됩니다. Azure AD Connect Health 포털을 사용하여 경고, 성능 모니터링, 사용 현황 분석, 기타 정보를 확인합니다. Azure AD Connect Health는 핵심 ID 구성 요소의 상태를 한 곳에서 볼 수 있게 해주는 단일 렌즈의 역할을 합니다.

![Azure AD Connect Health 정의](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Azure AD Connect를 사용하는 이유
Azure AD와 온-프레미스 디렉터리를 통합하면 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하므로 사용자가 더 생산성을 높일 수 있습니다. 사용자와 조직은 다음과 같은 이점을 얻을 수 있습니다.

* 사용자는 단일 ID를 사용하여 온-프레미스 응용 프로그램 및 Office 365와 같은 클라우드 서비스에 액세스할 수 있습니다.
* 동기화 및 로그인에 대해 간편한 배포 환경을 제공하는 단일 도구입니다.
* 시나리오에 대한 최신 기능을 제공합니다. Azure AD Connect는 DirSync 및 Azure AD Sync와 같은 이전 버전의 ID 통합 도구를 대체합니다. 자세한 내용은 [하이브리드 ID 디렉터리 통합 도구 비교](plan-hybrid-identity-design-considerations-tools-comparison.md)를 참조하세요.

## <a name="why-use-azure-ad-connect-health"></a>Azure AD Connect Health를 사용하는 이유
Azure AD를 사용하면 클라우드 및 온-프레미스 리소스 모두에 액세스할 수 있는 공통 ID가 제공되므로 사용자의 생산성이 향상됩니다. 사용자가 이러한 리소스에 액세스할 수 있게 되면 환경을 신뢰할 수 있는지에 대한 문제가 발생합니다.  Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라를 모니터링하고 파악할 수 있으므로 이 환경의 신뢰성을 보장합니다. 각 온-프레미스 ID 서버에 에이전트를 설치하는 것만큼 간단합니다.


## <a name="next-steps"></a>다음 단계

- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)
- [Azure AD Connect Health 에이전트 설치](how-to-connect-health-agent-install.md) 
