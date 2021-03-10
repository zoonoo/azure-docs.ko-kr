---
title: Azure AD Connect 클라우드 동기화의 특성 매핑
description: 이 문서에서는 Azure AD Connect의 클라우드 동기화 기능을 사용 하 여 특성을 매핑하는 방법을 설명 합니다.
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
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555213"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화의 특성 매핑

Azure AD (Azure Active Directory) 연결의 클라우드 동기화 기능을 사용 하 여 온-프레미스 사용자 또는 그룹 개체와 Azure AD의 개체 간에 특성을 매핑할 수 있습니다. 이 기능은 클라우드 동기화 구성에 추가 되었습니다.

비즈니스 요구에 따라 기본 특성 매핑을 사용자 지정 (변경, 삭제 또는 만들 수 있음) 할 수 있습니다. 동기화 되는 특성의 목록은 [Azure Active Directory로 동기화 된 특성](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)을 참조 하세요.

## <a name="understand-types-of-attribute-mapping"></a>특성 매핑의 유형 이해
특성 매핑을 사용 하 여 Azure AD에서 특성을 채우는 방법을 제어 합니다. Azure AD는 다음 네 가지 매핑 유형을 지원 합니다.

- **Direct**: 대상 특성이 Active Directory에 있는 연결 된 개체의 특성 값으로 채워집니다.
- **상수**: 대상 특성이 지정 된 특정 문자열로 채워집니다.
- **식**: 대상 특성이 스크립트와 유사한 식의 결과에 따라 채워집니다. 자세한 내용은 [Azure Active Directory에서 특성 매핑에 대 한 식 작성](reference-expressions.md)을 참조 하세요.
- **None**: 대상 특성이 수정 되지 않은 상태로 유지 됩니다. 그러나 대상 특성이 비어 있는 경우에는 사용자가 지정한 기본값으로 채워집니다.

이러한 기본 형식과 함께 사용자 지정 특성 매핑은 선택적 *기본값* 할당의 개념을 지원 합니다. 기본 값 할당은 Azure AD 또는 대상 개체에 값이 없는 경우 대상 특성이 값으로 채워지는지 확인 합니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.

## <a name="understand-properties-of-attribute-mapping"></a>특성 매핑의 속성 이해

형식 속성과 함께 특성 매핑은 다음 특성을 지원 합니다.

- **원본 특성**: 원본 시스템의 사용자 특성 (예: Active Directory).
- **대상 특성**: 대상 시스템의 사용자 특성 (예: Azure Active Directory).
- **Null (선택 사항) 인 경우 기본값** 은 원본 특성이 null 인 경우 대상 시스템에 전달 될 값입니다. 이 값은 사용자를 만들 때만 프로 비전 됩니다. 기존 사용자를 업데이트 하는 경우 프로 비전 되지 않습니다.  
- **이 매핑 적용**:
  - **항상**: 사용자 만들기 및 업데이트 작업 모두에이 매핑을 적용 합니다.
  - **만드는 동안에만**:이 매핑은 사용자 생성 작업에만 적용 합니다.

> [!NOTE]
> 이 문서에서는 Azure Portal를 사용 하 여 특성을 매핑하는 방법을 설명 합니다.  Microsoft Graph 사용에 대 한 자세한 내용은 [변환](how-to-transformation.md)을 참조 하세요.

## <a name="add-an-attribute-mapping"></a>특성 매핑 추가

새 기능을 사용 하려면 다음 단계를 수행 합니다.

1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2.  **Azure AD Connect** 를 선택합니다.
3.  **클라우드 동기화 관리** 를 선택 합니다.

    ![클라우드 동기화를 관리 하기 위한 링크를 보여 주는 스크린샷](media/how-to-install/install-6.png)

4. **구성** 아래에서 구성을 선택 합니다.
5. **매핑을 편집 하려면 클릭** 하십시오 .를 선택 합니다.  이 링크를 통해 **특성 매핑** 화면이 열립니다.

    ![특성을 추가 하기 위한 링크를 보여 주는 스크린샷](media/how-to-attribute-mapping/mapping-6.png)

6.  **특성 추가** 를 선택 합니다.

    ![특성 및 매핑 형식의 목록과 함께 특성을 추가 하는 단추를 보여 주는 스크린샷](media/how-to-attribute-mapping/mapping-1.png)

7. 매핑 유형을 선택 합니다. 이 예에서는 **식을** 사용 하 고 있습니다.
8. 상자에 식을 입력 합니다. 이 예에서는를 사용 하 고 `Replace([mail], "@contoso.com", , ,"", ,)` 있습니다.
9. 대상 특성을 입력 합니다. 이 예에서는 **ExtensionAttribute15** 를 사용 하 고 있습니다.
10. 이 매핑을 적용할 시기를 선택 하 고 **적용** 을 선택 합니다.

    ![특성 매핑을 만들기 위한 채워진 상자를 보여 주는 스크린샷](media/how-to-attribute-mapping/mapping-2a.png)

11. **특성 매핑** 화면으로 돌아가서 새 특성 매핑이 표시 됩니다.  
12. **스키마 저장** 을 선택 합니다.

    ![스키마 저장 단추를 보여 주는 스크린샷](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>특성 매핑 테스트

특성 매핑을 테스트 하기 위해 [주문형 프로 비전](how-to-on-demand-provision.md)을 사용할 수 있습니다. 

1. Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2. **Azure AD Connect** 를 선택합니다.
3. **프로 비전 관리** 를 선택 합니다.
4. **구성** 아래에서 구성을 선택 합니다.
5. **유효성 검사** 아래에서 **사용자 프로 비전** 단추를 선택 합니다. 
6. **요청 시 프로 비전** 화면에서 사용자 또는 그룹의 고유 이름을 입력 하 고 **프로 비전** 단추를 선택 합니다. 

   화면은 프로 비전이 진행 되 고 있음을 보여 줍니다.

   ![프로 비전 진행 중을 보여 주는 스크린샷](media/how-to-attribute-mapping/mapping-4.png)

8. 프로 비전이 완료 되 면 4 개의 녹색 확인 표시와 함께 성공 화면이 나타납니다. 

   **작업 수행** 에서 **세부 정보 보기** 를 선택 합니다. 오른쪽에 동기화 된 새 특성 및 식이 적용 된 것을 볼 수 있습니다.

   ![성공 및 내보내기 세부 정보를 보여 주는 스크린샷](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
- [특성 매핑에 대한 식 작성](reference-expressions.md)
- [Azure Active Directory로 동기화 되는 특성](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
