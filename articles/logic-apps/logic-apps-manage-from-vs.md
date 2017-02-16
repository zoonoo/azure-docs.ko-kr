---
title: "Visual Studio 클라우드 탐색기에서 Azure Logic Apps 관리 | Microsoft Docs"
description: "Visual Studio 클라우드 탐색기에서 Azure Logic Apps 관리"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>Visual Studio 클라우드 탐색기를 사용하여 Logic Apps 관리
[Azure Portal](https://portal.azure.com)을 통해 Logic Apps을 간편하게 설계하고 관리할 수 있지만, Visual Studio 클라우드 탐색기를 사용하면 Logic Apps을 포함한 여러 Azure 자산을 Visual Studio 내에서 관리할 수 있습니다.  클라우드 탐색기를 사용하여 게시된 Logic Apps을 찾아보고 실행 기록을 활성화하고, 비활성화하고, 편집하고, 보는 등의 작업을 수행할 수 있습니다. 

## <a name="installation-steps"></a>설치 단계
다음은 논리 앱용 Visual Studio 도구를 설치하고 구성하는 단계입니다.

### <a name="prerequisites"></a>필수 조건
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [최신 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 이상)
* [Visual Studio 클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* 포함된 디자이너를 사용하는 경우 웹에 대한 액세스

### <a name="install-visual-studio-tools-for-logic-apps"></a>논리 앱용 Visual Studio 도구 설치
필수 구성 요소를 설치한 경우, 

1. Visual Studio 2015를 열고 **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다.
2. **온라인** 범주를 선택하여 온라인으로 검색합니다.
3. **Logic Apps**를 검색하여 **Visual Studio용 Azure Logic Apps 도구**를 표시합니다.
4. **다운로드** 단추를 클릭하여 확장을 설치합니다.
5. 설치 후 Visual Studio를 다시 시작합니다.

> [!NOTE]
> [이 링크](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

## <a name="browsing-logic-apps"></a>Logic Apps 검색
클라우드 탐색기에서 Logic Apps을 찾으려면 보기 > 클라우드 탐색기에서 클라우드 탐색기를 열고 리소스 그룹 또는 리소스 유형별로 찾아볼 수 있습니다.  리소스 유형별로 찾아보는 경우 구독을 선택하고 Logic Apps 섹션을 확장한 다음 논리 앱을 선택합니다.  Logic Apps 중 하나를 마우스 오른쪽 단추로 클릭하거나 클라우드 탐색기 맨 아래의 작업 메뉴를 사용하여 원하는 작업을 수행할 수 있습니다.

![찾아보기](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>디자이너를 사용하여 논리 앱 편집
현재 배포된 논리 앱을 Azure Portal에 있는 것과 동일한 디자이너에서 열려면 논리 앱을 마우스 오른쪽 단추로 클릭하고 "논리 앱 편집기로 열기"를 선택합니다.  이 디자이너에서 논리 앱을 편집하고 다시 클라우드에 저장한 후 "트리거 실행" 단추를 사용하여 새 실행을 시작할 수 있습니다.

![디자이너](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>논리 앱 실행 기록 찾아보기
논리 앱의 실행 기록을 나열하려면 논리 앱을 마우스 오른쪽 단추로 클릭하고 "실행 기록 열기"를 선택합니다.  이 보기에서 열 머리글을 선택하여 표시된 속성을 기준으로 정렬할 수 있습니다.  

![실행](media/logic-apps-manage-from-vs/runs.png)

실행 인스턴스 중 하나를 두 번 클릭하면 각 단계의 입력 및 출력을 포함하여 실행 결과를 볼 수 있는 해당 인스턴스에 대한 실행 기록이 표시됩니다.

![기록](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>다음 단계
* 논리 앱을 시작하려면 [논리 앱 만들기](logic-apps-create-a-logic-app.md) 자습서를 따르세요.  
* [일반적인 예제 및 시나리오 보기](logic-apps-examples-and-scenarios.md)
* [논리 앱으로 비즈니스 프로세스를 자동화할 수 있습니다](http://channel9.msdn.com/Events/Build/2016/T694) 
* [논리 앱과 시스템을 통합하는 방법을 알아봅니다](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


