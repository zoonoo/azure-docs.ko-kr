---
title: Azure AD Connect 클라우드 동기화의 특성 매핑
description: 이 문서에서는 Azure AD Connect의 클라우드 동기화 기능을 사용하여 특성을 매핑하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/30/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e861a1adc2dead2ee7c4397b9fb09aae202aaf94
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963696"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화의 특성 매핑

Azure AD(Azure Active Directory) Connect의 클라우드 동기화 기능을 사용하여 온-프레미스 사용자 또는 그룹 개체와 Azure AD 개체 간에 특성을 매핑할 수 있으며 해당 기능이 클라우드 동기화 구성에 추가되었습니다.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정(변경, 삭제 또는 생성)할 수 있습니다. 동기화되는 특성 목록은 [Azure Active Directory에 동기화되는 특성](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)을 참조하세요.

> [!NOTE]
> 이 문서에서는 Azure Portal을 사용하여 특성을 매핑하는 방법을 설명합니다.  Microsoft Graph 사용에 대한 자세한 내용은 [변환](how-to-transformation.md)을 참조하세요.

## <a name="understand-types-of-attribute-mapping"></a>특성 매핑 유형 이해
특성 매핑으로 Azure AD에서 특성이 채워지는 방법을 제어합니다. Azure AD는 다음 네 가지 매핑 유형을 지원합니다.

|매핑 유형|Description|
|-----|-----|
|**수동으로 설치**|대상 특성이 Active Directory에서 연결된 개체의 특성 값으로 채워집니다.|
|**Constant**|대상 특성이 지정한 특정 문자열로 채워집니다.|
|**식**|대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다. 자세한 내용은 [식 작성기](how-to-expression-builder.md) 및 [Azure Active Directory의 특성 매핑에 대한 식 작성](reference-expressions.md)을 참조하세요.|
|**없음**|대상 특성이 수정되지 않고 남아있습니다. 그러나 대상 특성이 비어 있으면 지정한 기본값으로 채워집니다.|

해당 기본 유형과 함께 사용자 지정 특성 매핑은 ‘기본’ 값 할당(선택 사항)의 개념을 지원합니다. 기본값 할당은 Azure AD 또는 대상 개체에 값이 없는 경우 대상 특성에 값이 채워지도록 합니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.

## <a name="schema-updates-and-mappings"></a>스키마 업데이트 및 매핑
클라우드 동기화는 경우에 따라 스키마 및 [동기화되는](../hybrid/reference-connect-sync-attributes-synchronized.md?context=%2fazure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context) 기본 특성의 목록을 업데이트합니다.  해당 기본 특성 매핑을 새 설치에 사용할 수 있지만 기존 설치에 자동으로 추가되지는 않습니다.  관련 매핑을 추가하려면 아래 단계를 수행하면 됩니다.


  1. “특성 매핑 추가”를 클릭합니다.
  2. 대상 특성 드롭다운을 선택합니다.
  3. 여기에 사용할 수 있는 새 특성이 표시됩니다.

다음은 추가된 새 매핑의 목록입니다.

추가된 특성 | 매핑 유형 | 에이전트 버전과 함께 추가됨
| ----- | -----| -----|
|preferredDatalocation|직접|1.1.359.0|
|EmployeeNumber|직접|1.1.359.0|
|UserType|직접|1.1.359.0|

UserType을 매핑하는 방법에 대한 자세한 내용은 [클라우드 동기화를 사용하여 UserType 매핑](how-to-map-usertype.md)을 참조하세요.

## <a name="understand-properties-of-attribute-mappings"></a>특성 매핑의 속성 이해

특성 매핑은 형식 속성과 함께 특정 특성을 지원합니다.  관련 특성은 선택한 매핑 유형에 따라 달라집니다.  다음 섹션에서는 개별 형식에 지원되는 특성 매핑에 대해 설명합니다.

### <a name="direct-mapping-attributes"></a>직접 매핑 특성
다음은 직접 매핑에서 지원하는 특성입니다.

- **원본 특성**: 원본 시스템(예: Active Directory)의 사용자 특성입니다.
- **대상 특성**: 대상 시스템(예: Azure Active Directory)의 사용자 특성입니다.
- **null인 경우 기본값(선택 사항)** : 원본 특성이 null인 경우 대상 시스템에 전달될 값입니다. 이 값은 사용자가 생성될 때만 프로비저닝됩니다. 기존 사용자를 업데이트할 때에는 프로비저닝되지 않습니다.  
- **이 매핑 적용**:
  - **항상**: 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.
  - **만들기 작업 시에만**: 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

 ![직접 스크린샷](media/how-to-attribute-mapping/mapping-7.png)

### <a name="constant-mapping-attributes"></a>상수 매핑 특성
다음은 상수 매핑에서 지원하는 특성입니다.

