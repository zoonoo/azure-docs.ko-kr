---
title: "Visual Studio에서 논리 앱 빌드 | Microsoft Docs"
description: "논리 앱을 만들어 관리하기 위해 Visual Studio에서 프로젝트를 만듭니다."
author: jeffhollan
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 73a57310c07ed946480f60f6564d8619e54155a5


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Visual Studio에서 논리 앱 빌드 및 배포
[Azure 포털](https://portal.azure.com/) 에서 논리 앱을 디자인하고 관리하는 훌륭한 방법을 제공하지만, Visual Studio에서도 논리 앱을 디자인하고 배포할 수도 있습니다.  논리 앱은 풍부한 Visual Studio 도구 집합과 함께 제공되어 디자이너를 사용하여 논리 앱을 빌드하고, 모든 배포 및 자동화 템플릿을 구성하며, 모든 환경에 배포할 수 있습니다.  

## <a name="installation-steps"></a>설치 단계
다음은 논리 앱용 Visual Studio 도구를 설치하고 구성하는 단계입니다.

### <a name="prerequisites"></a>필수 조건
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [최신 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 이상)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* 포함된 디자이너를 사용하는 경우 웹에 대한 액세스

### <a name="install-visual-studio-tools-for-logic-apps"></a>논리 앱용 Visual Studio 도구 설치
필수 구성 요소를 설치한 경우, 

1. Visual Studio 2015를 열고 **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다.
2. **온라인** 범주를 선택하여 온라인으로 검색합니다.
3. **Logic Apps**를 검색하여 **Visual Studio용 Azure Logic Apps 도구**를 표시합니다.
4. **다운로드** 단추를 클릭하여 확장을 설치합니다.
5. 설치 후 Visual Studio를 다시 시작합니다.

> [!NOTE]
>  [이 링크](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

설치가 완료되면 논리 앱 디자이너로 Azure 리소스 그룹 프로젝트를 사용할 수 있습니다.

## <a name="create-a-project"></a>프로젝트 만들기
1. **파일** 메뉴로 이동하여 **새로 만들기** >  **프로젝트**를 선택합니다(또는 **추가**로 이동한 다음 **새 프로젝트**를 선택하여 기존 솔루션에 추가 가능).  ![파일 메뉴](./media/app-service-logic-deploy-from-vs/filemenu.png)
2. 대화 상자에서 **클라우드**를 찾아 **Azure 리소스 그룹**을 선택합니다. **이름**을 입력한 다음 **확인**을 클릭합니다.
    ![새 프로젝트 추가](./media/app-service-logic-deploy-from-vs/addnewproject.png)
3. **논리 앱** 템플릿을 선택합니다. 이렇게 하면 빈 논리 앱 배포 템플릿을 생성하여 시작됩니다.
    ![Azure 템플릿 선택](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)
4. **템플릿**을 선택했으면 **확인**을 누릅니다.
   
    이제 논리 앱 프로젝트가 솔루션에 추가됩니다. 솔루션 탐색기에 배포 파일이 표시되어야 합니다.  
   
    ![배포](./media/app-service-logic-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>논리 앱 디자이너 사용
논리 앱이 포함된 Azure 리소스 그룹 프로젝트를 만들면, Visual Studio 내에서 디자이너를 열어 워크플로를 만들 수 있습니다.  디자이너는 사용 가능한 속성 및 데이터를 위한 커넥터를 쿼리하기 위해 인터넷에 연결이 필요합니다(예를 들어, Dynamics CRM Online 커넥터를 사용하는 경우 디자이너는 CRM 인스턴스를 쿼리하여 사용 가능한 사용자 지정 및 기본 속성을 나열합니다).

1. `<template>.json` 파일을 마우스 오른쪽 단추로 클릭하고 **논리 앱 디자이너로 열기**(또는 `Ctrl+L`)를 선택합니다.
2. 구독, 리소스 그룹 및 배포 템플릿의 위치를 선택합니다.
   * 논리 앱을 디자인하면 디자인하는 동안 속성을 쿼리하는 **API 연결** 리소스가 만들어집니다.  선택한 리소스 그룹은 디자인 시간 동안 이 연결을 만드는 데 사용되는 리소스 그룹이 됩니다.  Azure Portal로 이동하여 **API 연결**을 검색함으로써 모든 API 연결을 보거나 수정할 수 있습니다.
     ![구독 선택기](./media/app-service-logic-deploy-from-vs/designer_picker.png)
3. 디자이너는 `<template>.json` 파일의 정의에 따라 렌더링해야 합니다.
4. 이제 논리 앱을 만들어 디자인할 수 있으며 변경 내용은 배포 템플릿에서 업데이트됩니다.
    ![Visual Studio의 디자이너](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

또한 논리 앱이 기능하는 데 필요한 모든 연결을 위해 리소스 파일에 추가되는 `Microsoft.Web/connections` 리소스도 볼 수 있습니다.  이러한 연결 속성은 배포할 때 설정할 수 있으며 배포한 후 Azure Portal의 **API 연결** 에서 관리할 수 있습니다.

### <a name="switching-to-the-json-code-view"></a>JSON 코드 보기로 전환
디자이너 아래에 있는 **코드 보기** 탭을 선택하여 논리 앱의 JSON 표현으로 전환할 수 있습니다.  전체 리소스 JSON으로 다시 전환하려면 `<template>.json` 파일을 마우스 오른쪽 단추로 클릭하고 **열기**를 선택합니다.

### <a name="saving-the-logic-app"></a>논리 앱 저장
**저장** 단추 또는 `Ctrl+S`를 통해 언제든지 논리 앱을 저장할 수 있습니다.  저장할 때 논리 앱에 오류가 발생하는 경우 Visual Studio의 **출력** 창에 오류가 표시됩니다.

## <a name="deploying-your-logic-app"></a>논리 앱 배포
마지막으로, 앱을 구성한 후 몇 단계 만에 Visual Studio에서 직접 배포할 수 있습니다. 

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포** > **새 배포...**
    ![새 배포](./media/app-service-logic-deploy-from-vs/newdeployment.png)로 이동합니다.
2. Azure 구독에 로그인하라는 메시지가 표시됩니다. 
3. 이제 논리 앱을 배포하려는 리소스 그룹의 세부 정보를 선택해야 합니다. 
    ![리소스 그룹에 배포](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > 리소스 그룹(예: 프로덕션 환경에 배포하는 경우 프로덕션 매개 변수 파일을 선택하고 싶어함)에 대한 올바른 템플릿 및 매개 변수 파일을 선택해야 합니다. 
   > 
   > 
4. 배포 단추를 선택합니다.
5. 배포의 상태가 **출력** 창(**Azure 프로비전**을 선택해야 할 수 있음)에 표시됩니다. 
    ![출력](./media/app-service-logic-deploy-from-vs/output.png)

나중에 소스 제어에서 논리 앱을 수정하고 새 버전을 배포하기 위해 Visual Studio를 사용할 수 있습니다. 

> [!NOTE]
> Azure 포털에서 정의를 직접 수정한 경우 다음에 Visual Studio에서 변경 내용을 배포하면 해당 변경 내용이 덮어쓰여집니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* 논리 앱을 시작하려면 [논리 앱 만들기](app-service-logic-create-a-logic-app.md) 자습서를 따르세요.  
* [일반적인 예제 및 시나리오 보기](app-service-logic-examples-and-scenarios.md)
* [논리 앱으로 비즈니스 프로세스를 자동화할 수 있습니다](http://channel9.msdn.com/Events/Build/2016/T694) 
* [논리 앱과 시스템을 통합하는 방법을 알아봅니다](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Nov16_HO3-->


