---
title: 전역 사용자 액세스 제어 정의
description: 대기업에서 사용자 권한은 복잡할 수 있으며 표준 사이트 및 영역 구조 외에도 전역 조직 구조에 의해 결정 될 수 있습니다.
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784291"
---
# <a name="define-global-access-control"></a>전역 액세스 제어 정의

대기업에서 사용자 권한은 복잡할 수 있으며 표준 사이트 및 영역 구조 외에도 전역 조직 구조에 의해 결정 될 수 있습니다.

전체적이 고 복잡 한 사용자 액세스 권한 요구를 지원 하기 위해 비즈니스 단위, 지역 및 사이트를 기반으로 하는 글로벌 비즈니스 토폴로지를 만들 수 있습니다. 그런 다음 이러한 엔터티에 대 한 사용자 액세스 권한을 정의할 수 있습니다.

비즈니스 토폴로지에 대 한 액세스 도구를 사용 하면 조직에서 사용자가 IoT 플랫폼용 Azure Defender에서 장치를 관리 하 고 분석 하는 위치를 효과적으로 제어 하 여 0 신뢰 전략을 구현할 수 있습니다.

## <a name="about-access-groups"></a>액세스 그룹 정보

전역 액세스 제어는 사용자 액세스 그룹 만들기를 통해 설정 됩니다. 액세스 그룹은 특정 비즈니스 엔터티에 액세스할 수 있는 사용자에 대 한 규칙으로 구성 됩니다. 그룹 작업을 통해 관련 사업부, 지역 및 사이트의 특정 사용자 역할에 대 한 Defender for IoT에 대 한 보기 및 구성 액세스를 제어할 수 있습니다.

예를 들어 Active Directory 그룹의 보안 분석가가 한 지역의 플라스틱 선과 함께 유럽 서 부 자동차 및 투명 효과 생산 라인에 모두 액세스할 수 있습니다.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="보안 분석가 Active Directory 그룹의 다이어그램입니다.":::

액세스 그룹을 만들기 전에 다음을 수행 하는 것이 좋습니다.

- 비즈니스 토폴로지를 신중 하 게 설정 합니다. 비즈니스 토폴로지에 대 한 자세한 내용은 [사이트 맵 보기 작업](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)을 참조 하세요.

- 사용자가 만드는 액세스 그룹에 연결 된 사용자를 계획 합니다. 사용자에 게 액세스 그룹을 할당 하는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

  - **Active Directory 그룹 그룹 할당**: 온-프레미스 관리 콘솔과 통합 되도록 Active Directory 인스턴스를 설정 했는지 확인 합니다.
  
  - **로컬 사용자 할당**: 사용자를 만들었는지 확인 합니다. 자세한 내용은 [사용자 정의](how-to-create-and-manage-users.md#define-users)를 참조 하세요.

관리자 사용자는 액세스 그룹에 할당할 수 없습니다. 이러한 사용자는 기본적으로 모든 비즈니스 토폴로지 엔터티에 액세스할 수 있습니다.

## <a name="create-access-groups"></a>액세스 그룹 만들기

이 섹션에서는 액세스 그룹을 만드는 방법을 설명 합니다. 만든 첫 번째 그룹에 대 한 기본 전역 비즈니스 단위 및 지역이 생성 됩니다. 첫 번째 그룹을 정의할 때 기본 엔터티를 편집할 수 있습니다.

그룹을 만들려면:

1. 온-프레미스 관리 콘솔의 측면 메뉴에서 **액세스 그룹** 을 선택 합니다.

2. :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::를 선택합니다. **액세스 그룹 추가** 대화 상자에서 액세스 그룹의 이름을 입력 합니다. 콘솔은 64 자를 지원 합니다. 이 그룹을 다른 그룹과 쉽게 구분 하는 데 도움이 되는 방식으로 이름을 할당 합니다.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="액세스 그룹을 만들 수 있는 액세스 그룹 추가 대화 상자":::

3. **Active Directory 그룹 할당** 옵션이 표시 되 면이 액세스 그룹에 Active Directory 사용자 그룹 하나를 할당할 수 있습니다.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="액세스 그룹 만들기 대화 상자에서 Active Directory 그룹을 할당 합니다.":::

   이 옵션이 표시 되지 않고 액세스 그룹에 Active Directory 그룹을 포함 하려는 경우 **시스템 설정** 을 선택 합니다. **통합** 창에서 그룹을 정의 합니다. Active Directory 구성에 표시 된 대로 정확 하 게 그룹 이름을 입력 하 고 소문자로 입력 합니다.

5. **사용자** 창에서 그룹에 필요한 만큼 사용자를 할당 합니다. 또한 사용자를 다른 그룹에 할당할 수 있습니다. 이러한 방식으로 작업 하는 경우 액세스 그룹 및 규칙을 만들어 저장 한 다음 **사용자** 창에서 그룹에 사용자를 할당 해야 합니다.

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="사용자의 역할을 관리 하 고 필요에 따라 할당 합니다.":::

6. 비즈니스 토폴로지의 액세스 요구 사항에 따라 ***이름* 에 대 한 규칙 추가** 대화 상자에서 규칙을 만듭니다. 여기에 표시 되는 옵션은 **엔터프라이즈 보기** 및 **사이트 관리** 창에서 디자인 한 토폴로지를 기반으로 합니다. 

   그룹당 규칙을 여러 개 만들 수 있습니다. 여러 사이트에서 복잡 한 액세스 세분성을 사용 하 여 작업 하는 경우 그룹 당 규칙을 두 개 이상 만들어야 할 수도 있습니다. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="시스템에 규칙을 추가 합니다.":::

만든 규칙은 **액세스 그룹 추가** 대화 상자에 나타납니다. 여기에서 삭제 하거나 편집할 수 있습니다.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="이 창에서 모든 액세스 그룹을 보고 편집 합니다.":::

### <a name="about-rules"></a>규칙 정보

규칙을 만들 때는 다음 정보에 유의 하세요.

- 액세스 그룹에 여러 규칙이 포함 된 경우 규칙 논리는 모든 규칙을 집계 합니다. 예를 들어 규칙은 및 논리를 사용 하지 않고 논리를 사용 합니다.

- 규칙이 성공적으로 적용 되려면 **사이트 관리** 창에서 영역에 센서를 할당 해야 합니다.

- 규칙 당 요소를 하나만 할당할 수 있습니다. 예를 들어 각 규칙에 대해 하나의 사업부, 하나의 지역 및 한 사이트를 할당할 수 있습니다. 예를 들어 한 Active Directory 그룹의 사용자가 다른 지역의 다른 사업부에 대 한 액세스 권한을 갖도록 하려는 경우 그룹에 더 많은 규칙을 만듭니다.

- 엔터티를 변경 하 고 변경 내용이 규칙 논리에 영향을 주는 경우 규칙은 삭제 됩니다. 토폴로지 엔터티에 대 한 변경 내용이 규칙 논리에 영향을 주는 경우 모든 규칙이 삭제 됩니다. 액세스 그룹은 유지 되지만 사용자는 온-프레미스 관리 콘솔에 로그인 할 수 없습니다. 사용자에 게 로그인 하려면 관리자에 게 문의 하 라는 알림이 표시 됩니다.

- 사업부 또는 지역이 선택 되지 않은 경우 사용자는 정의 된 모든 사업부 및 지역에 액세스할 수 있습니다.

## <a name="see-also"></a>참고 항목

[IoT 콘솔 사용자를 위한 Defender 정보](how-to-create-and-manage-users.md)
