---
title: 새 에이전트 구성을 Azure AD Connect 클라우드 프로 비전
description: 이 항목에서는 클라우드 프로 비전을 설치 하는 방법을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794303"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect 클라우드 프로 비전 새 구성

에이전트를 설치한 후 Azure Portal에 로그인 하 고 프로 비전을 구성 해야 합니다.  에이전트를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

## <a name="configure-provisioning"></a>프로 비전 구성
프로 비전을 구성 하려면 다음 단계를 사용 합니다.

1.  Azure AD 포털에서 **Azure Active Directory** 를 클릭 합니다.
2.  **Azure AD Connect** 클릭
3.  **프로 비전 관리 (미리 보기)** 
![](media/how-to-configure/manage1.png)를 선택 합니다.

4.  **새 구성**을 클릭 합니다.
5.  구성 화면에서 온-프레미스 도메인은 미리 채워져 있습니다.
6. **알림 전자 메일**을 입력 합니다. 이 전자 메일에 알림이 표시 됩니다. 
7. 프로 비전이 정상이 아닌 경우  
8. 선택기를 **설정** 으로 이동 하 고 **저장**을 클릭 합니다.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>특정 사용자 및 그룹에 프로 비전 범위 지정
특정 사용자 및 그룹만 동기화 하도록 에이전트 범위를 지정 하려는 경우이 작업을 수행할 수 있습니다. 온-프레미스 AD 그룹 또는 조직 구성 단위를 사용 하 여 범위를 지정할 수 있습니다. 구성 내에서는 그룹 및 조직 구성 단위를 구성할 수 없습니다. 

1.  Azure AD 포털에서 **Azure Active Directory** 를 클릭 합니다.
2.  **Azure AD Connect** 클릭
3.  **프로 비전 관리 (미리 보기)를** 선택 합니다.
4.  **구성** 아래에서 구성을 클릭 합니다.  
![](media/how-to-configure/scope1.png)

5.  **구성**아래에서 **모든 사용자** 를 선택 하 여 구성 규칙의 범위를 변경 합니다.
![](media/how-to-configure/scope2.png)

6. 오른쪽에서 그룹의 고유 이름을 입력 하 고 **추가**를 클릭 하 여 보안 그룹만 포함 하도록 범위를 변경할 수 있습니다.
![](media/how-to-configure/scope3.png)

7. 또는 특정 Ou만 포함 하도록 변경 합니다. **완료** 를 클릭 하 고 **저장**합니다.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>프로 비전 다시 시작 
다음 예약 된 실행을 기다리지 않으려면 프로 비전 다시 시작 단추를 사용 하 여 프로 비전 실행을 트리거할 수 있습니다. 
1.  Azure AD 포털에서 **Azure Active Directory** 를 클릭 합니다.
2.  **Azure AD Connect** 클릭
3.  **프로 비전 관리 (미리 보기)를** 선택 합니다.
4.  **구성** 아래에서 구성을 클릭 합니다.  
![](media/how-to-configure/scope1.png)

5.  위쪽에서 **프로 비전 다시 시작**을 클릭 합니다.

## <a name="removing-a-configuration"></a>구성 제거
구성을 삭제 하려면 다음 단계를 사용 하 여이 작업을 수행할 수 있습니다.

1.  Azure AD 포털에서 **Azure Active Directory** 를 클릭 합니다.
2.  **Azure AD Connect** 클릭
3.  **프로 비전 관리 (미리 보기)를** 선택 합니다.
4.  **구성** 아래에서 구성을 클릭 합니다.  
![](media/how-to-configure/scope1.png)

5.  위쪽에서 **삭제**를 클릭 합니다.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>구성을 삭제 하기 전에 확인이 수행 되지 않으므로 **삭제**를 클릭 하기 전에 수행 하려는 작업을 수행 해야 합니다.


## <a name="next-steps"></a>다음 단계 

- [프로 비전 이란?](what-is-provisioning.md)
- [클라우드 프로 비전 Azure AD Connect 이란?](what-is-cloud-provisioning.md)
