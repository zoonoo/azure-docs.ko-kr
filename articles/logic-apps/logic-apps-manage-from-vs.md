---
title: "Visual Studio에서 Logic Apps 관리 - Azure Logic Apps | Microsoft Docs"
description: "Visual Studio 클라우드 탐색기를 사용하여 Logic Apps 및 기타 Azure 자산 관리"
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
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: a5bf24de1a7a2b6d4c1ae6416c95d83ef7506da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Visual Studio 클라우드 탐색기를 사용하여 Logic Apps 관리

[Azure Portal](https://portal.azure.com/)에서는 Azure Logic Apps를 디자인하고 관리하는 유용한 방법을 제공하지만 Visual Studio 클라우드 탐색기를 사용하면 논리 앱을 포함한 여러 Azure 자산을 관리할 수 있습니다. Visual Studio 클라우드 탐색기를 사용하면 게시된 논리 앱을 찾아보기, 관리, 편집 및 다운로드할 수 있습니다. 관리 작업에는 실행 기록의 보기, 활성화 및 비활성화가 포함됩니다. 

먼저 이러한 Azure Logic Apps용 Visual Studio Tools를 설치하고 구성해야 Visual Studio에서 논리 앱에 액세스하고 관리할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* [Visual Studio 2015 또는 Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [최신 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 이상)
* [Visual Studio 클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* 포함된 디자이너를 사용하는 경우 웹에 대한 액세스

## <a name="install-visual-studio-tools-for-logic-apps"></a>Logic Apps용 Visual Studio 도구 설치

필수 구성 요소를 설치한 후 Visual Studio용 Azure Logic Apps 도구를 다운로드하여 설치합니다.

1. Visual Studio를 엽니다. **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다.
2. Visual Studio 갤러리에서 온라인을 검색할 수 있도록 **온라인** 범주를 확장합니다.
3. **Visual Studio용 Azure Logic Apps 도구**를 찾을 때까지 **Logic Apps**을 찾아보거나 검색합니다.
4. 확장을 다운로드하고 설치하려면 **다운로드**를 클릭합니다.
5. 설치 후 Visual Studio를 다시 시작합니다.

> [!NOTE]
> 직접 Visual Studio용 Azure Logic Apps 도구를 다운로드하려면 [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)로 이동합니다.

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>클라우드 탐색기에서 논리 앱 찾아보기

1.  클라우드 탐색기를 열려면 **보기** 메뉴에서 **클라우드 탐색기**를 선택합니다.
2.  리소스 그룹 또는 리소스 유형별로 논리 앱을 찾아봅니다. 

    * 리소스 종류별로 찾아보는 경우 Azure 구독을 선택하고 **Logic Apps** 섹션을 확장한 다음 논리 앱을 선택합니다. 
    * 리소스 그룹별로 찾아보는 경우 논리 앱을 갖는 리소스 그룹을 확장하고 논리 앱을 선택합니다.

    논리 앱에 대한 명령을 보려면 논리 앱을 마우스 오른쪽 단추로 클릭하거나 클라우드 탐색기 아래쪽의 **작업** 메뉴에서 선택합니다.

    ![논리 앱 찾아보기](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Logic Apps 디자이너로 논리 앱 편집

클라우드 탐색기에서는 Azure Portal에서 사용하는 동일한 디자이너에서 현재 배포된 논리 앱을 열 수 있습니다. 

* 클라우드 탐색기에서 논리 앱을 편집하려면 논리 앱을 마우스 오른쪽 단추로 클릭하고 **논리 앱 편집기로 열기**를 선택합니다. 

* 업데이트를 클라우드에 게시하려면 **게시**를 선택합니다. 

* 새 실행을 시작하려면 **트리거 실행**을 선택합니다.

![논리 앱 디자이너](./media/logic-apps-manage-from-vs/designer.png)

디자이너에서 논리 앱을 **다운로드**할 수도 있습니다. 이 작업은 자동으로 논리 앱 정의를 매개 변수화하고 Azure Resource Manager 배포 템플릿으로 정의를 저장합니다. Azure 리소스 그룹 프로젝트에 이 배포 템플릿을 추가할 수 있습니다.

## <a name="browse-your-logic-app-run-history"></a>논리 앱 실행 기록 찾아보기

논리 앱의 실행 기록을 보려면 논리 앱을 마우스 오른쪽 단추로 클릭하고 **실행 기록 열기**를 선택합니다. 표시된 속성 중 하나에 따라 실행 기록의 순서를 변경하려면 열 머리글을 선택합니다.

![실행 기록](media/logic-apps-manage-from-vs/runs.png)

각 단계의 입력 및 출력을 비롯하여 실행 결과를 검토할 수 있도록 인스턴스에 대한 실행 기록을 표시하려면 실행 인스턴스 중 하나를 두 번 클릭합니다.

![단계에서 기록 결과, 입력 및 출력 실행](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>다음 단계

* [첫 번째 논리 앱 만들기](logic-apps-create-a-logic-app.md)
* [Visual Studio에서 Logic Apps 디자인, 빌드 및 배포](logic-apps-deploy-from-vs.md)
* [일반적인 예제 및 시나리오 보기](logic-apps-examples-and-scenarios.md)
* [동영상: Azure Logic Apps를 사용하여 비즈니스 프로세스 자동화](http://channel9.msdn.com/Events/Build/2016/T694)
* [동영상: Azure Logic Apps와 시스템 통합](http://channel9.msdn.com/Events/Build/2016/P462)
