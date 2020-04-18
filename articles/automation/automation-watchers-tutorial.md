---
title: Azure Automation 계정에서 감시자 작업 만들기
description: 폴더에 만들어진 새 파일을 감시하도록 Azure Automation 계정에 감시자 작업을 만드는 방법을 알아봅니다.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617361"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>로컬 컴퓨터에서 파일 변경 내용을 추적하는 Azure Automation 감시자 태스크 만들기

Azure Automation은 감시자 작업을 사용하여 이벤트를 찾고 PowerShell Runbook을 사용하여 작업을 트리거합니다. 감시자 작업에는 감시자와 작업의 두 부분이 포함되어 있습니다. 감시자 Runbook은 감시자 작업에 정의된 간격으로 실행되고 데이터를 작업 런북에 출력합니다. 

이 자습서에서는 새 파일이 디렉터리에 추가되는 시기를 모니터링하기 위한 감시자 태스크를 만드는 과정을 안내합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 감시자 Runbook 가져오기
> * Automation 변수 만들기
> * 작업 Runbook 만들기
> * 감시자 태스크 만들기
> * 감시자 트리거
> * 출력 검사

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 감시자 및 작업 Runbook과 감시자 태스크를 포함하는 [Automation 계정](automation-offering-get-started.md)
* 감시자 작업이 실행되는 [하이브리드 Runbook 작업자](automation-hybrid-runbook-worker.md)
* 파워쉘 런북. PowerShell 워크플로 런북은 감시자 작업에서 지원되지 않습니다.

> [!NOTE]
> 감시자 작업은 Azure 중국에서 지원되지 않습니다.

## <a name="import-a-watcher-runbook"></a>감시자 Runbook 가져오기

이 자습서에서는 **Watch-NewFile**이라는 감시자 Runbook을 사용하여 디렉터리에서 새 파일을 찾습니다. 감시자 Runbook은 폴더의 파일에 마지막으로 쓴 것으로 알려진 시간을 검색하고 해당 워터마크보다 최근 버전의 파일을 찾습니다.

