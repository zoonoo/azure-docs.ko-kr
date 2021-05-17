---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 40e9e2b0e7437566a132f2402d3bed01b284d11a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081012"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>수동으로 트리거된 WebJob 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **App Service** 로 이동합니다. <abbr title="앱 리소스는 웹앱, API 앱 또는 모바일 앱일 수 있습니다.">앱 리소스</abbr>.
1. **WebJobs** 를 선택합니다.

    ![WebJobs 선택](../media/web-sites-create-web-jobs/select-webjobs.png)

2. **WebJobs** 페이지에서 **추가** 를 선택합니다.

   ![WebJob 페이지](../media/web-sites-create-web-jobs/wjblade.png)

3. 표에 지정된 대로 **WebJob 추가** 설정을 사용합니다.

    ![수동으로 트리거된 WebJob을 만들기 위해 설정해야 하는 설정을 보여 주는 스크린샷.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | 설정      | 샘플 값   | 
    | ------------ | ----------------- | 
   | <abbr title="App Service 앱 내에서 고유한 이름입니다. 문자 또는 숫자로 시작해야 하며, `-` 및 `_`을 제외한 다른 특수 문자를 포함할 수 없습니다.">이름</abbr> | myTriggeredWebJob | 
    | <abbr title="실행 파일 또는 스크립트 파일뿐만 아니라 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되는 *.zip* 파일입니다.">파일 업로드</abbr> | ConsoleApp.zip |
    | <abbr title="형식에는 연속, 트리거된 형식이 포함됩니다.">Type</abbr> | 트리거 | 
    | <abbr title="형식에는 예약 또는 수동이 포함됩니다.">트리거</a> | 수동 | 

4. **확인** 을 클릭합니다. 

   새 WebJob이 **WebJobs** 페이지에 표시됩니다.

   ![WebJobs 목록](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **수동 WebJob을 실행** 하려면 목록에서 이름을 마우스 오른쪽 단추로 클릭하고 **실행** 을 클릭합니다.
   
    ![WebJob 실행](../media/web-sites-create-web-jobs/runondemand.png)

