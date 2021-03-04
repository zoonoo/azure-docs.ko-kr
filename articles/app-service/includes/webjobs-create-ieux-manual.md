---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5687fb99c27b8b2141e0a2a817327cfbb124951a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109342"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>수동으로 트리거된 WebJob 만들기

1. [Azure Portal](https://portal.azure.com)를 가져왔습니다.
1. **App Service** 로 이동 합니다. <abbr title="앱 리소스는 웹 앱, API 앱 또는 모바일 앱 일 수 있습니다.">앱 리소스</abbr>.
1. **WebJobs** 를 선택합니다.

    ![WebJobs 선택](../media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** 페이지에서 **추가** 를 선택합니다.

   ![WebJob 페이지](../media/web-sites-create-web-jobs/wjblade.png)

3. 표에 지정된 대로 **WebJob 추가** 설정을 사용합니다.

    ![수동으로 트리거된 WebJob을 만들기 위해 설정 해야 하는 설정을 보여 주는 스크린샷](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | 설정      | 샘플 값   | 
    | ------------ | ----------------- | 
   | <abbr title="App Service 앱 내에서 고유한 이름입니다. 는 문자 또는 숫자로 시작 해야 하며 및 이외의 특수 문자를 포함할 수 없습니다 `-` `_` .">속성</abbr> | myTriggeredWebJob | 
    | <abbr title="실행 파일 또는 스크립트 파일뿐만 아니라 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되는 *.zip* 파일입니다.">파일 업로드</abbr> | ConsoleApp.zip |
    | <abbr title="연속, 트리거된 형식이 포함 됩니다.">Type</abbr> | 트리거 | 
    | <abbr title="형식에는 예약 또는 수동이 포함 됩니다.">트리거</a> | 수동 | |

4. **확인** 을 클릭합니다. 

   새 WebJob이 **WebJobs** 페이지에 표시됩니다.

   ![WebJobs 목록](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **수동 WebJob을 실행 하려면** 목록에서 해당 이름을 마우스 오른쪽 단추로 클릭 하 고 **실행** 을 클릭 합니다.
   
    ![WebJob 실행](../media/web-sites-create-web-jobs/runondemand.png)

