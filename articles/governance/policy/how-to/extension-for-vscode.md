---
title: 비주얼 스튜디오 코드에 대 한 Azure 정책 확장
description: Visual Studio 코드에 Azure 정책 확장을 사용하여 리소스 관리자 별칭을 찾는 방법을 알아봅니다.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 5e31af652f2746adbf0eda386bdb178c752f5f84
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641014"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>시각적 스튜디오 코드에 Azure 정책 확장 사용

> Azure 정책 확장 버전 **0.0.21** 및 최신 버전에 적용됩니다.

Visual Studio 코드에 Azure 정책 확장을 사용하여 [별칭을](../concepts/definition-structure.md#aliases) 찾고 리소스 및 정책을 검토하는 방법을 알아봅니다. 먼저 Visual Studio 코드에서 Azure 정책 확장을 설치하는 방법을 설명합니다. 그런 다음 별칭을 찾는 방법을 살펴보겠습니다.

Visual Studio 코드에 대한 Azure 정책 확장은 Visual Studio 코드에서 지원하는 모든 플랫폼에 설치할 수 있습니다. 이 지원에는 Windows, Linux 및 macOS가 포함됩니다.

> [!NOTE]
> Visual Studio 코드에 대한 Azure 정책 확장에서 볼 수 있는 정책에 대한 로컬로 변경된 내용은 Azure에 동기화되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 항목이 필요합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- [Visual Studio Code](https://code.visualstudio.com)

## <a name="install-azure-policy-extension"></a>Azure 정책 확장 설치

필수 구성 조건을 충족한 후 다음 단계를 수행하여 Visual Studio 코드에 대한 Azure 정책 확장을 설치할 수 있습니다.

1. Visual Studio Code를 엽니다.

1. 메뉴 모음에서 확장 **보기로** > **이동합니다.**

1. 검색 상자에 Azure **정책을**입력합니다.

1. 검색 결과에서 **Azure 정책을** 선택한 다음 **설치를**선택합니다.

1. 필요한 경우 **다시 로드를** 선택합니다.

## <a name="set-the-azure-environment"></a>Azure 환경 설정

국가 클라우드 사용자의 경우 다음 단계를 수행하여 Azure 환경을 먼저 설정합니다.

1. **파일\환경 설정\설정**선택 .

1. 다음 문자열에서 검색: _Azure: 클라우드_

1. 목록에서 국가 클라우드를 선택합니다.

   ![Visual Studio 코드에 대한 기본 Azure 클라우드 로그인 설정](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Azure 계정에 연결

리소스 및 조회 별칭을 평가하려면 Azure 계정에 연결해야 합니다. 다음 단계에 따라 Visual Studio 코드에서 Azure에 연결합니다.

1. Azure 정책 확장 또는 명령 팔레트에서 Azure에 로그인합니다.

   - Azure 정책 확장

     Azure 정책 확장에서 **Azure에 로그인을 선택합니다.**

     ![Azure 정책 확장에서 Visual Studio 코드에 대 한 Azure 클라우드 로그인](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - 명령 팔레트

     메뉴 모음에서**명령 팔레트** **보기로** > 이동하여 **Azure: 로그인**을 입력합니다.

     ![명령 팔레트에서 시각적 스튜디오 코드에 대 한 Azure 클라우드 로그인](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. 로그인 지침에 따라 Azure에 로그인합니다. 연결되면 Azure 계정 이름이 Visual Studio 코드 창 하단의 상태 표시줄에 표시됩니다.

## <a name="select-subscriptions"></a>구독 선택

처음 로그인하면 Azure Policy 확장에 의해 기본 구독 리소스 및 정책만 로드됩니다. 리소스 및 정책을 표시하지 못하도록 구독을 추가하거나 제거하려면 다음 단계를 따르세요.

1. 명령 팔레트 또는 창 바닥글에서 구독 명령을 시작합니다.

   - 명령 팔레트: 

     메뉴 모음에서**명령 팔레트** **보기로** > 이동하여 Azure: 구독 선택 을 **입력합니다.**

   - 창 바닥글

     화면 하단의 창 바닥글에서 Azure와 일치하는 **세그먼트: \<계정\>** 입니다.

1. 필터 상자를 사용하여 이름으로 구독을 빠르게 찾을 수 있습니다. 그런 다음 각 구독에서 검사를 확인하거나 제거하여 Azure Policy 확장에 표시된 구독을 설정합니다. 표시할 구독 추가 또는 제거가 완료되면 **확인을**선택합니다.

## <a name="search-for-and-view-resources"></a>리소스 검색 및 보기

Azure 정책 확장은 리소스 공급자및 **리소스** 창의 리소스 그룹별로 선택한 구독의 리소스를 나열합니다. 트리뷰에는 선택한 구독 내 또는 구독 수준에서 다음과 같은 리소스 그룹이 포함됩니다.

- **리소스 공급자**
  - 정책 별칭이 있는 리소스 및 관련 하위 리소스가 있는 등록된 각 리소스 공급자
- **리소스 그룹**
  - 리소스 그룹에 의한 모든 리소스

기본적으로 확장은 정책 별칭이 있는 기존 리소스 및 리소스에 의해 '리소스 공급자' 부분을 필터링합니다. **설정** > 확장**Azure 정책에서** 이 동작을 변경하여 필터링하지 않고 모든 리소스 공급자를 볼 수**있습니다.** > 

단일 구독에 수백 또는 수천 개의 리소스를 보유한 고객은 리소스를 찾는 검색 가능한 방법을 선호할 수 있습니다. Azure 정책 확장을 사용하면 다음 단계를 통해 특정 리소스를 검색할 수 있습니다.

1. Azure 정책 확장 또는 명령 팔레트에서 검색 인터페이스를 시작합니다.

   - Azure 정책 확장

     Azure 정책 확장에서 **리소스** 패널 위로 마우스를 가져가서 타원을 선택한 다음 **리소스 검색을**선택합니다.

   - 명령 팔레트:

     메뉴 모음에서 **명령 팔레트** **보기로** > 이동하여 **리소스: 리소스 검색**을 입력합니다.

1. 표시를 위해 두 개 이상의 구독을 선택한 경우 필터를 사용하여 검색할 구독을 선택합니다.

1. 필터를 사용하여 이전에 선택한 구독의 일부인 검색할 리소스 그룹을 선택합니다.

1. 필터를 사용하여 표시할 리소스를 선택합니다. 필터는 리소스 이름과 리소스 유형 모두에 대해 작동합니다.

## <a name="discover-aliases-for-resource-properties"></a>리소스 속성에 대한 별칭 검색

리소스를 선택 하면 검색 인터페이스를 통해 또는 treeview에서 선택 하 여 Azure Policy 확장자는 해당 리소스 및 모든 리소스 관리자 속성 값을 나타내는 JSON 파일을 엽니다.

리소스가 열리면 Resource Manager 속성 이름 또는 값 위로 마우스를 가져가면 Azure Policy 별칭이 있는 경우 표시됩니다. 이 예제에서는 리소스가 `Microsoft.Compute/virtualMachines` 리소스 유형이며 **property.storageProfile.imageReference.offer** 속성이 위에 마우스를 가져갑니다. 호버링에는 일치하는 별칭이 표시됩니다.

![Azure 정책 확장 마우스로 리소스 관리자 속성 별칭을 표시 합니다.](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>정책 및 과제 검색 및 보기

Azure 정책 확장은 정책 창에 표시하도록 선택한 구독에 대한 트리뷰로 **정책** 유형 및 정책 할당을 나열합니다. 단일 구독에서 수백 또는 수천 개의 정책 또는 할당을 보유한 고객은 정책 또는 할당을 검색할 수 있는 방법을 선호할 수 있습니다. Azure 정책 확장을 사용하면 다음 단계를 통해 특정 정책 또는 할당을 검색할 수 있습니다.

1. Azure 정책 확장 또는 명령 팔레트에서 검색 인터페이스를 시작합니다.

   - Azure 정책 확장

     Azure 정책 확장에서 **정책** 패널 위로 마우스를 가져가서 타원을 선택한 다음 **정책 검색을**선택합니다.

   - 명령 팔레트:

     메뉴 모음에서 **명령 팔레트** **보기로** > 이동하여 **정책: 정책 검색을**입력합니다.

1. 표시를 위해 두 개 이상의 구독을 선택한 경우 필터를 사용하여 검색할 구독을 선택합니다.

1. 필터를 사용하여 이전에 선택한 구독의 일부인 검색할 정책 유형 또는 할당을 선택합니다.

1. 필터를 사용하여 어떤 정책을 선택하거나 표시할지 선택합니다. 필터는 정책 정의 또는 정책 할당에 대한 _displayName에_ 대해 작동합니다.

정책 또는 할당을 선택할 때 검색 인터페이스를 통해 또는 treeview에서 선택 하여 Azure Policy 확장 정책 또는 할당 및 모든 리소스 관리자 속성 값을 나타내는 JSON을 엽니다. 확장은 열린 Azure 정책 JSON 스키마의 유효성을 검사할 수 있습니다.

## <a name="sign-out"></a>로그아웃

메뉴 모음에서**명령 팔레트** **보기로** > 이동한 다음 **Azure: 로그아웃**을 입력합니다.

## <a name="next-steps"></a>다음 단계

- Azure 정책 [샘플의 예제를 검토합니다.](../samples/index.md)
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 이해합니다.
- [비준수 리소스를 수정하는](remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)