---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842192"
---
## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

이 섹션에서는 Azure 구독에서 함수 앱 및 관련 리소스를 만든 다음, 코드를 배포합니다. 

1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **함수 앱에 배포...** 단추를 선택합니다.

    ![Azure에 프로젝트 게시](media/functions-publish-project-vscode/function-app-publish-project.png)

1. 프롬프트에서 다음 정보를 제공합니다.

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | prompt | 값 | Description |
    | ------ | ----- | ----- |
    | 구독 선택 | 사용자의 구독 | 구독이 여러 개인 경우 표시됩니다. |
    | Azure에서 함수 앱 선택 | + 새 함수 앱 만들기 | 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다. |
    | 함수 앱에 대한 전역 고유 이름을 입력합니다. | 고유 이름 | 함수 앱 이름에 대한 유효한 문자는 `a-z`, `0-9` 및 `-`입니다. |
    | 새 리소스의 위치 선택 | 지역 | 근처 [지역](https://azure.microsoft.com/regions/)을 선택하세요. | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | prompt | 값 | Description |
    | ------ | ----- | ----- |
    | 구독 선택 | 사용자의 구독 | 구독이 여러 개인 경우 표시됩니다. |
    | Azure에서 함수 앱 선택 | + 새 함수 앱 만들기 | 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다. |
    | 함수 앱에 대한 전역적으로 고유 이름을 입력합니다. | 고유 이름 | 함수 앱 이름에 대한 유효한 문자는 `a-z`, `0-9` 및 `-`입니다. |
    | 런타임 선택 | 사용자 버전 | 로컬에서 실행 중인 언어 버전을 선택합니다. |
    | 새 리소스의 위치 선택 | 지역 | 근처 [지역](https://azure.microsoft.com/regions/)을 선택하세요. | 

    ::: zone-end

    
1.  완료되면 구독에 다음과 같은 Azure 리소스가 생성됩니다.

    + **[리소스 그룹](../articles/azure-resource-manager/management/overview.md)** : 만든 Azure 리소스를 모두 포함합니다. 이름은 함수 앱 이름을 기반으로 합니다.
    + **[스토리지 계정](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : 표준 스토리지 계정은 함수 앱 이름을 기반으로 하는 고유한 이름으로 만들어집니다.
    + **[호스팅 계획](../articles/azure-functions/functions-scale.md)** : 소비 계획은 서버리스 함수 앱을 호스트할 미국 서부 지역에 생성됩니다.
    + **함수 앱**: 프로젝트가 이 새 함수 앱에 배포되고 실행됩니다.
    + **[Application Insights]()** : 함수 앱에 연결된 인스턴스는 함수 이름에 따라 만들어집니다.

    함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다. 
    
1. 이 알림에서 **출력 보기**를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다.

    ![전체 알림 만들기](media/functions-publish-project-vscode/function-create-notifications.png)

1. **Azure: Functions** 영역으로 돌아가서 구독의 새 함수 앱을 확장합니다. **Functions**를 확장하고 **HttpExample**에서 (Windows) 또는 Ctrl + 클릭(MacOS)을 마우스 오른쪽 단추로 클릭한 다음, **함수 URL 복사**를 선택합니다.

    ![새 HTTP 트리거에 대한 함수 URL 복사](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
