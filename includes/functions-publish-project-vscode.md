---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 41dfb809cdab00f4f9bee335d92522f37a438c68
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82109536"
---
## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

이 섹션에서는 Azure 구독에서 함수 앱 및 관련 리소스를 만든 다음, 코드를 배포합니다. 

> [!IMPORTANT]
> 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다. 


1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **함수 앱에 배포...** 단추를 선택합니다.

    ![Azure에 프로젝트 게시](media/functions-publish-project-vscode/function-app-publish-project.png)

1. 프롬프트에서 다음 정보를 제공합니다.

    + **폴더 선택**: 작업 영역에서 폴더를 선택하거나 함수 앱을 포함하는 폴더를 찾습니다. 유효한 함수 앱이 이미 열려 있는 경우에는 이 메시지가 표시되지 않습니다.

    + **구독 선택**: 사용할 구독을 선택합니다. 구독이 한 개만 있으면 이 메시지가 표시되지 않습니다.

    + **Azure에서 함수 앱 선택**: `+ Create new Function App`을 선택합니다. (이 문서에서 다루지 않는 `Advanced` 옵션은 선택하지 마세요.)
      
    + **함수 앱에 대해 전역적으로 고유 이름을 입력합니다**. URL 경로에 유효한 이름을 입력합니다. 입력한 이름이 Azure Functions에서 고유한지 확인하기 위해 유효성을 검사합니다. 
    
    ::: zone pivot="programming-language-python"
    + **런타임 선택**: 로컬에서 실행 중인 Python 버전을 선택합니다. `python --version` 명령을 사용하여 버전을 확인할 수 있습니다.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **런타임 선택**: 로컬에서 실행 중인 Node.js 버전을 선택합니다. `node --version` 명령을 사용하여 버전을 확인할 수 있습니다.
    ::: zone-end

    + **새 리소스의 위치 선택**:  성능을 향상시키려면 근처에 있는 [지역](https://azure.microsoft.com/regions/)을 선택합니다. 
    
1.  완료되면 함수 앱 이름을 기반으로 하는 이름을 사용하여 구독에 다음 Azure 리소스가 생성됩니다.
    
    + 리소스 그룹 - 관련 리소스에 대한 논리 컨테이너입니다.
    + 표준 Azure Storage 계정 - 프로젝트에 대한 상태 및 기타 정보를 유지 관리합니다.
    + 소비 계획 - 서버리스 함수 앱의 기본 호스트를 정의합니다. 
    + 함수 앱 - 함수 코드를 실행할 수 있는 환경을 제공합니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 동일한 호스팅 계획 내에서 더 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다.
    + 서버리스 함수의 사용을 추적하는 함수 앱에 연결된 Application Insights 인스턴스입니다.

    함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다. 
    
1. 이 알림에서 **출력 보기**를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다. 알림이 누락된 경우 오른쪽 아래 모서리에 있는 종 모양 아이콘을 선택하여 다시 확인합니다.

    ![전체 알림 만들기](media/functions-publish-project-vscode/function-create-notifications.png)
