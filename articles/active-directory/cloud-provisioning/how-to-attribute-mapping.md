---
title: Azure AD Connect 클라우드 프로 비전 특성 편집기
description: 이 문서에서는 특성 편집기를 사용 하는 방법을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b7aa0bf8c82990a00b4e41041145a67ee2f02b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637221"
---
# <a name="azure-ad-connect-cloud-provisioning-attibute-mapping"></a>Azure AD Connect 클라우드 프로 비전 특성이 있는데 매핑

Azure AD Connect 클라우드 프로 비전에는 온-프레미스 사용자/그룹 개체와 Azure AD의 개체 간에 특성을 쉽게 매핑할 수 있도록 하는 새로운 기능이 도입 되었습니다.  이 기능은 클라우드 프로 비전 구성에 추가 되었습니다.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다.  동기화 되는 특성 목록은 [동기화 된 특성](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)을 참조 하세요.

## <a name="understanding-attribute-mapping-types"></a>특성 매핑 유형 이해
특성 매핑을 사용 하 여 Azure AD에서 특성을 채우는 방법을 제어 합니다.
4 가지의 다른 매핑 형식이 지원됩니다.

- **직접** – target 특성은 AD에서 연결 된 개체의 특성 값으로 채워집니다.
- **상수** – 대상 특성이 지정한 특정 문자열로 채워집니다.
- **식** - 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다.
  자세한 내용은 [특성 매핑에 대 한 식 작성](reference-expressions.md)을 참조 하세요.
- **None** - 대상 특성이 수정되지 않고 남아있습니다. 그러나 대상 특성이 비어 있으면 지정한 기본 값으로 채워집니다.

이러한 4가지 기본 유형과 함께 사용자 지정 특성 매핑은 **기본** 값 할당(선택 사항)의 개념을 지원합니다. 기본 값 할당은 Azure AD 또는 대상 개체에 값이 없는 경우 대상 특성에 값이 채워지도록 합니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.

## <a name="understanding-attribute-mapping-properties"></a>특성 매핑 속성 이해

이전 섹션에서 특성 매핑 유형 속성을 이미 소개했습니다.
이 속성과 함께 특성 매핑은 다음 특성도 지원합니다.

- **원본 특성** -원본 시스템의 사용자 특성 (예: Active Directory).
- **대상 특성** – 대상 시스템의 사용자 특성 (예: Azure Active Directory).
- **null인 경우 기본값(선택 사항)** - 원본 속성이 null인 경우 대상 시스템에 전달될 값입니다. 이 값은 사용자가 생성될 때만 프로비저닝됩니다. 기존 사용자를 업데이트할 때는 "null 인 경우 기본값"이 프로비저닝되지 않습니다.  
- **이 매핑 적용**
  - **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.
  - **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

> [!NOTE]
> 이 문서에서는 Azure Portal를 사용 하 여 특성을 매핑하는 방법에 대해 설명 합니다.  그래프 사용에 대 한 자세한 내용은 [변환](how-to-transformation.md) 을 참조 하세요.

## <a name="using-attribute-mapping"></a>특성 매핑 사용
새 기능을 사용 하려면 다음 단계를 수행 합니다.

 1.  Azure Portal에서 **Azure Active Directory**를 선택 합니다.
 2.  **Azure AD Connect**를 선택합니다.
 3.  **프로 비전 관리**를 선택 합니다.

   ![프로 비전 관리](media/how-to-configure/manage1.png)
 
 4. **구성**아래에서 구성을 선택 합니다.
 5. **매핑을 편집 하려면 클릭**하십시오 .를 선택 합니다.  이렇게 하면 특성 매핑 화면이 열립니다.

 ![특성 추가](media/how-to-attribute-mapping/mapping6.png)
 6.  **특성 추가**를 클릭 합니다.

 ![매핑 유형](media/how-to-attribute-mapping/mapping1.png)
 
 7. **매핑 유형을**선택 합니다.  이 예에서는 식을 사용 합니다.
 8.  상자에 식을 입력 합니다.  이 예제에서는 다음을 사용 합니다. `Replace([mail], "@contoso.com", , ,"", ,).`
 9.  대상 특성을 입력 합니다.  이 예제에서는 ExtensionAttribute15를 사용 합니다.
 10. 적용 시기를 선택 하 고 **적용** 을 클릭 합니다.
   
   ![매핑 편집](media/how-to-attribute-mapping/mapping2a.png)
 11. 특성 매핑 화면으로 돌아가서 새 특성 매핑이 표시 됩니다.  
 12. **스키마 저장**을 클릭 합니다.

 ![스키마 저장](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>특성 매핑 테스트
특성 매핑을 테스트 하기 위해 [주문형 프로 비전](how-to-on-demand-provision.md)을 사용할 수 있습니다.  에서 

1.  Azure Portal에서 **Azure Active Directory**를 선택 합니다.
2.  **Azure AD Connect**를 선택합니다.
3.  **프로 비전 관리**를 선택 합니다.
4. **구성**아래에서 구성을 선택 합니다.
5. **유효성 검사** 아래에서 **사용자 프로 비전** 단추를 클릭 합니다. 
6. 요청 시 프로 비전 화면  사용자 또는 그룹의 **고유 이름을** 입력 하 고 **프로 비전** 단추를 클릭 합니다.  
7. 완료 되 면 성공 화면 및 4 개의 녹색 확인란이 표시 되 고 성공적으로 프로 비전 되었음을 나타냅니다.  
  ![프로 비전 성공](media/how-to-attribute-mapping/mapping4.png)
1. **작업 수행** 에서 **세부 정보 보기**를 클릭 합니다.  오른쪽에 새 특성 syncrhonized 및 적용 된 식이 표시 되어야 합니다.

  ![수행 작업](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)
- [특성 매핑에 대한 식 작성](reference-expressions.md)
- [동기화되는 특성](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)