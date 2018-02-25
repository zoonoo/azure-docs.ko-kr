---
title: "응용 프로그램을 Azure 및 Azure 배포 스택 | Microsoft Docs"
description: "하이브리드 CI/CD 파이프라인을 Azure 및 Azure 스택 앱을 배포 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 6292a846a2c3d7e112558ef0d2b62b3a3fdd3c51
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Azure 및 Azure에 앱 배포 스택
*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

하이브리드 [연속 통합](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[지속적인 업데이트](https://www.visualstudio.com/learn/what-is-continuous-delivery/)CI/CD () 파이프라인 빌드, 테스트 및 여러 클라우드에 앱을 배포할 수 있습니다.  이 자습서에서는 하이브리드 CI/CD 파이프라인 하는 방법에 대해 알아보려면 샘플 환경을 빌드할 수 있습니다.
 
> [!div class="checklist"]
> * Visual Studio Team Services (VSTS) 리포지토리에 코드 커밋에 따라 새 빌드를 시작 합니다.
> * 사용자 수용 테스트에 Azure를 새로 작성된 된 코드를 자동으로 배포 합니다.
> * 코드, 테스트를 통과 한 Azure 스택을 자동으로 배포 합니다. 


## <a name="prerequisites"></a>필수 조건
몇 가지 구성 요소는 하이브리드 CI/CD 파이프라인을 빌드하는 데 필요한 및 준비 하는 데 시간이 걸릴 수 있습니다.  이미 있는 경우 이러한 구성 요소 중 일부를 시작 하기 전에 요구 사항을 충족 해야 합니다.

이 항목에는 Azure 및 Azure 스택에 대 한 지식이 있다고 가정 합니다. 계속 하기 전에 자세한 정보를 원하는 경우 다음이 항목으로 시작 해야 합니다.

- [Azure 소개](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Azure 스택 주요 개념](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
 - 만들기는 [웹 앱](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md), 및 구성에 대해 [FTP 게시](../../app-service/app-service-deploy-ftp.md)합니다.  나중에 사용 중 이므로 새 웹 앱 URL을 기록해 두십시오.


### <a name="azure-stack"></a>Azure Stack
 - [Azure 스택 배포](../azure-stack-run-powershell-script.md)합니다.  설치는 일반적으로 있으므로 그에 따라 계획을 완료 하려면 몇 시간을 사용 합니다.
 - 배포 [앱 서비스](../azure-stack-app-service-deploy.md) Azure 스택에 PaaS 서비스입니다.
 - 웹 앱 만들기 및 구성에 대 한 [FTP 게시](../azure-stack-app-service-enable-ftp.md)합니다.  나중에 사용 중 이므로 새 웹 앱 URL을 기록해 두십시오.  

### <a name="developer-tools"></a>개발자 도구
 - 만들기는 [VSTS 작업 영역](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)합니다.  등록 프로세스 "MyFirstProject." 라는 프로젝트를 만듭니다.  
 - [Visual Studio 2017 설치](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 및 [VSTS 로그인](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - 프로젝트에 연결 하 고 [로컬로 복제](https://www.visualstudio.com/docs/git/gitquickstart)합니다.
 - 만들기는 [에이전트 풀](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues) VSTS에서 합니다.
 - Visual Studio를 설치 하 고 배포는 [VSTS 빌드 에이전트](https://www.visualstudio.com/docs/build/actions/agents/v2-windows) 스택에 Azure 가상 컴퓨터에 있습니다. 
 

## <a name="create-app--push-to-vsts"></a>앱 만들기 및 VSTS에 푸시

### <a name="create-application"></a>응용 프로그램 만들기
이 섹션에서는 간단한 ASP.NET 응용 프로그램 만들고 VSTS를 강제 합니다.  이러한 단계는 일반 개발자 워크플로 나타내는 하 고 개발자 도구 및 언어에 적용할 수 없습니다. 

1.  Visual Studio를 엽니다.
2.  팀 탐색기 공간에서 및 **솔루션 중...**  영역에서 클릭 **새로**합니다.
3.  선택 **Visual C#** > **웹** > **ASP.NET 웹 응용 프로그램 (.NET Framework)**합니다.
4.  응용 프로그램 및 클릭에 대 한 이름을 제공 **확인**합니다.
5.  다음 화면에서 기본값 (Web forms)를 유지 하 고 클릭 **확인**합니다.

### <a name="commit-and-push-changes-to-vsts"></a>커밋 및 VSTS에 변경 내용 적용
1.  팀 탐색기를 사용 하 여 Visual Studio에서, 드롭다운을 선택 하 고 클릭 **변경**합니다.
2.  커밋 메시지를 제공 하 고 선택 **모든**합니다. 솔루션 파일 저장을 모두 저장 하려면 예를 클릭 하 라는 메시지가 표시 될 수 있습니다.
3.  커밋되고 나면 Visual Studio 프로젝트에 변경 내용을 동기화를 제공 합니다. **동기화**를 선택합니다.

    ![커밋이 완료 되 면 커밋 화면을 보여 주는 이미지](./media/azure-stack-solution-pipeline/image1.png)

4.  동기화 탭에서 아래 *보내는*, 새 커밋을 참조 하십시오.  선택 **푸시** VSTS 하 여 변경 내용을 동기화 합니다.

    ![이미지 표시 동기화 단계](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>VSTS에서 코드 검토
한 번 변경 내용을 커밋 했습니다 및 VSTS 포털에서 코드를 체크 인 VSTS, 밀어넣습니다.  선택 **코드**, 차례로 **파일** 드롭다운 메뉴에서 합니다.  만든 솔루션을 볼 수 있습니다.

## <a name="create-build-definition"></a>빌드 정의 만들기
빌드 프로세스에는 응용 프로그램 빌드 및 코드 변경의 커밋 각에 배포를 위해 패키지 방법을 정의 합니다. 예제에서는 사용 하 여 포함 된 서식 파일 구성 ASP.NET 응용 프로그램에 대 한 빌드 프로세스 있지만이 구성은 응용 프로그램에 따라 조정 될 수 있습니다.

1.  VSTS 작업 영역에 웹 브라우저에서 로그인 합니다.
2.  선택의 배너에서 **빌드 및 릴리스** 차례로 **빌드**합니다.
3.  클릭 **+ 새 정의**합니다.
4.  템플릿 목록에서 선택 **ASP.NET (미리 보기)** 선택 **적용**합니다.
5.  수정 된 *MSBuild 인수* 필드에 *솔루션 빌드* 한 단계씩 코드 실행:

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  선택 된 **옵션** 탭을 스택에 Azure 가상 컴퓨터에 배포 하는 빌드 에이전트에 대 한 에이전트 큐를 선택 합니다. 
7.  선택 된 **트리거** 탭을 사용 하도록 설정 **연속 통합**합니다.
7.  클릭 **저장 후 큐** 선택한 후 **저장** 드롭다운 목록에서 합니다. 

## <a name="create-release-definition"></a>릴리스 정의 만들기
릴리스 프로세스의 이전 단계에서 빌드 환경에 배포 되는 방식을 정의 합니다.  이 자습서에서는 Azure 웹 앱에 FTP 통해 ASP.NET 응용 프로그램을 게시합니다. Azure로 릴리스를 구성 하려면 다음 단계를 사용 합니다.

1.  VSTS 배너에서 선택 **빌드 및 릴리스** 차례로 **릴리스**합니다.
2.  녹색 클릭 **+ 새 정의**합니다.
3.  선택 **빈** 클릭 **다음**합니다.
4.  에 대 한 확인란 *연속 배포*, 클릭 하 고 **만들기**합니다.

빈 릴리스 정의 생성 하 고 빌드에 연결 했으므로 Azure 환경에 대 한 단계를 추가 했습니다.

1.  녹색 클릭  **+**  작업을 추가 합니다.
2.  선택 **모든**, 한 다음 목록에서 추가 **FTP 업로드** 선택 **닫기**합니다.
3.  선택 된 **FTP 업로드** 방금 추가한 수행할 작업 하 고 다음 매개 변수를 구성 합니다.
    
    | 매개 변수 | 값 |
    | ----- | ----- |
    |인증 방법| 자격 증명 입력|
    |서버 URL | Azure 포털에서 검색 된 웹 응용 프로그램 FTP URL |
    |사용자 이름 | 웹 앱에 대 한 FTP 자격 증명을 만들 때 구성한 사용자 이름 |
    |암호 | 웹 앱에 대 한 FTP 자격 증명을 설정할 때 만든 암호|
    |원본 디렉터리 | $(System.DefaultWorkingDirectory)\**\ |
    |원격 디렉터리 | /site/wwwroot/ |
    |파일 경로 유지 합니다. | 사용 하도록 설정 (확인란 선택)|

4.  페이지 맨 아래에 있는 **저장**

마지막으로, 다음 단계를 사용 하 여 배포 에이전트를 포함 하는 에이전트 풀을 사용 하도록 릴리스 정의 구성 합니다.
1.  릴리스 정의 선택 하 고 클릭 **편집**합니다.
2.  선택 **에이전트에서 실행** 가운데 열에서 합니다.  오른쪽 열에서 Azure 스택에 실행 되는 빌드 에이전트를 포함 하는 에이전트 큐를 선택 합니다.  
    ![특정 큐를 사용 하려면 릴리스 정의의 표시 된 구성 이미지](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Azure에 앱 배포
이 단계는 Azure에서 웹 응용 프로그램에 ASP.NET 응용 프로그램을 배포 하려면 새로 빌드된 CI/CD 파이프라인을 사용 합니다. 

1.  VSTS에서 배너에서 선택 **빌드 및 릴리스**를 선택한 후 **빌드**합니다.
2.  클릭 **중...**  선택 하 고 이전에 만든 빌드 정의에 **새 빌드 큐 대기**합니다.
3.  기본값을 적용 하 고 클릭 **확인**합니다.  빌드를 시작 하 고 진행률을 표시 합니다.
4.  빌드가 완료 되 면 선택 하 여 상태를 추적할 수 있습니다 **빌드 및 릴리스** 선택 하 고 **릴리스**합니다.
5.  빌드가 완료 되 면 웹 앱을 생성할 때 확인 URL을 사용 하 여 웹 사이트를 방문 합니다.    


## <a name="add-azure-stack-to-pipeline"></a>Azure 스택 파이프라인에 추가 합니다.
이제 Azure에 배포 하 여 CI/CD 파이프라인을 테스트 하면 Azure 스택 파이프라인에 추가 하는 시간입니다.  다음 단계에서는 새 환경을 만들고 Azure 스택 앱을 배포 하는 FTP 업로드 태스크를 추가 합니다.  "사인 오프" Azure 스택에 코드 릴리스에서 시뮬레이션 하는 방법으로 사용 되는 릴리스 승인자를 추가할 수도 있습니다.  

1.  릴리스 정의의 선택 **환경을 추가 +** 및 **새 환경 만들기**합니다.
2.  선택 **빈**, 클릭 **다음**합니다.
3.  선택 **특정 사용자에 게** 사용자 계정을 지정 합니다.  **만들기**를 선택합니다.
4.  기존 이름을 입력 하 여 환경 이름을 바꾸는 *Azure 스택*합니다.
5.  이제 Azure 스택 환경 선택 select 다음 **작업 추가**합니다.
6.  선택 된 **FTP 업로드** 작업 및 선택 **추가**을 선택한 후 **닫기**합니다.


### <a name="configure-ftp-task"></a>FTP 태스크 구성
릴리스를 만들었으므로 이제 Azure 스택에 웹 앱에 게시 하는 데 필요한 단계를 구성 합니다.  Azure에 대 한 FTP 업로드 태스크를 구성 하는 것 처럼 Azure 스택에 대 한 작업을 구성 합니다.

1.  선택 된 **FTP 업로드** 방금 추가한 수행할 작업 하 고 다음 매개 변수를 구성 합니다.
    
    | 매개 변수 | 값 |
    | -----     | ----- |
    |인증 방법| 자격 증명 입력|
    |서버 URL | Azure 스택 포털에서 검색 된 웹 응용 프로그램 FTP URL |
    |사용자 이름 | 웹 앱에 대 한 FTP 자격 증명을 만들 때 구성한 사용자 이름 |
    |암호 | 웹 앱에 대 한 FTP 자격 증명을 설정할 때 만든 암호|
    |원본 디렉터리 | $(System.DefaultWorkingDirectory)\**\ |
    |원격 디렉터리 | /site/wwwroot/|
    |파일 경로 유지 합니다. | 사용 하도록 설정 (확인란 선택)|

2.  페이지 맨 아래에 있는 **저장**

마지막으로, 다음 단계를 사용 하 여 배포 에이전트를 포함 하는 에이전트 풀을 사용 하려면 릴리스 정의 구성 합니다.
1.  릴리스 정의 선택 하 고 클릭 **편집**
2.  선택 **에이전트에서 실행** 가운데 열에서 합니다. 오른쪽 열에서 Azure 스택에 실행 되는 빌드 에이전트를 포함 하는 에이전트 큐를 선택 합니다.  
    ![특정 큐를 사용 하려면 릴리스 정의의 표시 된 구성 이미지](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>새 코드 배포
이제 Azure 스택에 게시 하는 마지막 단계와 하이브리드 CI/CD 파이프라인을 테스트할 수 있습니다.  이 섹션에서는 사이트의 바닥글을 수정 하 고 파이프라인을 통해 배포를 시작 합니다.  검토를 위해 Azure에 배포 된 변경 내용을 표시는 완료 되 면 다음 릴리스를 승인 되 면 Azure 스택에 게시 합니다.

1. Visual Studio에서 열고는 *site.master* 파일을이 줄을 변경 합니다.
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    다음과 같이 변경합니다.

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  변경 내용을 커밋하고 VSTS에 동기화 합니다.  
4.  VSTS 작업 영역에서 선택 하 여 빌드 상태를 검사 **빌드 및 릴리스** > **빌드**
5.  진행 중인 빌드가 표시 됩니다.  상태를 두 번 클릭 하 고 빌드 진행률을 볼 수 있습니다.  콘솔에서 "완료 된 빌드"를 표시 되 면 릴리스를 확인 하려면 이동 **빌드 및 릴리스** > **릴리스**합니다.  릴리스를 두 번 클릭 합니다.
6.  릴리스 검토 해야 한다는 알림을 받게 됩니다. 웹 앱 URL을 확인 하 고 새 변경 내용이 있는지 확인 합니다.  VSTS에서 릴리스를 승인 합니다.
    ![특정 큐를 사용 하려면 릴리스 정의의 표시 된 구성 이미지](./media/azure-stack-solution-pipeline/image4.png)
    
7.  웹 앱을 생성할 때 확인 URL을 사용 하 여 웹 사이트를 방문 하 여 Azure 스택에 게시 완료 되었는지 확인 합니다.
    ![ASP를 보여 주는 이미지입니다. NEt 응용 프로그램 변경 바닥글](./media/azure-stack-solution-pipeline/image5.png)


이제 다른 하이브리드 클라우드 패턴에 대 한 빌딩 블록으로 새로운 하이브리드 CI/CD 파이프라인을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 하이브리드 빌드하는 방법을 배웠습니다 CI/CD 파이프라인입니다.

> [!div class="checklist"]
> * 시작 코드에 따라 새 빌드를 VSTS Visual Studio Team Services () 저장소에 커밋합니다.
> * 사용자 수용 테스트에 Azure에 새로 작성된 된 코드를 자동으로 배포 합니다.
> * 코드, 테스트를 통과 한 Azure 스택에 자동으로 배포 합니다. 

이제 하이브리드 CI/CD 파이프라인 했으므로 어 Azure 스택에 대 한 앱을 개발 하는 방법을 배웁니다.

> [!div class="nextstepaction"]
> [Azure Stack에 대한 개발](azure-stack-developer.md)


