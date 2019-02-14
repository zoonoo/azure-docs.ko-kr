---
title: Azure AD Connect에서 동기화 규칙을 사용자 지정하는 방법 | Microsoft Docs
description: 이 토픽에서는 Azure AD Connect 설치 문제를 해결하는 단계를 안내합니다.
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
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172436"
---
# <a name="how-to-customize-a-synchronization-rule"></a>동기화 규칙을 사용자 지정하는 방법

## <a name="recommended-steps"></a>**권장되는 단계**

동기화 규칙 편집기를 사용하여 동기화 규칙을 편집하거나 새로 만들 수 있습니다. 동기화 규칙을 변경하려면 고급 사용자여야 합니다. 잘못 변경하면 대상 디렉터리에서 개체가 삭제될 수 있습니다. [권장되는 문서](#recommended-documents)를 읽고 동기화 규칙에 대한 지식을 습득하세요. 동기화 규칙을 수정하려면 다음 단계를 수행합니다.

* 아래와 같이 데스크톱의 애플리케이션 메뉴에서 동기화 편집기를 시작합니다.

    ![동기화 규칙 편집기 메뉴](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* 기본 동기화 규칙을 사용자 지정하기 위해, 동기화 규칙 편집기의 "편집" 단추를 클릭하여 기존 규칙을 복제합니다. 이렇게 하면 표준 기본 규칙의 복사본이 만들어지고 비활성화됩니다. 복제된 규칙 중에서 우선 순위가 100 미만인 규칙을 저장합니다.  우선 순위는 특성 흐름 충돌이 있을 때 충돌을 해결하는 규칙(숫자 값이 낮은 규칙)을 결정합니다.

    ![동기화 규칙 편집기](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* 특정 특성을 수정할 때 복제된 규칙에 수정 특성만 있는 것이 가장 이상적입니다.  그 후 수정된 특성은 복제된 규칙에서 오고 다른 속성은 기본 표준 규칙에서 선택되도록 기본 규칙을 사용합니다. 

* 수정된 속성의 계산 값이 복제된 규칙에서 NULL이고 기본 표준 규칙에서는 NULL이 아닌 경우 not NULL 값이 승리하고 NULL 값을 대체합니다. NULL 값이 not NULL 값으로 바뀌지 않게 하려면 복제된 규칙에서 AuthoritativeNull을 할당합니다.

* **아웃바운드** 규칙을 수정하려면 동기화 규칙 편집기에서 필터를 변경합니다.

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