- **상수 값**: 대상 특성에 적용할 값입니다.
- **대상 특성**: 대상 시스템(예: Azure Active Directory)의 사용자 특성입니다.
- **이 매핑 적용**:
  - **항상**: 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.
  - **만들기 작업 시에만**: 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

 ![상수 스크린샷](media/how-to-attribute-mapping/mapping-9.png)

### <a name="expression-mapping-attributes"></a>식 매핑 특성
다음은 식 매핑에서 지원하는 특성입니다.

- **식**: 대상 특성에 적용할 식입니다.  자세한 내용은 [식 작성기](how-to-expression-builder.md) 및 [Azure Active Directory의 특성 매핑에 대한 식 작성](reference-expressions.md)을 참조하세요.
-  **null인 경우 기본값(선택 사항)** : 원본 특성이 null인 경우 대상 시스템에 전달될 값입니다. 이 값은 사용자가 생성될 때만 프로비저닝됩니다. 기존 사용자를 업데이트할 때에는 프로비저닝되지 않습니다. 
- **대상 특성**: 대상 시스템(예: Azure Active Directory)의 사용자 특성입니다.
 
- **이 매핑 적용**:
  - **항상**: 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.
  - **만들기 작업 시에만**: 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

 ![식 스크린샷](media/how-to-attribute-mapping/mapping-10.png)

## <a name="add-an-attribute-mapping"></a>특성 매핑 추가

새 기능을 사용하려면 다음 단계를 따릅니다.

1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2.  **Azure AD Connect** 를 선택합니다.
3.  **클라우드 동기화 관리** 를 선택합니다.

    ![클라우드 동기화를 관리하기 위한 링크를 보여 주는 스크린샷](media/how-to-install/install-6.png)

4. **구성** 아래에서 구성을 선택합니다.
5. **클릭하여 매핑 편집** 을 선택합니다.  그러면 **특성 매핑** 화면이 열립니다.

    ![특성을 추가 링크를 보여 주는 스크린샷.](media/how-to-attribute-mapping/mapping-6.png)

6.  **특성 추가** 를 선택합니다.

    ![특성 및 매핑 유형 목록과 함께 특성 추가 단추를 보여 주는 스크린샷.](media/how-to-attribute-mapping/mapping-1.png)

7. 매핑 유형을 선택합니다. 다음 중 하나일 수 있습니다.
     - **직접**: 대상 특성이 Active Directory에서 연결된 개체의 특성 값으로 채워집니다.
     - **상수**: 대상 특성이 지정한 특정 문자열로 채워집니다.
     - **식**: 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다. 
     - **없음**: 대상 특성이 수정되지 않고 남아있습니다. 
 
     자세한 내용은 위의 [특성 유형 이해](#understand-types-of-attribute-mapping)를 참조하세요.
8. 이전 단계에서 선택한 내용에 따라 다른 옵션을 입력할 수 있습니다.  관련 특성에 대한 자세한 내용은 위의 [특성 매핑 속성 이해](#understand-properties-of-attribute-mappings) 섹션을 참조하세요.
9. 해당 매핑을 적용할 시기를 선택한 다음, **적용** 을 선택합니다.
11. **특성 매핑** 화면으로 돌아가면 새 특성 매핑이 표시됩니다.  
12. **스키마 저장** 을 선택합니다.

    ![스키마 저장 단추를 보여 주는 스크린샷.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>특성 매핑 테스트

특성 매핑을 테스트하려면 [주문형 프로비저닝](how-to-on-demand-provision.md)을 사용하면 됩니다. 

1. Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2. **Azure AD Connect** 를 선택합니다.
3. **프로비저닝 관리** 를 선택합니다.
4. **구성** 아래에서 구성을 선택합니다.
5. **유효성 검사** 아래에서 **사용자 프로비전** 단추를 선택합니다. 
6. **주문형 프로비저닝** 화면에서 사용자 또는 그룹의 고유 이름을 입력하고 **프로비저닝** 단추를 선택합니다. 

   화면에 진행 중인 프로비저닝이 표시됩니다.

   ![진행 중인 프로비저닝을 보여 주는 스크린샷.](media/how-to-attribute-mapping/mapping-4.png)

8. 프로비전이 완료되면 4개의 녹색 확인 표시와 함께 성공 화면이 나타납니다. 

   **작업 수행** 에서 **세부 정보 보기** 를 선택합니다. 오른쪽에 동기화된 새 특성과 적용된 식이 표시됩니다.

   ![작업 완료 및 내보내기 세부 정보를 보여 주는 스크린샷.](media/how-to-attribute-mapping/mapping-5.png)






## <a name="next-steps"></a>다음 단계

- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
- [특성 매핑에 대한 식 작성](reference-expressions.md)
- [클라우드 동기화와 함께 식 작성기를 사용하는 방법](how-to-expression-builder.md)
- [Azure Active Directory에 동기화된 특성](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)