---
title: Azure AD Connect cloud sync 새 에이전트 구성
description: 이 문서에서는 클라우드 동기화를 설치 하는 방법을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffdd27b67a122f82cc5fdb5568f11c85387955e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660799"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화에 대 한 새 구성 만들기

Azure AD Connect 프로 비전 에이전트를 설치한 후 Azure Portal에 로그인 하 여 구성 해야 합니다. 에이전트를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

## <a name="configure-provisioning"></a>프로 비전 구성
프로 비전을 구성 하려면 다음 단계를 수행 합니다.

 1. Azure Portal에서를 선택 **Azure Active Directory**
 2. **Azure AD Connect** 를 선택합니다.
 3. **클라우드 동기화 관리** 를 선택 합니다.

 ![프로 비전 관리](media/how-to-install/install-6.png)
 
 4. **새 구성** 을 선택 합니다.
 5. 구성 화면에서 도메인을 선택 하 고 암호 해시 동기화를 사용 하도록 설정할지 여부를 선택 합니다.  **만들기** 를 클릭 합니다.  
 
 ![새 구성 만들기](media/how-to-configure/configure-1.png)


 6.  프로 비전 구성 편집 화면이 열립니다.

   ![구성 편집](media/how-to-configure/con-1.png)

 7. **알림 전자 메일** 을 입력 합니다. 프로 비전이 정상이 아닌 경우이 전자 메일에 알림이 표시 됩니다.  **실수로 인 한 삭제를 방지** 하 고 **실수로 인 한 삭제 임계값** 을 통보 하려는 숫자로 설정 하는 것이 좋습니다.  자세한 내용은 아래의 [실수로 인 한 삭제](#accidental-deletions) 를 참조 하세요.
 8. 선택기를 사용으로 이동 하 고 저장을 선택 합니다.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>특정 사용자 및 그룹에 프로 비전 범위 지정
온-프레미스 Active Directory 그룹 또는 조직 구성 단위를 사용 하 여 에이전트의 범위를 지정 하 여 특정 사용자 및 그룹을 동기화 할 수 있습니다. 구성 내에서는 그룹 및 조직 구성 단위를 구성할 수 없습니다. 

 1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3. **클라우드 동기화 관리** 를 선택 합니다.
 4. **구성** 아래에서 구성을 선택 합니다.

 ![구성 섹션](media/how-to-configure/scope-1.png)
 
 5. **구성** 에서 범위 지정 **필터 편집** 을 선택 하 여 구성 규칙의 범위를 변경 합니다.
 ![범위 편집](media/how-to-configure/scope-3.png)
 7. 오른쪽에서 범위를 변경할 수 있습니다.  완료 되 면 **완료**  를 클릭 하 고 **저장** 을 클릭 합니다.
 8. 범위를 변경한 후에는 [프로 비전을 다시 시작](#restart-provisioning) 하 여 변경 내용에 대 한 즉각적인 동기화를 시작 해야 합니다.

## <a name="attribute-mapping"></a>특성 매핑
Azure AD Connect 클라우드 동기화를 사용 하면 온-프레미스 사용자/그룹 개체와 Azure AD의 개체 간에 특성을 쉽게 매핑할 수 있습니다.  비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다.  자세한 내용은 [특성 매핑](how-to-attribute-mapping.md)을 참조 하세요.

## <a name="on-demand-provisioning"></a>주문형 프로비저닝
Azure AD Connect 클라우드 동기화를 사용 하면 이러한 변경 사항을 단일 사용자 또는 그룹에 적용 하 여 구성 변경을 테스트할 수 있습니다.  이를 사용 하 여 구성에 대 한 변경 내용이 제대로 적용 되었으며 Azure AD에 올바르게 동기화 되었는지 확인할 수 있습니다.  자세한 내용은 [주문형 프로 비전을](how-to-on-demand-provision.md)참조 하세요.

## <a name="accidental-deletions"></a>실수로 삭제
실수로 인 한 삭제 기능은 여러 사용자 및 그룹에 영향을 주는 실수로 인 한 구성 변경 및 온-프레미스 디렉터리 변경 으로부터 보호 하도록 설계 되었습니다.  이 기능을 사용하여 다음을 할 수 있습니다.

- 실수로 인 한 삭제를 방지 하는 기능을 구성 합니다. 
- 구성이 적용 되는 개체의 수 (임계값)를 설정 합니다. 
- 알림 전자 메일 주소를 설정 하 여이 시나리오에 대 한 동기화 작업이 격리 된 후 전자 메일 알림을 받을 수 있도록 합니다. 

자세한 내용은 [실수로 인 한 삭제](how-to-accidental-deletes.md) 를 참조 하세요.

## <a name="quarantines"></a>격리할
클라우드 동기화는 구성의 상태를 모니터링 하 고 비정상 개체를 격리 상태에 배치 합니다. 오류 (예: 잘못 된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행 된 대부분의 호출이 일관 되 게 실패 하는 경우 동기화 작업은 격리 된 상태로 표시 됩니다.  자세한 내용은 [격리할](how-to-troubleshoot.md#provisioning-quarantined-problems)의 문제 해결 섹션을 참조 하세요.

## <a name="restart-provisioning"></a>프로 비전 다시 시작 
다음 예약 된 실행을 기다리지 않으려면 **프로 비전 다시 시작** 단추를 사용 하 여 프로 비전 실행을 트리거합니다. 
 1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3.  **클라우드 동기화 관리** 를 선택 합니다.
 4. **구성** 아래에서 구성을 선택 합니다.

   ![프로 비전 다시 시작에 대 한 구성 선택](media/how-to-configure/scope-1.png)

 5. 위쪽에서 **프로 비전 다시 시작** 을 선택 합니다.

## <a name="remove-a-configuration"></a>구성 제거
구성을 삭제 하려면 다음 단계를 수행 합니다.

 1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3. **클라우드 동기화 관리** 를 선택 합니다.
 4. **구성** 아래에서 구성을 선택 합니다.
   
   ![구성을 제거 하기 위한 구성 선택](media/how-to-configure/scope-1.png)

 5. 구성 화면 위쪽에서 **삭제** 를 선택 합니다.

>[!IMPORTANT]
>구성을 삭제 하기 전에 확인이 없습니다. **삭제** 를 선택 하기 전에 수행 하려는 작업 인지 확인 합니다.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
