---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745661"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a>연속 WebJob 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **App Service** 로 이동합니다. <abbr title="앱 리소스는 웹앱, API 앱 또는 모바일 앱일 수 있습니다.">앱 리소스</abbr>.
1. **WebJobs** 를 선택합니다.

   ![WebJobs 선택](../media/web-sites-create-web-jobs/select-webjobs.png)

1. **WebJobs** 페이지에서 **추가** 를 선택합니다.

    ![WebJob 페이지](../media/web-sites-create-web-jobs/wjblade.png)

1. 표에 지정된 대로 **WebJob 추가** 설정을 사용합니다.

   ![구성해야 하는 WebJob 추가 설정을 보여 주는 스크린샷](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | 설정      | 샘플 값   | 
   | ------------ | ----------------- | 
   | <abbr title="App Service 앱 내에서 고유한 이름입니다. 문자 또는 숫자로 시작해야 하며, `-` 및 `_`을 제외한 다른 특수 문자를 포함할 수 없습니다.">이름</abbr> | myContinuousWebJob | 
   | <abbr title=" 실행 파일 또는 스크립트 파일뿐만 아니라 프로그램 또는 스크립트를 실행하는 데 필요한 지원 파일이 포함되는 *.zip* 파일입니다.">파일 업로드</abbr> | ConsoleApp.zip |
   | <abbr title="형식에는 연속, 트리거됨이 포함됩니다.">Type</abbr> | 계속 | 
   | <abbr title="연속 WebJobs에 대해서만 사용할 수 있습니다. 프로그램 또는 스크립트가 모든 인스턴스 또는 하나의 인스턴스에서 실행되는지를 결정합니다. 여러 인스턴스에서 실행하는 옵션은 무료 또는 공유 가격 책정 계층에 적용되지 않습니다.">확장</abbr> | 다중 인스턴스 | 

1. **확인** 을 클릭합니다.

    새 WebJob이 **WebJobs** 페이지에 표시됩니다.

    ![WebJobs 목록](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. 연속 WebJob을 **중지 또는 시작** 하려면 목록에서 WebJob을 마우스 오른쪽 단추로 클릭하고 **중지** 또는 **시작** 을 클릭합니다.

   ![연속 WebJob 중지](../media/web-sites-create-web-jobs/continuousstop.png)
