---
title: Azure AD Connect에서 동기화되지 않는 특성 문제 해결 | Microsoft Docs
description: 이 토픽에서는 문제 해결 작업을 사용하여 특성 동기화 관련 문제를 해결하는 단계를 안내합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180828"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Azure AD Connect에서 동기화되지 않는 특성 문제 해결

## <a name="recommended-steps"></a>**권장되는 단계**

특성 동기화 문제를 조사하기 전에, **Azure AD Connect** 동기화 프로세스부터 살펴보겠습니다.

  ![Azure AD Connect 동기화 프로세스](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**용어**

* **CS:** 커넥터 공간(데이터베이스의 테이블)
* **MV:** 메타버스(데이터베이스의 테이블)
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**동기화 단계**

* AD에서 가져오기: Active Directory 개체를 AD CS로 가져옵니다.

* AAD에서 가져오기: Azure Active Directory 개체를 AAD CS로 가져옵니다.

* 동기화: **인바운드 동기화 규칙** 및 **아웃바운드 동기화 규칙**은 우선 순위 번호가 낮은 규칙부터 차례로 실행됩니다. 동기화 규칙을 보려면 데스크톱 애플리케이션에서 **동기화 규칙 편집기**로 이동하면 됩니다. **인바운드 동기화 규칙**은 CS에서 MV로 데이터를 가져옵니다. **아웃바운드 동기화 규칙**은 MV에서 CS로 데이터를 가져옵니다.

* AD로 내보내기: 동기화를 실행하고 나면 AD CS에서 **Active Directory**로 개체가 내보내집니다.

* AAD로 내보내기: 동기화를 실행하고 나면 AAD CS에서 **Azure Active Directory**로 개체가 내보내집니다.

### <a name="step-by-step-investigation"></a>**단계별 조사**

* **메타버스**에서 검색을 시작하여 원본에서 대상으로의 특성 매핑을 확인하겠습니다.

* 아래와 같이 데스크톱 애플리케이션에서 **Synchronization Service Manager**를 시작합니다.

  ![Synchronization Service Manager 시작](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* **Synchronization Service Manager**에서 **메타버스 검색**을 선택하고, **개체 유형별 범위**를 선택하고, 특성을 사용하는 개체를 선택하고, **검색** 단추를 클릭합니다.

  ![메타버스 검색](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* **메타버스** 검색에서 찾은 개체를 두 번 클릭하여 모든 특성을 봅니다. **커넥터** 탭을 클릭하면 모든 **커넥터 공간**의 해당 개체를 살펴볼 수 있습니다.

  ![메타버스 개체 커넥터](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* **Active Directory 커넥터**를 두 번 클릭하여 **커넥터 공간** 특성을 봅니다. **미리 보기** 단추를 클릭하고, 다음 대화 상자에서 **미리 보기 생성** 단추를 클릭합니다.

  ![커넥터 공간 특성](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* **가져오기 특성 흐름**을 클릭하면 **Active Directory 커넥터 공간**에서 **메타버스**로의 특성 흐름이 표시됩니다. **동기화 규칙** 열에는 해당 특성에 적용되는 **동기화 규칙**이 표시됩니다. **데이터 원본** 열에는 **커넥터 공간**의 특성이 표시됩니다. **메타버스 특성** 열에는 **메타버스**의 특성이 표시됩니다. 여기서 동기화되지 않는 특성을 찾을 수 있습니다. 여기서 특성을 찾지 못하면 매핑되지 않은 것이며, 특성을 매핑하는 새 사용자 지정 **동기화 규칙**을 만들어야 합니다.

  ![커넥터 공간 특성](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* 왼쪽 창에서 **내보내기 특성 흐름**을 클릭하여 **아웃바운드 동기화 규칙**을 통해 **메타버스**에서 **Active Directory 커넥터 공간**으로 돌아하는 특성 흐름을 봅니다.

  ![커넥터 공간 특성](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* 마찬가지로, **Azure Active Directory 커넥터 공간** 개체를 볼 수 있으며, **메타버스**에서 **커넥터 공간**으로 그리고 그 반대로 흐르는 특성 흐름을 보는 **미리 보기**를 만들 수 있습니다. 이러한 방식으로 특성이 동기화되지 않는 이유를 조사할 수 있습니다.

## <a name="recommended-documents"></a>**권장되는 문서**
* [Azure AD Connect 동기화: 기술 개념](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect 동기화: 아키텍처 이해](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect 동기화: 선언적 프로비전 이해](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect 동기화: 선언적 프로비전 식 이해](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect 동기화: 기본 구성 이해](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect 동기화: 사용자, 그룹 및 연락처 이해](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect 동기화: 섀도 특성](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>다음 단계

- [Azure AD Connect 동기화](how-to-connect-sync-whatis.md)
- [하이브리드 ID란?](whatis-hybrid-identity.md)
