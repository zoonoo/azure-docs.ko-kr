---
title: Azure AD Connect 클라우드 프로비저닝 지원 토폴로지 및 시나리오
description: 이 항목에서는 필수 구성 사항 및 하드웨어 요구 사항 클라우드 프로비저닝에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620866"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect 클라우드 프로비저닝 지원 토폴로지 및 시나리오
이 문서에서는 Azure AD Connect 클라우드 프로비전을 사용하는 다양한 온-프레미스 및 Azure Active Directory(Azure AD) 토폴로지에 대해 설명합니다. 이 문서에는 지원되는 구성 및 시나리오만 포함됩니다.

> [!IMPORTANT]
> Microsoft는 공식적으로 문서화된 구성 이나 작업 외부에서 Azure AD Connect 클라우드 프로비저닝을 수정 하거나 운영 하는 것을 지원 하지 않습니다. 이러한 구성 이나 작업 Azure AD Connect 클라우드 프로비저닝의 일관성 또는 지원되지 않는 상태가 발생할 수 있습니다. 결과적으로, Microsoft는 해당 배포에 대해 기술 지원을 제공할 수 없습니다.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>모든 시나리오와 토폴로지에 대해 기억해야 할 사항
다음은 솔루션을 선택할 때 염두에 두어야 할 정보 목록입니다.

- 모든 포리스트에서 사용자 및 그룹을 고유하게 식별해야 합니다.
- 클라우드 프로비저닝을 사용하면 포리스트 간에 일치가 발생하지 않습니다.
- 사용자 또는 그룹은 모든 포리스트에서 한 번만 표시되어야 합니다.
- 개체의 소스 앵커가 자동으로 선택됩니다.  ms-DS-일관성Guid를 사용 하 여 있는 경우, 그렇지 않으면 ObjectGUID가 사용 됩니다.
- 소스 앵커에 사용되는 특성은 변경할 수 없습니다.

## <a name="single-forest-single-azure-ad-tenant"></a>단일 포리스트, 단일 Azure AD 테넌트
![단일 포리스트 및 단일 테넌트에 대한 토폴로지](media/plan-cloud-provisioning-topologies/single-forest.png)

가장 간단한 토폴로지는 하나 또는 여러 개의 도메인이 있는 단일 온-프레미스 포리스트와 단일 Azure AD 테넌트입니다.  이 시나리오의 예는 [자습서: 단일 Azure AD 테넌트가 있는 단일 포리스트를](tutorial-single-forest.md) 참조하십시오.


## <a name="multi-forest-single-azure-ad-tenant"></a>다중 포리스트, 단일 Azure AD 테넌트
![다중 포리스트 및 단일 테넌트에 대한 토폴로지](media/plan-cloud-provisioning-topologies/multi-forest.png)

일반적인 토폴로지는 하나 또는 여러 도메인과 단일 Azure AD 테넌트가 있는 다중 AD 포리스트입니다.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Azure AD Connect를 갖춘 기존 포리스트, 클라우드 프로비저닝을 갖춘 새로운 포리스트
![단일 포리스트 및 단일 테넌트에 대한 토폴로지](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

이 시나리오는 토폴로지가 다중 포리스트 시나리오와 유사하지만 기존 Azure AD Connect 환경을 사용한 다음 Azure AD Connect 클라우드 프로비저닝을 사용하여 새 포리스트를 가져오는 작업이 포함됩니다.  이 시나리오의 예는 [자습서: 단일 Azure AD 테넌트가 있는 기존 포리스트를](tutorial-existing-forest.md) 참조하십시오.

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>기존 하이브리드 AD 포리스트에서 Azure AD Connect 클라우드 프로비저닝 파일럿
![단일 포리스트와 단일 테넌트에](media/plan-cloud-provisioning-topologies/migrate.png) 대한 토폴로지 파일럿 시나리오에는 동일한 포리스트에 Azure AD Connect 및 Azure AD Connect 클라우드 프로비저닝이 존재하고 그에 따라 사용자 및 그룹의 범위를 지정하는 작업이 포함됩니다. 참고: 개체는 도구 중 하나에만 범위여야 합니다. 

이 시나리오의 예는 [자습서: 기존 동기화된 AD 포리스트에서 파일럿 Azure AD Connect 클라우드 프로비저닝을](tutorial-pilot-aadc-aadccp.md) 참조하십시오.



## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)

