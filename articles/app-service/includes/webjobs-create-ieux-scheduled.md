---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ef0aa8ba1983ca30fd44c27fe570b6b5f51733a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745669"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>예약된 WebJob 만들기


1. [Azure Portal](https://portal.azure.com)를 가져왔습니다.
1. **App Service** 로 이동 합니다. <abbr title="앱 리소스는 웹 앱, API 앱 또는 모바일 앱 일 수 있습니다.">앱 리소스</abbr>.
1. **WebJobs** 를 선택합니다.

   ![WebJobs 선택](../media/web-sites-create-web-jobs/select-webjobs.png)

1. **WebJobs** 페이지에서 **추가** 를 선택합니다.

    ![WebJob 페이지](../media/web-sites-create-web-jobs/wjblade.png)

1. 표에 지정된 대로 **WebJob 추가** 설정을 사용합니다.

    ![WebJob 페이지 추가](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | 설정      | 샘플 값   |
    | ------------ | ----------------- | 
    | <abbr title="App Service 앱 내에서 고유한 이름입니다. 는 문자 또는 숫자로 시작 해야 하며 및 이외의 특수 문자를 포함할 수 없습니다 `-` `_` .">속성</a> | myScheduledWebJob |  |
    | <abbr title="실행 파일 또는 스크립트 파일뿐만 아니라 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되는 *.zip* 파일입니다.">파일 업로드</abbr> | ConsoleApp.zip |
    | <abbr title="연속, 트리거된 형식이 포함 됩니다.">Type</abbr> | 트리거 |
    | <abbr title="안정적으로 실행되도록 예약하기 위해 Always On 기능을 사용하도록 설정합니다. Always On은 기본, 표준 및 프리미엄 가격 책정 계층에서만 사용할 수 있습니다.">트리거</a> | 예약됨 |
    | CRON 식</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>CRON 식에 대 한 자세한 정보</summary>
     <a name="#ncrontab-expressions"></a>
    
     다음 예제와 같이 포털에 [NCRONTAB 식을](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) 입력 하거나 `settings.job` WebJob *.zip* 파일의 루트에 파일을 포함할 수 있습니다.
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     자세히 알아보려면 [트리거된 WebJob 예약](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)을 참조 하세요.
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. **확인** 을 클릭합니다.

    새 WebJob이 **WebJobs** 페이지에 표시됩니다.
    
    ![WebJobs 목록](../media/web-sites-create-web-jobs/listallwebjobs.png)
