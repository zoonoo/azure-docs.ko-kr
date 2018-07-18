---
title: Azure DevOps Project를 사용하여 Go lang용 CI/CD 파이프라인 만들기 | 빠른 시작
description: DevOps Project를 사용하면 Azure를 쉽게 시작할 수 있습니다. 빠른 몇 단계로 Azure 서비스에서 Go lang 웹앱을 시작할 수 있습니다.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 77f002e41aa23a635f44e13ce60368da6323b536
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37966986"
---
# <a name="create-a-cicd-pipeline-for-go-with-the-azure-devops-project"></a>Azure DevOps Project를 사용하여 Go용 CI/CD 파이프라인 만들기

**Azure DevOps 프로젝트**를 사용하여 **Go** 응용 프로그램에 대한 CI(연속 통합) 및 CD(지속적인 업데이트)를 구성합니다.  Azure DevOps 프로젝트는 VSTS 빌드 및 릴리스 파이프라인의 초기 구성을 간소화합니다.

Azure 구독이 없으면 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료로 구독을 구할 수 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

Azure DevOps 프로젝트는 VSTS에서 CI/CD 파이프라인을 만듭니다.  **새 VSTS** 계정을 만들거나 **기존 계정**을 사용할 수 있습니다.  또한 Azure DevOps 프로젝트는 선택한 **Azure 구독**에서 **Azure 리소스**를 만듭니다.