이 가져오기 프로세스는 [PowerShell 갤러리를](https://www.powershellgallery.com)통해 수행할 수 있습니다.

1. [Watch-NewFile.ps1에](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)대한 갤러리 페이지로 이동합니다.
2. Azure **자동화** 탭에서 **Azure 자동화에 배포를 클릭합니다.**

다음 단계를 사용하여 이 Runbook을 포털에서 자동화 계정으로 가져올 수도 있습니다.

1. Automation 계정을 열고 Runbook 페이지를 클릭합니다.
2. **갤러리 찾아보기를 클릭합니다.**
3. **Watcher runbook을**검색하고 **디렉터리에서 새 파일을 찾는 Watcher runbook을**선택하고 **가져오기를**클릭합니다.
  ![UI에서 Automation Runbook 가져오기](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Runbook에 이름과 설명을 지정하고 **확인을** 클릭하여 Runbook을 자동화 계정으로 가져옵니다.
5. **편집**을 선택하고 **게시**를 클릭합니다. 메시지가 표시되면 **예를** 클릭하여 Runbook을 게시합니다.

## <a name="create-an-automation-variable"></a>Automation 변수 만들기

[Automation 변수](automation-variables.md)는 위의 Runbook이 각 파일에서 읽고 저장하는 타임스탬프를 저장하는 데 사용됩니다.

1. **공유 리소스** 에서 **변수를** 선택하고 **+ 변수 추가를 클릭합니다.**
1. 이름에 대한 Watch-NewFile타임스탬프를 입력합니다.
1. 형식의 날짜 시간을 선택합니다.
1. **만들기를** 클릭하여 자동화 변수를 만듭니다.

## <a name="create-an-action-runbook"></a>작업 Runbook 만들기

작업 Runbook은 감시자 Runbook에서 전달된 데이터에 대한 작업을 수행하기 위해 감시자 태스크에서 사용됩니다. [PowerShell 갤러리에서](https://www.powershellgallery.com) **Process-NewFile이라는** 미리 정의된 작업 런북을 가져와야 합니다. 

작업 실행책을 만들려면 다음을 수행합니다.

1. [프로세스-NewFile.ps1에](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)대 한 갤러리 페이지로 이동 합니다.
2. Azure **자동화** 탭에서 **Azure 자동화에 배포를 클릭합니다.**

이 Runbook을 Azure 포털에서 자동화 계정으로 가져올 수도 있습니다.

1. 자동화 계정으로 이동하여 프로세스 **자동화**에서 **Runbook을** 선택합니다.
1. **갤러리 찾아보기를 클릭합니다.**
1. **감시자 작업**검색, **감시자 Runbook에서 트리거된 이벤트를 처리하는 감시자 작업을**선택하고 **가져오기를**클릭합니다.
  ![UI에서 작업 Runbook 가져오기](media/automation-watchers-tutorial/importsourceaction.png)
1. Runbook에 이름과 설명을 지정하고 **확인을** 클릭하여 Runbook을 자동화 계정으로 가져옵니다.
1. **편집**을 선택하고 **게시**를 클릭합니다. 메시지가 표시되면 **예를** 클릭하여 Runbook을 게시합니다.

## <a name="create-a-watcher-task"></a>감시자 태스크 만들기

이 단계에서는 이전 섹션에 정의된 감시자 및 작업 실행책을 참조하는 감시자 작업을 구성합니다.

1. 자동화 계정으로 이동하여 **프로세스 자동화**에서 **감시자 작업을** 선택합니다.
1. 감시자 작업 페이지를 선택하고 **감시자 작업 추가를**클릭합니다.
1. **이름으로 WatchMyFolder를 입력합니다.**

1. **감시자 구성을** 선택하고 **Watch-NewFile** 런북을 선택합니다.

1. 매개 변수에 대해 다음 값을 입력합니다.

   * **폴더경로** - 새 파일이 생성되는 하이브리드 Runbook 작업자의 폴더(예: **d:\예제 파일)**
   * **확장** - 구성에 대한 확장입니다. 모든 파일 확장명을 처리하려면 비워 둡니다.
   * **RECURSE** - 재귀 작업. 이 값을 기본값으로 둡니다.
   * **설정 실행** - Runbook 실행을 위한 설정입니다. 하이브리드 작업자를 선택합니다.

1. **확인을**클릭한 다음 **선택하여** 감시자 페이지로 돌아갑니다.
1. **작업 구성을** 선택하고 **프로세스-NewFile** 실행책을 선택합니다.
1. 매개 변수에 대해 다음 값을 입력합니다.

   * **이벤트 데이터** - 이벤트 데이터. 비워 둡니다. 데이터는 감시자 Runbook에서 제공됩니다.
   * **설정 실행** - Runbook 실행을 위한 설정입니다. 이 실행책은 Azure 자동화에서 실행될 때 Azure로 둡니다.

1. **확인을**클릭한 다음 **선택하여** 감시자 페이지로 돌아갑니다.
1. **확인을** 클릭하여 감시자 작업을 만듭니다.

![UI에서 감시자 작업 구성](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>감시자 트리거

감시자 작업이 예상대로 작동하는지 확인하려면 아래에 설명된 대로 테스트를 실행해야 합니다. 

1. 하이브리드 Runbook 작업자로 원격. 
2. **PowerShell**을 열고 폴더에 테스트 파일을 만듭니다.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

다음 예제에는 테스트가 정상적으로 완료된 경우의 결과가 나와 있습니다.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>출력 검사

1. 자동화 계정으로 이동하여 **프로세스 자동화**에서 **감시자 작업을** 선택합니다.
1. 감시자 작업 **WatchMyFolder를**선택합니다.
1. **스트림** 에서 **감시자 스트림 보기를** 클릭하면 감시자가 새 파일을 발견하고 작업 런북을 시작했는지 확인합니다.
1. 작업 Runbook 작업을 보려면 **감시자 작업 보기 작업을**클릭합니다. 각 작업을 선택하여 작업의 세부 정보를 볼 수 있습니다.

   ![UI의 감시자 작업](media/automation-watchers-tutorial/WatcherActionJobs.png)

다음 예에는 새 파일을 찾은 경우 표시되는 출력이 나와 있습니다.

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 감시자 Runbook 가져오기
> * Automation 변수 만들기
> * 작업 Runbook 만들기
> * 감시자 태스크 만들기
> * 감시자 트리거
> * 출력 검사

Runbook을 직접 작성하는 방법을 자세히 알아보려면 아래 링크로 이동하세요.

> [!div class="nextstepaction"]
> [내 첫 번째 파워 쉘 실행 책](automation-first-runbook-textual-powershell.md).

