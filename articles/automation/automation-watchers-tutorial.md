---
title: "Azure Automation 계정에서 감시자 작업 만들기 | Microsoft Docs"
description: "폴더에 만들어진 새 파일을 감시하도록 Azure Automation 계정에 감시자 작업을 만드는 방법을 알아봅니다."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Azure Automation 감시자 작업을 통해 로컬 데이터 센터에서 발생하는 이벤트에 대응할 수 있습니다.

이 자습서에서는 다음을 수행하는 새 감시자 작업을 만드는 방법을 알아봅니다.

> [!div class="checklist"]
> * 디렉터리에서 새 파일을 검색하는 감시자 Runbook을 만듭니다.
> * 파일이 감시자에서 마지막으로 처리된 시간을 유지하는 Automation 변수를 만듭니다.
> * 감시자 Runbook이 새 파일을 찾을 때 호출되는 작업 Runbook을 만듭니다.
> * 감시자 Runbook 및 작업 Runbook을 선택하는 감시자 작업을 만듭니다.
> * 디렉터리에 새 파일을 추가하여 감시자를 트리거합니다.
> * 새 파일에 정보를 표시하는 작업 Runbook에서 출력을 검사합니다.  

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.
+ 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
+ 감시자 및 작업 Runbook과 감시자 태스크를 포함하는 [Automation 계정](automation-offering-get-started.md)
+ 감시자 작업이 실행되는 [하이브리드 Runbook 작업자](automation-hybrid-runbook-worker.md)

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>새 파일을 찾는 감시자 Runbook 만들기
1.  Automation 계정을 열고 Runbook 페이지를 클릭합니다.
2.  "갤러리 찾아보기" 단추를 클릭합니다.
![UI의 Runbook 목록](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  “Watch-NewFile”을 검색하고 Runbook을 Automation 계정으로 가져옵니다.
![UI에서 Runbook 게시](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  "편집"을 클릭하여 Runbook 원본을 보고 "게시" 단추를 클릭합니다.

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>파일이 감시자에서 마지막으로 처리된 시간을 유지하는 Automation 변수 만들기
1.  공유 리소스에서 변수 페이지를 열고 “변수 추가"를 클릭합니다. ![UI의 변수 나열](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  이름으로 "Watch-NewFileTimestamp" 입력
3.  형식으로 DateTime을 선택하고 "만들기" 단추를 클릭합니다.
![UI에서 워터마크 변수 만들기](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>감시자 Runbook이 새 파일을 찾을 때 호출되는 작업 Runbook을 만들기
1.  Runbook 페이지에서 "프로세스 Automation" 범주 아래를 클릭합니다.
2.  "갤러리 찾아보기" 단추를 클릭합니다.
3.  "Process-NewFile"을 검색하고 Runbook을 Automation 계정으로 가져옵니다.
4.  "편집"을 클릭하여 Runbook 원본을 보고 "게시" 단추를 클릭합니다.
![UI의 프로세스 감시자](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>감시자 Runbook 및 작업 Runbook을 선택하는 감시자 작업 만들기
1.  감시자 태스크 페이지를 열고 "감시자 태스크 추가" 단추를 클릭합니다.
![UI의 감시자 목록](media/automation-watchers-tutorial/WatchersList.png)
2.  이름으로 "WatchMyFolder"를 입력합니다.
3.  "감시자 구성"을 선택하고 "Watch-NewFile" Runbook을 선택합니다.
![UI에서 감시자 구성](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  매개 변수에 대해 다음 값을 입력합니다.
    *   FOLDERPATH. 새 파일을 만들 하이브리드 작업자의 폴더입니다.
    *   EXTENSION. 모든 파일 확장명을 처리하려면 비워 둡니다.
    *   RECURSE. 기본값을 그대로 둡니다.
    *   실행 설정. 하이브리드 작업자를 선택합니다.
5.  확인을 클릭하고 선택을 클릭하여 감시자 페이지로 돌아갑니다.
6.  "작업 구성"을 선택하고 "Process-NewFile" Runbook을 선택합니다.
![UI에서 감시자 작업 구성](media/automation-watchers-tutorial/ConfigureAction.png)
7.  매개 변수에 대해 다음 값을 입력합니다.
    *   EVENTDATA. 비워 둡니다. 데이터는 감시자 Runbook에서 제공됩니다.
    *   실행 설정. 이 Runbook이 Automation 서비스에서 실행되므로 Azure로 둡니다.
8.  확인을 클릭하고 선택을 클릭하여 감시자 페이지로 돌아갑니다.
9.  확인을 클릭하여 감시자 태스크를 만듭니다.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>디렉터리에 새 파일을 추가하여 감시자 트리거
1.  하이브리드 작업자에 연결으로 연결합니다.
2.  감시자 태스크에 의해 모니터링되는 폴더에 새 텍스트 파일을 추가합니다.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>새 파일에 정보를 표시하는 작업 Runbook에서 출력 검사
1.  "WatchMyFolder"에 대한 감시자 태스크를 클릭합니다.
2.  "감시자 스트림 보기"를 클릭하여 감시자가 새 파일을 찾았으며 작업 Runbook을 시작했음을 확인합니다.
3.  "감시자 작업 보기"를 클릭하여 작업 Runbook 작업을 확인합니다.
![UI의 감시자 작업](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>다음 단계:

자세한 내용은 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.