1. [Microsoft Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **리소스 만들기** 아이콘을 선택한 다음, **DevOps 프로젝트**를 검색합니다.  **만들기**를 선택합니다.

    ![지속적인 업데이트 시작](_img/azure-devops-project-go/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>샘플 응용 프로그램 및 Azure 서비스 선택

1. **Go** 샘플 응용 프로그램을 선택하고 **다음**을 선택합니다.

1. **간단한 Go 앱**은 기본 프레임워크입니다.  **다음**을 선택합니다. 

1. 이전 단계에서 선택한 응용 프로그램 프레임워크는 배포에 대해 사용 가능한 Azure 서비스 배포 대상의 유형을 나타냅니다.  기본 Azure 서비스에서 나가고 **다음**을 선택합니다.

## <a name="configure-vsts-and-an-azure-subscription"></a>VSTS 및 Azure 구독 구성 

1. **새** VSTS 무료 계정을 만들거나 **기존** 계정을 선택합니다.  VSTS 프로젝트의 **이름**을 선택합니다.  **Azure 구독** 및 **위치**를 선택하고 응용 프로그램의 **이름**을 선택합니다.  작업을 완료하면 **완료**를 선택합니다.

    ![VSTS 정보 입력](_img/azure-devops-project-go/vstsazureinfo.png)

1. 몇 분 안에 **프로젝트 대시보드**가 Azure Portal에 로드됩니다.  샘플 응용 프로그램이 VSTS 계정의 리포지토리에서 설정되고 빌드가 실행되고 응용 프로그램이 Azure에 배포됩니다.  이 대시보드에서는 **코드 리포지토리**, **VSTS CI/CD 파이프라인** 및 **Azure의 응용 프로그램**에 가시성을 제공합니다.  대시보드의 오른쪽에서 **찾아보기**를 선택하여 실행 중인 응용 프로그램을 봅니다.

    ![대시보드 보기](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>코드 변경 내용 커밋 및 CI/CD 실행

Azure DevOps 프로젝트는 VSTS 또는 GitHub 계정에서 Git 리포지토리를 만들었습니다.  리포지토리를 살펴보고 응용 프로그램에 코드를 변경하려면 다음 단계를 수행합니다.

1. DevOps 프로젝트 대시보드의 왼쪽에서 **마스터** 분기에 대한 링크를 선택합니다.  이 링크는 새로 생성된 Git 리포지토리 보기를 엽니다.

1. 리포지토리 복제 URL을 보려면 브라우저의 오른쪽 위에서 **복제**를 선택합니다. 즐겨찾는 IDE에서 Git 리포지토리를 복제할 수 있습니다.  다음 몇 단계에서는 웹 브라우저를 사용하여 코드 변경을 직접 마스터 분기에 만들고 커밋할 수 있습니다.

1. 브라우저의 왼쪽에서 **views/index.html** 파일로 이동합니다.

1. **편집**을 선택하고 일부 텍스트를 변경합니다.  예를 들어 한 div 태그 내부에서 텍스트를 수정합니다.

1. **커밋**을 선택한 다음, 변경 사항을 저장합니다.

1. 브라우저에서 **Azure DevOps 프로젝트 대시보드**로 이동합니다.  이제 빌드가 진행되고 있음을 확인해야 합니다.  변경한 내용은 자동으로 빌드되며 VSTS CI/CD 파이프라인을 통해 배포됩니다.

## <a name="examine-the-vsts-cicd-pipeline"></a>VSTS CI/CD 파이프라인 검사

Azure DevOps 프로젝트는 VSTS 계정에서 전체 VSTS CI/CD 파이프라인을 자동으로 구성했습니다.  필요에 따라 파이프라인을 탐색하고 사용자 지정합니다.  VSTS 빌드 및 릴리스 정의를 숙지하려면 다음 단계를 수행합니다.

1. Azure DevOps 프로젝트 대시보드의 **위쪽**에서 **빌드 파이프라인**을 선택합니다.  이 링크는 브라우저 탭 및 새 프로젝트에 대한 VSTS 빌드 정의를 엽니다.

1. **줄임표**를 선택합니다.  이 작업은 새 빌드 큐, 빌드 일시 중지 및 빌드 정의 편집 같은 여러 활동을 시작할 수 있는 메뉴를 엽니다.

1. **편집**을 선택합니다.

1. 이 보기에서 빌드 정의에 대한 **다양한 작업을 검사합니다**.  빌드는 Git 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 사용된 출력 게시 등 다양한 작업을 실행합니다.

1. 빌드 정의의 위쪽에서 **빌드 정의 이름**을 선택합니다.

1. 빌드 정의의 **이름**을 좀 더 구체적인 것으로 변경합니다.  **저장 및 큐**를 선택한 다음, **저장**을 선택합니다.

1. 빌드 정의 이름 아래에서 **기록**을 선택합니다.  빌드에 대한 최근 변경 내용의 감사 내역이 표시됩니다.  VSTS는 빌드 정의에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

1. **트리거**를 선택합니다.  Azure DevOps 프로젝트는 CI 트리거를 자동으로 생성하면 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다.  필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

1. **보존**을 선택합니다.  시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

1. **빌드 및 릴리스**를 선택한 다음, **릴리스**를 선택합니다.  Azure DevOps 프로젝트는 Azure에 배포를 관리하는 VSTS 릴리스 정의를 만들었습니다.

1. 브라우저의 왼쪽에서 릴리스 정의 옆에 있는 **줄임표**를 선택한 다음, **편집**을 선택합니다.

1. 릴리스 정의에는 릴리스 프로세스를 정의하는 **파이프라인**이 포함됩니다.  **아티팩트** 아래에서 **드롭**을 선택합니다.  이전 단계에서 검사한 빌드 정의는 아티팩트에 사용된 출력을 생성합니다. 

1. **Drop** 아이콘의 오른쪽에서 **지속적인 배포 트리거**를 선택합니다.  이 릴리스 정의는 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 CD 트리거를 사용하도록 설정했습니다.  필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필수적입니다. 

1. 브라우저의 왼쪽에서 **작업**을 선택합니다.  작업은 배포 프로세스가 수행하는 활동입니다.  이 예제에서는 **Azure 앱 서비스**에 배포하기 위해 작업을 만들었습니다.

1. 브라우저의 오른쪽에서 **릴리스 보기**를 선택합니다.  이 보기에는 릴리스의 기록이 표시됩니다.

1. 한 릴리스 옆에 있는 **줄임표**를 선택하고 **열기**를 선택합니다.  릴리스 요약, 연결된 작업 항목 및 테스트 등 여러 메뉴를 이 보기에서 탐색할 수 있습니다.

1. **커밋**을 선택합니다.  이 보기에는 특정 배포와 연결된 코드 커밋이 표시됩니다. 

1. **로그**를 선택합니다.  로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다.  배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 Azure DevOps 프로젝트 대시보드에서 **삭제** 기능을 사용하여 이 빠른 시작에서 만든 Azure 앱 서비스 및 관련 리소스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

팀의 요구에 맞게 빌드 및 릴리스 정의 수정 방법에 대해 자세한 알려면 이 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [CD 프로세스 사용자 지정](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
