---
title: Azure AD Connect 클라우드 프로 비전 주문형 프로 비전
description: 이 문서에서는 주문형 프로 비전 기능을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91637173"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Azure AD Connect 클라우드 프로 비전 주문형 프로 비전

Azure AD Connect 클라우드 프로 비전에는 단일 사용자에 게 이러한 변경 사항을 적용 하 여 구성 변경을 테스트할 수 있는 새로운 기능이 도입 되었습니다.  이를 사용 하 여 구성에 대 한 변경 내용이 제대로 적용 되었으며 Azure AD에 올바르게 동기화 되었는지 확인할 수 있습니다.  

> [!IMPORTANT] 
> 주문형 프로 비전을 사용 하는 경우 범위 지정 필터는 선택한 사용자에 게 적용 되지 않습니다.  즉, 사용자가 지정한 Ou 외부에 있는 사용자에 대해 주문형 프로 비전을 사용할 수 있습니다.


## <a name="using-on-demand-provisioning"></a>주문형 프로 비전 사용
새 기능을 사용 하려면 다음 단계를 수행 합니다.


1.  Azure Portal에서 **Azure Active Directory**를 선택 합니다.
2.  **Azure AD Connect**를 선택합니다.
3.  **프로 비전 관리**를 선택 합니다.

    ![프로 비전 관리](media/how-to-configure/manage1.png)
4. **구성**아래에서 구성을 선택 합니다.
5. **유효성 검사** 아래에서 **사용자 프로 비전** 단추를 클릭 합니다. 

 ![사용자 프로 비전](media/how-to-on-demand-provision/on-demand2.png)

6. 요청 시 프로 비전 화면  사용자의 **고유 이름을** 입력 하 고 **프로 비전** 단추를 클릭 합니다.  
 
 ![요청 시 프로 비전](media/how-to-on-demand-provision/on-demand3.png)
7. 완료 되 면 성공 화면 및 4 개의 녹색 확인란이 표시 되 고 성공적으로 프로 비전 되었음을 나타냅니다.  모든 오류가 왼쪽에 표시 됩니다.

  ![성공](media/how-to-on-demand-provision/on-demand4.png)

이제 사용자를 확인 하 고 구성에서 변경한 내용이 적용 되었는지 확인할 수 있습니다.  이 문서의 나머지 부분에서는 성공적으로 동기화 된 사용자의 세부 정보에 표시 되는 개별 섹션에 대해 설명 합니다.

## <a name="import-user-details"></a>사용자 정보 가져오기
이 섹션에서는 Active Directory에서 가져온 사용자에 대 한 정보를 제공 합니다.  Azure AD에 프로 비전 되기 전에 사용자가 표시 되는 모양입니다.  **자세히 보기** 링크를 클릭 하 여이 정보를 표시 합니다.

![사용자 가져오기](media/how-to-on-demand-provision/on-demand5.png)

이 정보를 사용 하 여 가져온 다양 한 특성 및 해당 값을 확인할 수 있습니다.  사용자 지정 특성 매핑을 만든 경우 여기에 값을 확인할 수 있습니다.
![사용자 정보 가져오기](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>사용자가 범위 세부 정보 인지 확인
이 섹션에서는 Azure AD로 가져온 사용자가 범위 내에 있는지 여부에 대 한 정보를 제공 합니다.  **자세히 보기** 링크를 클릭 하 여이 정보를 표시 합니다.

![사용자 범위](media/how-to-on-demand-provision/on-demand7.png)

이 정보를 사용 하 여 사용자 범위에 대 한 추가 정보를 볼 수 있습니다.

![사용자 범위 세부 정보](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>원본 및 대상 시스템 간에 사용자 일치 세부 정보
이 섹션에서는 Azure AD에 사용자가 이미 존재 하 고 새 사용자를 프로 비전 하는 대신 조인이 발생 해야 하는지 여부에 대 한 정보를 제공 합니다.  **자세히 보기** 링크를 클릭 하 여이 정보를 표시 합니다.
![세부 정보 보기](media/how-to-on-demand-provision/on-demand8.png)

이 정보를 사용 하 여 일치 항목을 찾을 것인지 아니면 새 사용자를 만들 것인지를 확인할 수 있습니다.

![사용자 정보](media/how-to-on-demand-provision/on-demand11.png)

일치 하는 세부 정보에는 다음 세 가지 작업 중 하나가 포함 된 메시지가 표시 됩니다.  아래에 이 계정과 키의 예제가 나와 있습니다.
- 만들기-사용자가 Azure AD에서 생성 됩니다.
- 업데이트-구성에서 변경한 내용에 따라 사용자가 업데이트 됩니다.
- Delete-사용자가 Azure AD에서 제거 됩니다.

수행한 작업 유형에 따라 메시지는 달라 집니다.

## <a name="perform-action-details"></a>작업 세부 정보 수행
이 섹션에서는 구성이 적용 된 후 Azure AD로 프로 비전 되거나 내보내진 사용자에 대 한 정보를 제공 합니다.  사용자가 Azure AD에 프로 비전 되 면 사용자가 표시 되는 모양입니다.  **자세히 보기** 링크를 클릭 하 여이 정보를 표시 합니다.
![작업 세부 정보 수행](media/how-to-on-demand-provision/on-demand9.png)

이 정보를 사용 하 여 구성이 적용 된 후 특성의 값을 확인할 수 있습니다.  가져온 것과 유사 하거나 다른 지 확인 합니다.  구성이 성공적으로 적용 되나요?  

이렇게 하면 클라우드를 통해 Azure AD 테 넌 트로 이동 하는 동안 특성 변환을 추적할 수 있습니다.

![추적 특성](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>다음 단계 

- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
- [Azure AD Connect 클라우드 프로 비전을 설치 하는 방법](how-to-install.md)
 