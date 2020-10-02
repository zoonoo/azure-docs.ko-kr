---
title: 새 에이전트 구성을 Azure AD Connect 클라우드 프로 비전
description: 이 문서에서는 클라우드 프로 비전을 설치 하는 방법을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628900"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Azure AD Connect 클라우드 기반 프로 비전을 위한 새 구성 만들기

에이전트를 설치한 후 Azure Portal에 로그인 하 고 Azure Active Directory (Azure AD) Connect cloud 프로 비전을 구성 해야 합니다. 에이전트를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

## <a name="configure-provisioning"></a>프로 비전 구성
프로 비전을 구성 하려면 다음 단계를 수행 합니다.

 1. Azure Portal에서를 선택 **Azure Active Directory**
 2. **Azure AD Connect**를 선택합니다.
 3. **프로 비전 관리**를 선택 합니다.

 ![프로 비전 관리](media/how-to-configure/manage1.png)
 
 4. **새 구성**을 선택 합니다.
 5. 구성 화면에서 도메인을 선택 하 고 암호 해시 동기화를 사용 하도록 설정할지 여부를 선택 합니다.  **만들기**를 클릭 합니다.  
 
 ![새 구성 만들기](media/how-to-configure/configure1.png)


 6.  프로 비전 구성 편집 화면이 열립니다.

   ![구성 편집](media/how-to-configure/configure2.png)

 7. **알림 전자 메일**을 입력 합니다. 프로 비전이 정상이 아닌 경우이 전자 메일에 알림이 표시 됩니다.
 8. 선택기를 사용으로 이동 하 고 저장을 선택 합니다.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>특정 사용자 및 그룹에 프로 비전 범위 지정
온-프레미스 Active Directory 그룹 또는 조직 구성 단위를 사용 하 여 에이전트의 범위를 지정 하 여 특정 사용자 및 그룹을 동기화 할 수 있습니다. 구성 내에서는 그룹 및 조직 구성 단위를 구성할 수 없습니다. 

 1.  Azure Portal에서 **Azure Active Directory**를 선택 합니다.
 2. **Azure AD Connect**를 선택합니다.
 3. **프로 비전 관리 (미리 보기)** 를 선택 합니다.
 4. **구성**아래에서 구성을 선택 합니다.

 ![구성 섹션](media/how-to-configure/scope1.png)
 
 5. **구성**에서 범위 지정 **필터 편집** 을 선택 하 여 구성 규칙의 범위를 변경 합니다.
 ![범위 편집](media/how-to-configure/scope3.png)
 7. 오른쪽에서 범위를 변경할 수 있습니다.  완료 되 면 **완료**  를 클릭 하 고 **저장** 을 클릭 합니다.
 8. 범위를 변경한 후에는 [프로 비전을 다시 시작](#restart-provisioning) 하 여 변경 내용에 대 한 즉각적인 동기화를 시작 해야 합니다.

## <a name="attribute-mapping"></a>특성 매핑
Azure AD Connect 클라우드 프로 비전을 사용 하면 온-프레미스 사용자/그룹 개체와 Azure AD의 개체 간에 특성을 쉽게 매핑할 수 있습니다.  비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다.  자세한 내용은 [특성 매핑](how-to-attribute-mapping.md)을 참조 하세요.

## <a name="on-demand-provisioning"></a>주문형 프로 비전
Azure AD Connect 클라우드 프로 비전을 사용 하면 이러한 변경 사항을 단일 사용자 또는 그룹에 적용 하 여 구성 변경을 테스트할 수 있습니다.  이를 사용 하 여 구성에 대 한 변경 내용이 제대로 적용 되었으며 Azure AD에 올바르게 동기화 되었는지 확인할 수 있습니다.  자세한 내용은 [주문형 프로 비전을](how-to-on-demand-provision.md)참조 하세요.

## <a name="restart-provisioning"></a>프로 비전 다시 시작 
다음 예약 된 실행을 기다리지 않으려면 **프로 비전 다시 시작** 단추를 사용 하 여 프로 비전 실행을 트리거합니다. 
 1.  Azure Portal에서 **Azure Active Directory**를 선택 합니다.
 2. **Azure AD Connect**를 선택합니다.
 3.  **프로 비전 관리 (미리 보기)** 를 선택 합니다.
 4. **구성**아래에서 구성을 선택 합니다.

   ![프로 비전 다시 시작에 대 한 구성 선택](media/how-to-configure/scope1.png)

 5. 위쪽에서 **프로 비전 다시 시작**을 선택 합니다.

## <a name="remove-a-configuration"></a>구성 제거
구성을 삭제 하려면 다음 단계를 수행 합니다.

 1.  Azure Portal에서 **Azure Active Directory**를 선택 합니다.
 2. **Azure AD Connect**를 선택합니다.
 3. **프로 비전 관리 (미리 보기)** 를 선택 합니다.
 4. **구성**아래에서 구성을 선택 합니다.
   
   ![구성을 제거 하기 위한 구성 선택](media/how-to-configure/scope1.png)

 5. 구성 화면 위쪽에서 **삭제**를 선택 합니다.

>[!IMPORTANT]
>구성을 삭제 하기 전에 확인이 없습니다. **삭제**를 선택 하기 전에 수행 하려는 작업 인지 확인 합니다.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
