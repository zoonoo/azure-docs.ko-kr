---
title: VSTS를 사용하여 CI/CD로 Azure Stream Analytics 작업 배포 자습서
description: 이 문서에서는 VSTS를 사용하여 CI/CD로 Stream Analytics 작업을 배포하는 방법을 설명합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: d4f1e188a1a145ba3be5fb45d2b0ea4d0bfd57a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41921036"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>자습서: VSTS를 사용하여 CI/CD로 Azure Stream Analytics 작업 배포
이 자습서에서는 Visual Studio Team Services를 사용하여 Azure Stream Analytics 작업에 대한 연속 통합 및 배포를 설정하는 방법을 설명합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 프로젝트에 소스 제어 추가
> * Team Services에서 빌드 정의 만들기
> * Team Services에서 릴리스 정의 만들기
> * 응용 프로그램 자동 배포 및 업그레이드

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음이 필요합니다.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 및 **Azure 개발** 또는 **데이터 저장소 및 처리** 워크로드를 설치합니다.
* [Visual Studio에서 Stream Analytics 프로젝트](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs)를 만듭니다.
* [Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/) 계정을 만듭니다.

## <a name="configure-nuget-package-dependency"></a>NuGet 패키지 종속성 구성
임의의 시스템에서 자동 빌드 및 자동 배포를 수행하려면 NuGet 패키지 `Microsoft.Azure.StreamAnalytics.CICD`를 사용해야 합니다. Stream Analytics Visual Studio 프로젝트의 지속적 통합과 배포를 지원하는 MSBuild, 로컬 실행 및 배포 도구를 제공합니다. 자세한 내용은 [Stream Analytics CI/CD 도구](stream-analytics-tools-for-visual-studio-cicd.md)를 참조하세요.

프로젝트 디렉터리에 **packages.config**를 추가합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>새 Team Services Git 리포지토리에 Visual Studio 솔루션 공유
응용 프로그램 소스 파일을 Team Services의 팀 프로젝트에 공유하여 빌드를 생성할 수 있습니다.  

1. Visual Studio의 오른쪽 하단의 상태 표시줄에서 **소스 제어에 추가**를 선택한 다음, **Git**을 선택하여 프로젝트에 대한 새 로컬 Git 리포지토리를 만듭니다. 

