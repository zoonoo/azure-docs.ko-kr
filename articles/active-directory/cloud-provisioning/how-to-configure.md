---
title: Azure AD Connect 클라우드 프로비전 새 에이전트 구성
description: 이 문서에서는 클라우드 프로비저닝을 설치하는 방법에 대해 설명합니다.
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
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620981"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Azure AD Connect 클라우드 기반 프로비저닝을 위한 새 구성 만들기

에이전트를 설치한 후에는 Azure 포털에 로그인하고 Azure Active Directory(Azure AD) Connect 클라우드 프로비저닝을 구성해야 합니다. 다음 단계에 따라 에이전트를 사용하도록 설정합니다.

## <a name="configure-provisioning"></a>프로비저닝 구성
프로비저닝을 구성하려면 다음 단계를 따르십시오.

1.  Azure Portal에서 **Azure Active Directory**를 선택합니다.
1.  **Azure AD Connect**를 선택합니다.
1.  **프로비저닝 관리(미리 보기)를 선택합니다.**

    ![프로비저닝 관리(미리 보기)](media/how-to-configure/manage1.png)

1.  **새 구성을**선택합니다.
1.  구성 화면에서 온-프레미스 도메인은 미리 채워집니다.
1.  알림 **전자 메일을**입력합니다. 프로비저닝이 정상적이지 않을 때 이 이메일에 알림이 전송됩니다.
1.  선택기를 **이동하여 를 사용하도록 설정하고** **저장을**선택합니다.

    ![Azure AD 프로비저닝(미리 보기)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>특정 사용자 및 그룹에 대한 범위 프로비전
온-프레미스 Active Directory 그룹 또는 조직 단위를 사용 하 여 특정 사용자 및 그룹을 동기화 하는 에이전트 범위를 지정할 수 있습니다. 구성 내에서 그룹 및 조직 단위를 구성할 수 없습니다. 

1.  Azure Portal에서 **Azure Active Directory**를 선택합니다.
1.  **Azure AD Connect**를 선택합니다.
1.  **프로비저닝 관리(미리 보기)를 선택합니다.**
1.  **구성에서**구성을 선택합니다.

    ![구성 섹션](media/how-to-configure/scope1.png)

1.  **구성** 아래에서 **모든 사용자**를 선택하여 구성 규칙의 범위를 변경합니다.

    ![모든 사용자 옵션](media/how-to-configure/scope2.png)

1. 오른쪽에는 보안 그룹만 포함하도록 범위를 변경할 수 있습니다. 그룹의 고유 이름을 입력하고 **추가를**선택합니다.

    ![선택한 보안 그룹 옵션](media/how-to-configure/scope3.png)

1.  또는 특정 조직 단위만 포함하도록 범위를 변경할 수 있습니다. 완료 및 **저장을** **선택합니다.**  
2.  범위를 변경한 후에는 [프로비저닝을 다시 시작하여](#restart-provisioning) 변경 내용의 즉각적인 동기화를 시작해야 합니다.

    ![선택한 조직 단위 옵션](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>프로비저닝 다시 시작 
다음 예약된 실행을 기다리지 않으려면 프로비저닝 다시 시작 단추를 사용하여 프로비저닝 실행을 **트리거합니다.** 
1.  Azure Portal에서 **Azure Active Directory**를 선택합니다.
1.  **Azure AD Connect**를 선택합니다.
1.  **프로비저닝 관리(미리 보기)를 선택합니다.**
1.  **구성에서**구성을 선택합니다.

    ![프로비저닝을 다시 시작하는 구성 선택](media/how-to-configure/scope1.png)

1.  상단에서 **프로비저닝 다시 시작을 선택합니다.**

## <a name="remove-a-configuration"></a>구성 제거
구성을 삭제하려면 다음 단계를 따르십시오.

1.  Azure Portal에서 **Azure Active Directory**를 선택합니다.
1.  **Azure AD Connect**를 선택합니다.
1.  **프로비저닝 관리(미리 보기)를 선택합니다.**
1.  **구성에서**구성을 선택합니다.

    ![구성을 제거하는 구성 선택](media/how-to-configure/scope1.png)

1.  구성 화면 상단에서 **삭제를**선택합니다.

    ![삭제 단추](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>구성을 삭제하기 전에는 확인이 없습니다. **삭제를**선택하기 전에 수행하려는 작업인지 확인합니다.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
