---
title: Azure AD Connect 클라우드 동기화 실수로 인 한 삭제
description: 이 항목에서는 실수로 인 한 삭제 기능을 사용 하 여 삭제를 방지 하는 방법에 대해 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613802"
---
# <a name="accidental-delete-prevention"></a>실수로 인 한 삭제 방지

다음 문서에서는 Azure AD Connect 클라우드 동기화에 대 한 실수로 인 한 삭제 기능을 설명 합니다.  실수로 인 한 삭제 기능은 여러 사용자 및 그룹에 영향을 주는 실수로 인 한 구성 변경 및 온-프레미스 디렉터리 변경 으로부터 보호 하도록 설계 되었습니다.  이 기능을 사용하여 다음을 할 수 있습니다.

- 실수로 인 한 삭제를 방지 하는 기능을 구성 합니다. 
- 구성이 적용 되는 개체의 수 (임계값)를 설정 합니다. 
- 알림 전자 메일 주소를 설정 하 여이 시나리오에 대 한 동기화 작업이 격리 된 후 전자 메일 알림을 받을 수 있도록 합니다. 

이 기능을 사용 하려면 삭제 된 경우 동기화가 중지 되어야 하는 개체 수에 대 한 임계값을 설정 합니다.  따라서이 수에 도달 하면 동기화가 중지 되 고 지정 된 전자 메일에 알림이 전송 됩니다.  이렇게 하면 진행 상황을 조사할 수 있습니다.


## <a name="configure-accidental-delete-prevention"></a>실수로 인 한 삭제 방지 구성
새 기능을 사용 하려면 다음 단계를 수행 합니다.


1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2.  **Azure AD Connect** 를 선택합니다.
3.  **클라우드 동기화 관리** 를 선택 합니다.
4. **구성** 아래에서 구성을 선택 합니다.
5. **설정** 아래에서 다음을 입력 합니다.
    - **알림 전자 메일** -알림에 사용 되는 전자 메일
    - **실수로 인 한 삭제 방지** -이 확인란을 선택 하 여 기능을 사용 하도록 설정 합니다.
    - **실수로 인 한 삭제 임계값** -동기화 중지 및 알림을 트리거할 개체 수를 입력 합니다.

![실수로 삭제](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>다음 단계 

- [Azure AD Connect 클라우드 동기화 란?](what-is-cloud-sync.md)
- [클라우드 동기화 Azure AD Connect 설치 하는 방법](how-to-install.md)
 

