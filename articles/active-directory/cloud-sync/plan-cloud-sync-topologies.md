---
title: Azure AD Connect 클라우드 동기화 지원 토폴로지 및 시나리오
description: Azure AD Connect 클라우드 동기화를 사용 하는 다양 한 온-프레미스 및 Azure Active Directory (Azure AD) 토폴로지에 대해 알아봅니다.
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
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613544"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect 클라우드 동기화 지원 토폴로지 및 시나리오
이 문서에서는 Azure AD Connect 클라우드 동기화를 사용 하는 다양 한 온-프레미스 및 Azure Active Directory (Azure AD) 토폴로지에 대해 설명 합니다. 이 문서에는 지원 되는 구성 및 시나리오만 포함 되어 있습니다.

> [!IMPORTANT]
> Microsoft는 공식적으로 문서화 된 구성 또는 작업 외부에서 클라우드 동기화를 수정 하거나 Azure AD Connect 하는 것을 지원 하지 않습니다. 이러한 구성 또는 작업으로 인해 Azure AD Connect 클라우드 동기화에 대해 불일치 하거나 지원 되지 않는 상태가 될 수 있습니다. 따라서 Microsoft는 이러한 배포에 대 한 기술 지원을 제공할 수 없습니다.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>모든 시나리오 및 토폴로지에 대해 기억할 사항
다음은 솔루션을 선택할 때 염두에 두어야 하는 정보 목록입니다.

- 모든 포리스트에서 사용자 및 그룹을 고유 하 게 식별 해야 함
- 클라우드 동기화에서는 포리스트 간 일치가 발생 하지 않습니다.
- 사용자 또는 그룹은 모든 포리스트에서 한 번만 표시 되어야 합니다.
- 개체에 대 한 원본 앵커는 자동으로 선택 됩니다.  Msds-consistencyguid를 사용 합니다 (있는 경우). 그렇지 않으면 ObjectGUID가 사용 됩니다.
- 원본 앵커에 사용 되는 특성은 변경할 수 없습니다.

## <a name="single-forest-single-azure-ad-tenant"></a>단일 포리스트, 단일 Azure AD 테넌트
![단일 포리스트 및 단일 테 넌 트에 대 한 토폴로지를 보여 주는 다이어그램입니다.](media/tutorial-single-forest/diagram-2.png)

가장 간단한 토폴로지는 하나 이상의 도메인 및 단일 Azure AD 테 넌 트를 포함 하는 단일 온-프레미스 포리스트입니다.  이 시나리오의 예는 [자습서: 단일 AZURE AD 테 넌 트를 포함 하는 단일 포리스트](tutorial-single-forest.md) 를 참조 하세요.


## <a name="multi-forest-single-azure-ad-tenant"></a>다중 포리스트, 단일 Azure AD 테 넌 트
![다중 포리스트 및 단일 테 넌 트에 대 한 토폴로지](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

일반적인 토폴로지는 하나 이상의 도메인 및 단일 Azure AD 테 넌 트를 포함 하는 여러 AD 포리스트입니다.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Azure AD Connect 있는 기존 포리스트, 클라우드 프로 비전을 사용 하는 새 포리스트
![기존 포리스트와 새 포리스트에 대 한 토폴로지를 표시 하는 다이어그램입니다.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

이 시나리오는 토폴로지가 다중 포리스트 시나리오와 유사 하지만이 시나리오는 기존 Azure AD Connect 환경을 포함 하지만 Azure AD Connect 클라우드 동기화를 사용 하 여 새 포리스트를 가져오는 것입니다.  이 시나리오의 예는 [자습서: 단일 AZURE AD 테 넌 트를 포함 하는 기존 포리스트](tutorial-existing-forest.md) 를 참조 하세요.

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>기존 하이브리드 AD 포리스트에서 클라우드 동기화 Azure AD Connect 파일럿
![단일 포리스트 및 단일 테 넌 트에 대 한 토폴로지 ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) 파일럿 시나리오는 동일한 포리스트에 Azure AD Connect와 Azure AD Connect 클라우드 동기화를 모두 포함 하 고 그에 따라 사용자 및 그룹의 범위를 지정 합니다. 참고: 개체는 도구 중 하나 에서만 범위 내에 있어야 합니다. 

이 시나리오에 대 한 예는 [자습서: 기존 동기화 된 AD 포리스트의 파일럿 Azure AD Connect 클라우드 동기화](tutorial-pilot-aadc-aadccp.md) 를 참조 하세요.



## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화 란?](what-is-cloud-sync.md)

