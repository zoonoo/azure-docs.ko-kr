---
title: 새 에이전트 구성을 Azure AD Connect 클라우드 프로 비전
description: 이 문서에서는 클라우드 프로 비전을 설치 하는 방법을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853950cf441007eac0170f32f28f2d9c16a71292
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549434"
---
# <a name="azure-ad-connect-cloud-provisioning-new-agent-configuration"></a>새 에이전트 구성을 Azure AD Connect 클라우드 프로 비전

에이전트를 설치한 후 Azure Portal에 로그인 하 고 Azure Active Directory (Azure AD) Connect cloud 프로 비전을 구성 해야 합니다. 에이전트를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

## <a name="configure-provisioning"></a>프로 비전 구성
프로 비전을 구성 하려면 다음 단계를 수행 합니다.

1.  Azure Portal에서 **Azure Active Directory**를 선택합니다.
1.  **Azure AD Connect**를 선택합니다.
1.  **프로 비전 관리 (미리 보기)** 를 선택 합니다.

    ![프로 비전 관리 (미리 보기)](media/how-to-configure/manage1.png)

1.  **새 구성**을 선택 합니다.
1.  구성 화면에서 온-프레미스 도메인은 미리 채워져 있습니다.
1.  **알림 전자 메일**을 입력 합니다. 프로 비전이 정상이 아닌 경우이 전자 메일에 알림이 표시 됩니다.
1.  선택기를 **사용**으로 이동 하 고 **저장**을 선택 합니다.

    ![Azure AD 프로 비전 (미리 보기)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>특정 사용자 및 그룹에 프로 비전 범위 지정
온-프레미스 Active Directory 그룹 또는 조직 구성 단위를 사용 하 여 에이전트의 범위를 지정 하 여 특정 사용자 및 그룹을 동기화 할 수 있습니다. 구성 내에서는 그룹 및 조직 구성 단위를 구성할 수 없습니다. 

1.  Azure Portal에서 **Azure Active Directory**를 선택합니다.
1.  **Azure AD Connect**를 선택합니다.
1.  **프로 비전 관리 (미리 보기)** 를 선택 합니다.
1.  **구성**아래에서 구성을 선택 합니다.

    ![구성 섹션](media/how-to-configure/scope1.png)

1.  **구성** 아래에서 **모든 사용자**를 선택하여 구성 규칙의 범위를 변경합니다.

    ![모든 사용자 옵션](media/how-to-configure/scope2.png)

1. 오른쪽에서 보안 그룹만 포함 하도록 범위를 변경할 수 있습니다. 그룹의 고유 이름을 입력 하 고 **추가**를 선택 합니다.

    ![선택한 보안 그룹 옵션](media/how-to-configure/scope3.png)

1.  또는 특정 조직 구성 단위만 포함 하도록 범위를 변경할 수 있습니다. **완료** 를 선택 하 고 **저장**을 선택 합니다.

    ![선택한 조직 구성 단위 옵션](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>프로 비전 다시 시작 
다음 예약 된 실행을 기다리지 않으려면 **프로 비전 다시 시작** 단추를 사용 하 여 프로 비전 실행을 트리거합니다. 
1.  Azure Portal에서 **Azure Active Directory**를 선택합니다.
1.  **Azure AD Connect**를 선택합니다.
1.  **프로 비전 관리 (미리 보기)** 를 선택 합니다.
1.  **구성**아래에서 구성을 선택 합니다.

    ![프로 비전 다시 시작에 대 한 구성 선택](media/how-to-configure/scope1.png)

1.  위쪽에서 **프로 비전 다시 시작**을 선택 합니다.

## <a name="remove-a-configuration"></a>구성 제거
구성을 삭제 하려면 다음 단계를 수행 합니다.

1.  Azure Portal에서 **Azure Active Directory**를 선택합니다.
1.  **Azure AD Connect**를 선택합니다.
1.  **프로 비전 관리 (미리 보기)** 를 선택 합니다.
1.  **구성**아래에서 구성을 선택 합니다.

    ![구성을 제거 하기 위한 구성 선택](media/how-to-configure/scope1.png)

1.  구성 화면 위쪽에서 **삭제**를 선택 합니다.

    ![삭제 단추](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>구성을 삭제 하기 전에 확인이 없습니다. **삭제**를 선택 하기 전에 수행 하려는 작업 인지 확인 합니다.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
