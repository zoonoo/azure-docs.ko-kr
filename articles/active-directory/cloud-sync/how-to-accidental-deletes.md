---
title: Azure AD Connect 클라우드 동기화 실수로 인 한 삭제
description: 이 항목에서는 실수로 인 한 삭제 기능을 사용 하 여 삭제를 방지 하는 방법에 대해 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785188"
---
# <a name="accidental-delete-prevention"></a>실수로 인 한 삭제 방지

다음 문서에서는 Azure AD Connect 클라우드 동기화에 대 한 실수로 인 한 삭제 기능을 설명 합니다.  실수로 인 한 삭제 기능은 여러 사용자 및 그룹에 영향을 주는 실수로 인 한 구성 변경 및 온-프레미스 디렉터리 변경 으로부터 보호 하도록 설계 되었습니다.  이 기능을 사용하여 다음을 할 수 있습니다.

- 실수로 인 한 삭제를 방지 하는 기능을 구성 합니다. 
- 구성이 적용 되는 개체의 수 (임계값)를 설정 합니다. 
- 이 시나리오에 대 한 동기화 작업이 격리 되는 경우 전자 메일 알림을 받을 수 있도록 알림 전자 메일 주소를 설정 합니다. 

이 기능을 사용 하려면 삭제 된 경우 동기화가 중지 되어야 하는 개체 수에 대 한 임계값을 설정 합니다.  따라서이 수에 도달 하면 동기화가 중지 되 고 지정 된 전자 메일에 알림이 전송 됩니다.  이 알림을 통해 진행 상황을 조사할 수 있습니다.


## <a name="configure-accidental-delete-prevention"></a>실수로 인 한 삭제 방지 구성
새 기능을 사용 하려면 다음 단계를 수행 합니다.


1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2.  **Azure AD Connect** 를 선택합니다.
3.  **클라우드 동기화 관리** 를 선택 합니다.
4. **구성** 아래에서 구성을 선택 합니다.
5. **설정** 아래에서 다음을 입력 합니다.
    - **알림 전자 메일** -알림에 사용 되는 전자 메일
    - **실수로 인 한 삭제 방지** -이 확인란을 선택 하 여 기능을 사용 하도록 설정 합니다.
    - **실수로 인 한 삭제 임계값** -동기화를 중지 하 고 알림을 보내기 위한 개체의 수를 입력 합니다.

![실수로 삭제](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>실수로 인 한 삭제 인스턴스 복구
실수로 삭제 한 경우 프로 비전 에이전트 구성의 상태에서이를 확인할 수 있습니다.  **삭제 임계값이 초과** 되었다고 표시 됩니다.
 
![실수로 삭제 상태](media/how-to-accidental-deletes/delete-1.png)

**삭제 임계값을 초과** 하면 동기화 상태 정보가 표시 됩니다.  그러면 추가 세부 정보가 제공 됩니다. 
 
 ![동기화 상태](media/how-to-accidental-deletes/delete-2.png)

줄임표를 마우스 오른쪽 단추로 클릭 하면 다음 옵션이 표시 됩니다.
 - 프로 비전 로그 보기
 - 에이전트 보기
 - 삭제 허용

 ![마우스 오른쪽 단추로 클릭](media/how-to-accidental-deletes/delete-3.png)

**프로 비전 로그 보기** 를 사용 하 여 **StagedDelete** 항목을 확인 하 고 삭제 된 사용자에 게 제공 된 정보를 검토할 수 있습니다.
 
 ![프로비저닝 로그](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>삭제 허용

**삭제 허용** 작업은 실수로 인 한 삭제 임계값을 트리거한 개체를 삭제 합니다.  삭제를 적용 하려면 다음 절차를 따르십시오.  

1. 줄임표를 마우스 오른쪽 단추로 클릭 하 고 **삭제 허용** 을 선택 합니다.
2. 삭제를 허용 하려면 확인에서 **예** 를 클릭 합니다.
 
 ![확인 시 예](media/how-to-accidental-deletes/delete-4.png)

3. 삭제가 수락 되었다는 확인 메시지가 표시 되 고 상태가 다음 주기에 의해 정상으로 돌아옵니다. 
 
 ![삭제 허용](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>삭제 거부

삭제를 허용 하지 않으려면 다음을 수행 해야 합니다.
- 삭제 원본 조사
- 문제를 해결 합니다 (예: OU가 실수로 범위 밖으로 이동 했으며 이제 범위에 다시 추가 됨).
- 에이전트 구성에서 **다시 시작 동기화** 실행

## <a name="next-steps"></a>다음 단계 

- [클라우드 동기화 문제 해결을 Azure AD Connect 하 시겠습니까?](how-to-troubleshoot.md)
- [Azure AD Connect 클라우드 동기화 오류 코드](reference-error-codes.md)
 

