---
title: 비주얼 스튜디오 코드를 사용하여 템플릿 만들기
description: Visual Studio 코드 및 Azure 리소스 관리자 도구 확장을 설치하고 사용하는 방법에 대해 알아봅니다.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273632"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>시각적 스튜디오 코드를 사용하여 Azure 리소스 관리자 템플릿 만들기

비주얼 스튜디오 코드는 경량, 멀티 플랫폼, 오픈 소스 편집기입니다. Azure 리소스 관리자 템플릿 도구 확장은 리소스 관리자 템플릿 개발을 위한 플러그인입니다. 확장은 intellisense, 구문 강조 표시, 인라인 도움말 및 기타 여러 언어 함수를 제공하기 위해 템플릿에 대한 언어 지원을 추가합니다. 함께, 그들은 권장된 템플릿 개발 경험을 제공 합니다.

## <a name="install-visual-studio-code"></a>Visual Studio Code 설치

비주얼 스튜디오 코드는 맥 OS, 윈도우, 리눅스를 지원합니다.  [그것은 시각적 스튜디오 코드에서](https://code.visualstudio.com/)설치할 수 있습니다.

## <a name="install-resource-manager-tools-extension"></a>리소스 관리자 도구 확장 설치

1. Visual Studio Code를 엽니다.
1. 왼쪽 메뉴에서 **확장을 선택합니다.** 또는 **보기** 메뉴에서 **확장을** 선택하여 확장 창을 엽니다.

    ![비주얼 스튜디오 코드 리소스 관리자 도구 확장 설치](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. 리소스 **관리자를**검색합니다.
1. **Azure 리소스 관리자 도구에서 설치를**선택합니다. **Install**

## <a name="the-extension-features"></a>확장 기능

### <a name="colorization-for-template-language-expressions"></a>템플릿 언어 표현식에 대한 색상 지정

매개 변수, 변수, 함수, 이름 및 식은 다음 스크린샷과 같이 색상으로 구분됩니다.

![비주얼 스튜디오 코드 리소스 관리자 도구 확장 색상 지정](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

템플릿 개요 보기를 사용하면 큰 템플릿을 쉽게 탐색할 수 있습니다.

### <a name="intellisense"></a>Intellisense.

리소스 관리자 템플릿 확장은 함수 이름, 매개 변수, 변수 및 참조에 대한 가능한 완료를 알고 있습니다. 입력할 때 IntelliSense 제안이 나타납니다. 문자를 계속 입력하면 멤버 목록(변수, 메서드 등)이 입력된 문자를 포함하는 멤버만 포함하도록 필터링됩니다. **탭을** 누르거나 **입력을** 누르면 선택한 멤버가 삽입됩니다.

- 기본 제공 함수 이름

    ![비주얼 스튜디오 코드 리소스 관리자 도구 확장 intellisense 함수](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- 매개 변수 참조

    ![비주얼 스튜디오 코드 리소스 관리자 도구 확장 intellisense 매개 변수](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- 변수 참조

    ![비주얼 스튜디오 코드 리소스 관리자 도구 확장 intellisense 변수](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- 리소스 그룹() 속성

    ![비주얼 스튜디오 코드 리소스 관리자 도구 확장 intellisense 함수](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

또한 intellisense는 구독() 속성 및 개체인 변수에 대한 참조 속성에서도 작동합니다.

### <a name="signature-help-for-function-parameters"></a>함수 매개 변수에 대한 서명 도움말

함수 이름 위로 마우스를 가져가면 확장에 함수 매개 변수에 대한 서명 도움말이 표시됩니다.

![비주얼 스튜디오 코드 리소스 관리자 도구 확장 서명 기능](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>변수 및 매개변수 참조에 대해 정의로 이동

**Ctrl+Click**을 사용하여 정의로 이동하거나 스크린샷에 ![표시된 컨텍스트 메뉴를 사용하여 시각적 스튜디오 코드 리소스 관리자 도구 확장이 정의로 이동하면 됩니다.](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

**Ctrl+Alt+Click을**사용하여 정의를 측면으로 열 수 있습니다.

### <a name="peek-for-variable-and-parameter-definitions"></a>변수 및 매개변수 정의 엿보기

엿보는 편집기를 열려면 이전 스크린샷과 같이 컨텍스트 메뉴를 사용합니다.

다음 스크린샷은 픽 편집기입니다.

![비주얼 스튜디오 코드 리소스 관리자 도구 확장 엿보기 편집기](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>변수 및 매개 변수에 대한 모든 참조 찾기

모든 참조를 찾으려면 이전 스크린샷과 같이 컨텍스트 메뉴를 사용합니다.

다음 스크린샷은 참조가 강조 표시되는 방식을 보여 주며 다음과 같습니다.

![비주얼 스튜디오 코드 리소스 관리자 도구 확장 모든 참조를 찾을 수 있습니다.](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>변수 및 매개 변수에 대한 모든 참조 의 이름 바꾸기

변수 및 매개 변수에 대한 모든 참조의 이름을 바꾸려면 이전 스크린샷과 같이 컨텍스트 메뉴를 사용합니다.

### <a name="hover-for-parameter-description"></a>매개변수 설명을 위해 마우스를 가져가기

![비주얼 스튜디오 코드 리소스 관리자 도구 확장 호버 정의](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>중괄호 일치

일치하는 대괄호는 커서가 커서 중 하나 근처에 있는 즉시 강조 표시됩니다. 중괄호를 클릭하면 다음 스크린샷과 같이 일치하는 중괄호도 강조 표시됩니다.

![비주얼 스튜디오 코드 리소스 관리자 도구 확장 중괄호 일치](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>오류 및 경고 표시

확장에 의해 식별되는 오류는 다음과 같습니다.

- 정의되지 않은 매개 변수 참조
- 정의되지 않은 변수 참조
- 인식할 수 없는 함수 이름
- 참조() 가변 정의의 함수 사용
- 함수의 잘못된 인수 수

경고는 다음과 같습니다.

- 사용되지 않는 매개 변수
- 사용되지 않는 변수

## <a name="next-steps"></a>다음 단계

- Azure 리소스 관리자 템플릿을 만드는 방법에 대해 알아보려면 [자습서: 첫 번째 Azure 리소스 관리자 템플릿 만들기 및 배포를](template-tutorial-create-first-template.md)참조하세요.
- Visual Studio 코드를 사용하여 빠른 시작을 진행하려면 [빠른 시작: Visual Studio 코드를 사용하여 Azure 리소스 관리자 템플릿 만들기를](quickstart-create-templates-use-visual-studio-code.md) 참조하십시오.
