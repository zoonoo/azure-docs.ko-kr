---
title: Visual Studio Code를 사용 하 여 템플릿 만들기
description: Visual Studio Code 및 Azure Resource Manager 도구 확장을 설치 하 고 사용 하는 방법에 대해 알아봅니다.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 3f0eaf44a536cfb54796744068fee8a70135597a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149382"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Visual Studio Code를 사용 하 여 Azure Resource Manager 템플릿 만들기

Visual Studio Code는 경량 다중 플랫폼 오픈 소스 편집기입니다. Azure Resource Manager 템플릿 도구 확장은 리소스 관리자 템플릿 개발용 플러그 인입니다. 확장은 intellisense, 구문 강조 표시, 온라인 도움말 및 기타 많은 언어 함수를 제공 하기 위해 템플릿에 대 한 언어 지원을 추가 합니다. 권장 되는 템플릿 개발 환경을 제공 합니다.

## <a name="install-visual-studio-code"></a>Visual Studio Code 설치

Visual studio Code는 MacOS, Windows 및 Linux를 지원 합니다.  [Visual Studio Code](https://code.visualstudio.com/)에서 설치할 수 있습니다.

## <a name="install-resource-manager-tools-extension"></a>리소스 관리자 도구 확장 설치

1. Visual Studio Code를 엽니다.
1. 왼쪽 메뉴에서 **확장** 을 선택 합니다. 또는 **보기** 메뉴에서 **확장** 을 선택 하 여 확장 창을 엽니다.

    ![Visual Studio Code 리소스 관리자 도구 확장 설치](./media/resource-manager-tools-vs-code/resource-manager-visual-studio-code-tools-extension.png)
1. **리소스 관리자**를 검색 합니다.
1. **Azure Resource Manager 도구**에서 **설치** 를 선택 합니다.

## <a name="the-extension-features"></a>확장 기능

### <a name="colorization-for-template-language-expressions"></a>템플릿 언어 식에 대 한 색 지정

다음 스크린샷에 표시 된 것 처럼 매개 변수, 변수, 함수, 이름 및 식이 색으로 구분 됩니다.

![Visual Studio Code 리소스 관리자 도구 확장 색 지정](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-colorization.png)

템플릿 개요 뷰를 사용 하면 많은 템플릿을 쉽게 탐색할 수 있습니다.

### <a name="intellisense"></a>메서드가

리소스 관리자 템플릿 확장은 함수 이름, 매개 변수, 변수 및 참조에 대 한 가능한 완료를 알고 있습니다. 입력 하면 IntelliSense 제안 팝업이 표시 됩니다. 문자를 계속 입력 하면 멤버 목록 (변수, 메서드 등)이 필터링 되어 입력 된 문자를 포함 하는 멤버만 포함 됩니다. **Tab** 또는 **enter** 키를 누르면 선택한 멤버가 삽입 됩니다.

- 기본 제공 함수 이름

    ![Visual Studio Code 리소스 관리자 도구 확장 intellisense 함수](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-functions.png)

- 매개 변수 참조

    ![Visual Studio Code 리소스 관리자 도구 확장 intellisense 매개 변수](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-parameters.png)

- 변수 참조

    ![Visual Studio Code 리소스 관리자 도구 확장 intellisense 변수](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroup () 속성

    ![Visual Studio Code 리소스 관리자 도구 확장 intellisense 함수](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-resourcegroup.png)

또한 intellisense는 subscription () 속성과 개체 인 변수에 대 한 참조의 속성을 사용 합니다.

### <a name="signature-help-for-function-parameters"></a>함수 매개 변수에 대 한 시그니처 도움말

함수 이름을 가리키면 확장에 함수 매개 변수에 대 한 서명 도움말이 표시 됩니다.

![Visual Studio Code 리소스 관리자 도구 확장 서명 함수](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>변수 및 매개 변수 참조에 대 한 정의로 이동

**Ctrl + 클릭**을 사용 하 여 정의로 이동 하거나 스크린샷에 표시 된 상황에 맞는 메뉴를 사용 하 여 (![Visual Studio Code 리소스 관리자 도구 확장 정의로 이동)를 사용할 수 있습니다](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-context-menu.png)

**Ctrl + Alt + 클릭**을 사용 하 여 정의를 왼쪽으로 열 수 있습니다.

### <a name="peek-for-variable-and-parameter-definitions"></a>변수 및 매개 변수 정의 피킹

피킹 editor를 열려면 이전 스크린샷에서와 같이 상황에 맞는 메뉴를 사용 합니다.

다음 스크린샷은 피킹 (peeking) 편집기를 보여 줍니다.

![Visual Studio Code 리소스 관리자 도구 확장 피킹 편집기](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>변수 및 매개 변수에 대 한 모든 참조 찾기

모든 참조를 찾으려면 이전 스크린샷에서와 같이 상황에 맞는 메뉴를 사용 합니다.

다음 스크린샷은 참조를 강조 표시 하는 방법을 보여 줍니다.

![Visual Studio Code 리소스 관리자 도구 확장 모든 참조 찾기](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>변수 및 매개 변수에 대 한 모든 참조 이름 바꾸기

변수 및 매개 변수에 대 한 모든 참조의 이름을 바꾸려면 이전 스크린샷에 표시 된 대로 상황에 맞는 메뉴를 사용 합니다.

### <a name="hover-for-parameter-description"></a>매개 변수 설명 가리키기

![Visual Studio Code 리소스 관리자 도구 확장 가리키기 정의](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>중괄호 일치

짝이 되는 대괄호는 커서가 그 근처에 있는 즉시 강조 표시 됩니다. 중괄호를 클릭 하면 다음 스크린샷에 표시 된 것 처럼 짝이 되는 중괄호도 강조 표시 됩니다.

![Visual Studio Code 리소스 관리자 도구 확장 중괄호 일치](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>오류 및 경고 표시

확장에 의해 식별 되는 오류는 다음과 같습니다.

- 정의 되지 않은 매개 변수 참조
- 정의 되지 않은 변수 참조
- 인식할 수 없는 함수 이름
- 변수 정의에서 reference () 함수 사용
- 함수의 인수 개수가 잘못 되었습니다.

경고는 다음과 같습니다.

- 사용 하지 않는 매개 변수
- 사용 하지 않는 변수

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager 템플릿을 만드는 방법에 대 한 자세한 내용은 [자습서: 첫 번째 Azure Resource Manager 템플릿 만들기 및 배포](template-tutorial-create-first-template.md)를 참조 하세요.
- Visual Studio Code를 사용 하 여 빠른 시작을 진행 하려면 [빠른 시작:를 사용 하 여 Azure Resource Manager 템플릿 만들기](./resource-manager-quickstart-create-templates-use-visual-studio-code.md) 를 참조 하세요 Visual Studio Code
