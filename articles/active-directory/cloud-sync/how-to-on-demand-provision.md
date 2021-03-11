---
title: Azure AD Connect 클라우드 동기화의 주문형 프로 비전
description: 이 문서에서는 Azure AD Connect의 클라우드 동기화 기능을 사용 하 여 구성 변경 내용을 테스트 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554278"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화의 주문형 프로 비전

Azure AD (Azure Active Directory) 연결의 클라우드 동기화 기능을 사용 하 여 이러한 변경 내용을 단일 사용자에 게 적용 하 여 구성 변경을 테스트할 수 있습니다. 이 주문형 프로 비전을 사용 하면 구성에 대 한 변경 내용이 제대로 적용 되었으며 Azure AD에 올바르게 동기화 되는지 확인 하 고 확인할 수 있습니다.  

> [!IMPORTANT] 
> 주문형 프로 비전을 사용 하는 경우 범위 지정 필터는 선택한 사용자에 게 적용 되지 않습니다. 지정 된 조직 구성 단위 외부에 있는 사용자에 대해 주문형 프로 비전을 사용할 수 있습니다.

## <a name="validate-a-user"></a>사용자 유효성 검사
주문형 프로 비전을 사용 하려면 다음 단계를 수행 합니다.

1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2.  **Azure AD Connect** 를 선택합니다.
3.  **클라우드 동기화 관리** 를 선택 합니다.

    ![클라우드 동기화를 관리 하기 위한 링크를 보여 주는 스크린샷](media/how-to-install/install-6.png)
4. **구성** 아래에서 구성을 선택 합니다.
5. **유효성 검사** 아래에서 **사용자 프로 비전** 단추를 선택 합니다. 

   ![사용자를 프로 비전 하는 단추를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-2.png)

6. **요청 시 프로 비전** 화면에서 사용자의 고유 이름을 입력 하 고 **프로 비전** 단추를 선택 합니다.  
 
   ![사용자 이름 및 프로 비전 단추를 표시 하는 스크린샷](media/how-to-on-demand-provision/on-demand-3.png)
7. 프로 비전이 완료 되 면 4 개의 녹색 확인 표시와 함께 성공 화면이 나타납니다. 모든 오류가 왼쪽에 표시 됩니다.

   ![성공적인 프로 비전을 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>프로 비전에 대 한 세부 정보 가져오기
이제 사용자 정보를 살펴보고 구성에서 변경한 내용이 적용 되었는지 여부를 확인할 수 있습니다. 이 문서의 나머지 부분에서는 성공적으로 동기화 된 사용자의 세부 정보에 표시 되는 개별 섹션에 대해 설명 합니다.

### <a name="import-user"></a>사용자 가져오기
**사용자 가져오기** 섹션은 Active Directory에서 가져온 사용자에 대 한 정보를 제공 합니다. Azure AD에 프로 비전 하기 전에 사용자가 볼 수 있는 것입니다. **자세히 보기** 링크를 선택 하 여이 정보를 표시 합니다.

![가져온 사용자에 대 한 세부 정보를 보는 단추의 스크린샷](media/how-to-on-demand-provision/on-demand-5.png)

이 정보를 사용 하 여 가져온 다양 한 특성 (및 해당 값)을 볼 수 있습니다. 사용자 지정 특성 매핑을 만든 경우 여기에서 값을 확인할 수 있습니다.

![사용자 세부 정보를 표시 하는 스크린샷](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>사용자가 범위 내에 있는지 확인
**사용자가 범위 내에 있는지 확인** 섹션은 Azure AD로 가져온 사용자가 범위 내에 있는지 여부에 대 한 정보를 제공 합니다. **자세히 보기** 링크를 선택 하 여이 정보를 표시 합니다.

![사용자 범위에 대 한 세부 정보를 볼 단추의 스크린샷](media/how-to-on-demand-provision/on-demand-7.png)

이 정보를 사용 하 여 사용자가 범위 내에 있는지 확인할 수 있습니다.

![사용자 범위 세부 정보를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>원본 시스템과 대상 시스템 간에 사용자 일치
**원본 및 대상 시스템 간에 사용자 일치** 섹션에서는 사용자가 Azure AD에 이미 있는지 여부와 새 사용자를 프로 비전 하는 대신 조인이 발생 하는지 여부에 대 한 정보를 제공 합니다. **자세히 보기** 링크를 선택 하 여이 정보를 표시 합니다.

![일치 하는 사용자에 대 한 세부 정보를 보는 단추의 스크린샷](media/how-to-on-demand-provision/on-demand-8.png)

이 정보를 사용 하 여 일치 항목을 찾을 것인지 아니면 새 사용자를 만들 것인지를 확인할 수 있습니다.

![사용자 정보를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-11.png)

일치 세부 정보에는 다음 세 가지 작업 중 하나가 포함 된 메시지가 표시 됩니다.
- **만들기**: 사용자가 Azure AD에서 만들어집니다.
- **업데이트**: 구성에서 변경한 내용에 따라 사용자가 업데이트 됩니다.
- **삭제**: 사용자가 Azure AD에서 제거 됩니다.

수행한 작업 유형에 따라 메시지는 달라 집니다.

### <a name="perform-action"></a>수행 작업
**작업 수행** 섹션에서는 구성이 적용 된 후 Azure AD로 프로 비전 되거나 내보내진 사용자에 대 한 정보를 제공 합니다. Azure AD에 프로 비전 한 후 사용자가 볼 수 있는 것입니다. **자세히 보기** 링크를 선택 하 여이 정보를 표시 합니다.

![수행 된 작업에 대 한 세부 정보를 볼 단추의 스크린샷](media/how-to-on-demand-provision/on-demand-9.png)

이 정보를 사용 하 여 구성이 적용 된 후 특성의 값을 확인할 수 있습니다. 가져온 것과 유사 하거나 다른 지 확인 합니다. 구성이 성공적으로 적용 되었습니까?  

이 프로세스를 통해 클라우드를 통해 Azure AD 테 넌 트로 이동할 때 특성 변환을 추적할 수 있습니다.

![추적 된 특성 세부 정보를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>다음 단계 

- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
- [Azure AD Connect 클라우드 동기화 설치](how-to-install.md)
 