2. **팀 탐색기**의 **동기화** 보기에서 **Visual Studio Team Services에 푸시** 아래에 있는 **Git 리포지토리 게시** 단추를 선택합니다.

   ![Git 리포지토리 푸시](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. 사용자의 전자 메일을 확인하고 **Team Services 도메인** 드롭다운에서 계정을 선택합니다. 리포지토리 이름을 입력하고 **리포지토리 게시**를 선택합니다.

   ![Git 리포지토리 푸시](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    리포지토리를 게시하면 사용자 계정에 로컬 리포지토리와 같은 이름으로 새 팀 프로젝트가 만들어집니다. 기존 팀 프로젝트에서 리포지토리를 만들려면 **리포지토리** 이름 옆에서 **고급**을 클릭하고 팀 프로젝트를 선택합니다. **웹에서 확인하세요**를 선택하면 웹에서 코드를 볼 수 있습니다.
 
## <a name="configure-continuous-delivery-with-vsts"></a>VSTS를 사용한 지속적인 업데이트 구성
Team Services 빌드 정의는 순차적으로 실행되는 빌드 단계로 구성된 워크플로를 설명합니다. [Team Services 빌드 정의](https://www.visualstudio.com/docs/build/define/create)에 대해 자세히 알아봅니다. 

Team Services 릴리스 정의에서는 응용 프로그램 패키지를 클러스터에 배포하는 워크플로를 설명합니다. 빌드 정의와 릴리스 정의를 함께 사용할 경우 소스 파일로 시작하여 클러스터에서 실행 중인 응용 프로그램에서 종료할 때까지 전체 워크플로를 실행합니다. Team Services [릴리스 정의](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)에 대해 자세히 알아봅니다.

### <a name="create-a-build-definition"></a>빌드 정의 만들기
웹 브라우저를 열고 [Visual Studio Team Services](https://app.vsaex.visualstudio.com/)에서 방금 만든 팀 프로젝트로 이동합니다. 

1. **빌드 및 릴리스** 탭 아래에서 **빌드**를 선택한 다음, **+새로 만들기**를 선택합니다.  **VSTS Git**를 선택하고 **계속**을 선택합니다.
    
    ![원본 선택](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. **템플릿 선택**에서 **빈 프로세스**를 클릭하여 빈 정의로 시작합니다.
    
    ![빌드 템플릿 선택](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. **트리거** 아래에서 **지속적인 통합 사용** 트리거 상태를 확인하여 연속 통합을 사용하도록 설정합니다.  **저장 및 큐**를 선택하여 수동으로 빌드를 시작합니다. 
    
    ![트리거 상태](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. 빌드는 푸시 또는 체크인할 때도 트리거됩니다. 빌드 진행률을 확인하려면 **빌드** 탭으로 전환합니다.  빌드가 성공적으로 실행되는지 확인한 후에는 응용 프로그램을 클러스터에 배포하는 릴리스 정의를 정의해야 합니다. 빌드 정의 옆의 줄임표를 마우스 오른쪽 단추로 클릭하고 **편집**을 선택합니다.

5.  **작업**에서 **에이전트 큐**로 "호스트된"을 입력합니다.
    
    ![에이전트 큐 선택](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. **1 단계**에서 **+** 를 클릭하고 **NuGet** 작업을 추가합니다.
    
    ![NuGet 작업 추가](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. **고급**을 펼치고 `$(Build.SourcesDirectory)\packages`를 **대상 디렉터리**에 추가합니다. 나머지 기본 NuGet 구성 값을 유지합니다.

   ![NuGet 작업 구성](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. **1 단계**에서 **+** 를 클릭하고 **MSBuild** 작업을 추가합니다.

   ![MSBuild 작업 추가](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. **MSBuild 인수**를 다음과 같이 변경합니다.

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![MSBuild 작업 구성](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. **1단계**에서 **+** 를 클릭하고 **Azure Resource Group 배포** 작업을 추가합니다. 
    
    ![Azure 리소스 그룹 배포 작업 추가](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. **Azure 세부 정보**를 펼쳐서 다음과 같이 구성을 채웁니다.
    
    |**설정**  |**제안 값**  |
    |---------|---------|
    |구독  |  구독을 선택합니다.   |
    |조치  |  리소스 그룹을 만들기 또는 업데이트   |
    |리소스 그룹  |  리소스 그룹 이름을 입력합니다.   |
    |Template  | [솔루션 경로]\bin\Debug\Deploy\\[프로젝트 이름].JobTemplate.json   |
    |템플릿 매개 변수  | [솔루션 경로]\bin\Debug\Deploy\\[프로젝트 이름].JobTemplate.parameters.json   |
    |템플릿 매개 변수 재정의  | 텍스트 상자에 재정의할 템플릿 매개 변수를 입력합니다. 예: –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). 이 속성은 선택 사항이지만 키 매개 변수를 재정의하지 않으면 빌드에 오류가 발생합니다.    |
    
    ![속성 설정](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. **저장 및 큐에 넣기**를 클릭하여 빌드 정의를 테스트합니다.
    
    ![재정의 매개 변수 설정](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>실패한 빌드 프로세스
빌드 정의의 **Azure Resource 그룹 배포** 작업에서 템플릿 매개 변수를 재정의하지 않은 경우 null 배포 매개 변수에 대한 오류가 발생할 수 있습니다. 빌드 정의로 돌아가서 null 매개 변수를 재정의하여 오류를 해결합니다.

   ![빌드 프로세스 실패](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>변경 내용을 커밋 및 푸시하여 릴리스 트리거
Team Services에 코드 변경을 체크 인하여 연속 통합 파이프라인이 작동하는지 확인합니다.    

코드를 작성하면 Visual Studio에서 변경 내용을 자동으로 추적합니다. 오른쪽 하단의 상태 표시줄에서 보류 중인 변경 내용 아이콘을 선택하여 로컬 Git 리포지토리로 변경 내용을 커밋합니다.

1. 팀 탐색기의 **변경 내용** 보기에서 업데이트를 설명하는 메시지를 추가하고 변경 내용을 커밋합니다.

    ![변경 내용 커밋 및 푸시](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. 팀 탐색기에서 게시 취소된 변경 내용 상태 표시줄 아이콘 또는 동기화 보기를 선택합니다. **푸시**를 선택하여 Team Services/TFS에서 코드를 업데이트합니다.

    ![변경 내용 커밋 및 푸시](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

Team Services에 변경 내용을 푸시하면 빌드가 자동으로 트리거됩니다.  빌드 정의가 성공적으로 완료되면 릴리스가 자동으로 만들어지고 클러스터에서 작업 업데이트가 시작됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 스트리밍 작업 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 작업에서 사용되는 스트리밍 단위에 대한 청구를 방지합니다. 작업을 나중에 사용하려는 경우 중지하고 나중에 필요할 때 다시 시작할 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음 단계를 사용하여 이 자습서에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다.  
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

Visual Studio용 Azure Stream Analytics 도구를 사용하여 지속적인 통합 및 배포 프로세스를 설정하는 방법에 대해 자세히 알아보려면 CI/CD 파이프라인 설정 문서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Stream Analytics 도구를 사용하여 지속적으로 통합 및 개발](stream-analytics-tools-for-visual-studio-cicd.md)