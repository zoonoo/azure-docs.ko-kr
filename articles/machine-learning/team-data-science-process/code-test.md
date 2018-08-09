---
title: UCI 성인 소득 예측 데이터 집합을 활용하여 Azure에서 데이터 과학 코드 테스트 - Team Data Science Process 및 Visual Studio Team Services
description: UCI 성인 소득 예측 데이터를 활용한 데이터 과학 코드 테스트
services: machine-learning, team-data-science-process
documentationcenter: ''
author: weig
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2018
ms.author: weig
ms.openlocfilehash: 46d156ce09b1ebcdcceb27ede6e7fa1595d30da6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439500"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>UCI 성인 소득 예측 데이터 집합을 활용한 데이터 과학 코드 테스트
이 문서에서는 데이터 과학 워크플로에서 코드를 테스트하기 위한 예비 지침을 제공합니다. 이러한 테스트를 통해 데이터 과학자는 체계적이며 효율적인 방식으로 품질 및 예상되는 코드의 결과를 확인할 수 있습니다. 코드 테스트가 어떻게 수행되는지 보여 주기 위해 이전에 게시한 [UCI 성인 소득 데이터 집합을 사용하는 TDSP(Team Data Science Process) 프로젝트](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)를 사용합니다. 

## <a name="introduction-on-code-testing"></a>코드 테스트 소개
“단위 테스트”는 소프트웨어 개발을 위한 오랜 관행입니다. 단, 데이터 과학의 경우 무엇을 의미하는지, 그리고 데이터 과학 수명 주기의 다른 단계에서 코드를 어떻게 테스트해야 하는지는 종종 명확하지 않습니다.

* 데이터 준비
* 데이터 품질 검사
* 모델링
* 모델 배포 

이 문서에서는 “단위 테스트”라는 용어를 “코드 테스트”로 대체합니다. 테스트란 데이터 과학 수명 주기의 특정 단계에 대한 코드가 결과를 “예상대로” 생성하는지 평가하는 데 도움이 되는 함수를 가리킵니다. 테스트를 작성하는 사람은 함수의 결과에 따라 “예상대로”가 무엇인지를 정의합니다(예: 데이터 품질 확인 또는 모델링).

이 문서는 유용한 리소스로 참조를 제공합니다.

## <a name="visual-studio-team-services-for-the-testing-framework"></a>테스트 프레임워크에 대한 Visual Studio Team Services
이 문서에서는 VSTS(Visual Studio Team Services)를 사용하여 테스트를 수행하고 자동화하는 방법을 설명합니다. 다른 도구를 사용할 수도 있습니다. 또한 VSTS 및 빌드 에이전트를 사용하여 자동 빌드를 설정하는 방법을 보여 줍니다. 빌드 에이전트의 경우 Azure DSVM(Data Science Virtual Machines)를 사용합니다.

## <a name="flow-of-code-testing"></a>코드 테스트의 흐름
데이터 과학 프로젝트에서 코드 테스트의 전체 워크플로는 다음과 같습니다. 

![코드 테스트의 흐름 차트](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>자세한 단계

다음 단계를 사용하여 빌드 에이전트와 VSTS를 사용하여 자동화된 빌드 및 코드 테스트를 설정하고 실행합니다.

1. Visual Studio 데스크톱 응용 프로그램에서 프로젝트를 만듭니다.

    ![Visual Studio의 “새 프로젝트 만들기” 화면](./media/code-test/create_project.PNG)

   프로젝트를 만든 후 오른쪽 창에 솔루션 탐색기에서 찾을 수 있습니다.
    
    ![프로젝트 만들기 단계](./media/code-test/create_python_project_in_vs.PNG)

    ![Controllers\HomeController.cs](./media/code-test/solution_explorer_in_vs.PNG)

1. VSTS 프로젝트 코드 리포지토리로 프로젝트 코드를 피드합니다. 

    ![프로젝트 코드 리포지토리](./media/code-test/create_repo.PNG)

1. 데이터 수집, 기능 엔지니어링 및 레이블 열 만들기와 같은 일부 데이터 준비 작업을 완료했다고 가정합니다. 사용자의 코드가 원하는 결과를 생성하는지 확인하려고 합니다. 데이터 처리 코드가 올바르게 작동하는지 여부를 테스트하는 데 사용할 수 있는 일부 코드는 다음과 같습니다.

    * 열 이름이 올바른지 확인합니다.
    
      ![일치하는 열 이름에 대한 코드](./media/code-test/check_column_names.PNG)

    * 응답 수준이 올바른지 확인합니다.

      ![일치하는 수준에 대한 코드](./media/code-test/check_response_levels.PNG)

    * 응답 비율이 적당한지 확인합니다.

      ![응답 비율에 대한 코드](./media/code-test/check_response_percentage.PNG)

    * 데이터에 있는 각 열의 누락 비율을 확인합니다.
    
      ![누락된 속도에 대한 코드](./media/code-test/check_missing_rate.PNG)


1. 데이터 처리 및 기능 엔지니어링 작업을 완료했고 우수한 모델을 학습한 후에는 학습한 모델이 새 데이터 집합을 올바르게 계산하는지 확인합니다. 다음 두 가지 테스트를 사용하여 예측 수준 및 레이블 값의 분포를 확인할 수 있습니다.

    * 예측 수준을 확인합니다.
    
      ![예측 수준 검사에 대한 코드](./media/code-test/check_prediction_levels.PNG)

    * 예측 값의 분포를 확인합니다.

      ![예측 값 검사에 대한 코드](./media/code-test/check_prediction_values.PNG)

1. **test_funcs.py**라는 Python 스크립트에 모든 테스트 함수를 배치합니다.

    ![테스트 함수에 대한 Python 스크립트](./media/code-test/create_file_test_func.PNG)


1. 테스트 코드가 준비되면 Visual Studio에서 테스트 환경을 설정할 수 있습니다.

   **test1.py**라고 하는 Python 파일을 만듭니다. 이 파일에서 수행하려는 모든 테스트를 포함하는 클래스를 만듭니다. 다음 예제에는 준비된 6개의 테스트가 나와 있습니다.
    
    ![클래스의 테스트 목록이 포함된 Python 파일](./media/code-test/create_file_test1_class.PNG)

1. 그러한 테스트는 **codetest.testCase**를 클래스 이름 뒤에 배치하여 자동으로 검색할 수 있습니다. 오른쪽 창에서 테스트 탐색기를 열고 **모두 실행**을 선택합니다. 모든 테스트가 순차적으로 실행되고 테스트가 성공적인지 아닌지를 사용자에게 알립니다.

    ![테스트 실행](./media/code-test/run_tests.PNG)

1. Git 명령을 사용하여 프로젝트 리포지토리에 사용자 코드를 체크 인합니다. 가장 최근 작업이 VSTS에서 즉시 반영됩니다.

    ![코드에서의 검사를 위한 Git 명령](./media/code-test/git_check_in.PNG)

    ![VSTS의 가장 최근 작업](./media/code-test/git_check_in_most_recent_work.PNG)

1. 자동 빌드를 설정하고 VSTS에서 테스트합니다.

    a. 프로젝트 리포지토리에서 **빌드 및 릴리스**를 선택한 다음, **+새로 만들기**를 선택하여 새 빌드 프로세스를 만듭니다.

       ![Selections for starting a new build process](./media/code-test/create_new_build.PNG)

    나. 화면에 표시되는 메시지에 따라 소스 코드 위치, 프로젝트 이름, 리포지토리 및 분기 정보를 선택합니다.
    
       ![Source, name, repository, and branch information](./media/code-test/fill_in_build_info.PNG)

    다. 템플릿을 선택합니다. Python 프로젝트 템플릿이 없으므로 **빈 프로세스**를 선택하여 시작합니다. 

       ![List of templates and "Empty process" button](./media/code-test/start_empty_process_template.PNG)

    d. 빌드에 이름을 지정하고 에이전트를 선택합니다. DSVM을 사용하여 빌드 프로세스를 완료하려면 여기서 기본값을 선택할 수 있습니다. 에이전트 설정에 대한 자세한 내용은 [빌드 및 릴리스 에이전트](https://docs.microsoft.com/vsts/build-release/concepts/agents/agents?view=vsts)를 참조하세요.
    
       ![Build and agent selections](./media/code-test/select_agent.PNG)

    e. 왼쪽 창에서 **+** 를 선택하여 이 빌드 단계에 대한 작업을 추가합니다. 모든 검사를 완료하기 위해 Python 스크립트 **test1.py**를 실행해야 하므로 이 태스크는 PowerShell 명령을 사용하여 Python 코드를 실행합니다.
    
       !["Add tasks" pane with PowerShell selected](./media/code-test/add_task_powershell.PNG)

    f. PowerShell 세부 정보에서 PowerShell의 이름 및 버전 같은 필요한 정보를 입력합니다. 형식으로 **인라인 스크립트**를 선택합니다. 
    
       In the box under **Inline Script**, you can type **python test1.py**. Make sure the environment variable is set up correctly for Python. If you need a different version or kernel of Python, you can explicitly specify the path as shown in the figure: 
    
       ![PowerShell details](./media/code-test/powershell_scripts.PNG)

    g. **저장 및 큐**를 선택하여 빌드 정의 프로세스를 완료합니다.

       !["Save & queue" button](./media/code-test/save_and_queue_build_definition.PNG)

이제 코드 리포지토리에 새 커밋이 푸시될 때마다 빌드 프로세스가 자동으로 시작됩니다. (여기서는 마스터를 리포지토리로 사용하지만, 사용자가 모든 분기를 정의할 수 있습니다.) 프로세스는 코드에 정의된 모든 내용이 제대로 실행되도록 에이전트 컴퓨터에서 **test1.py** 파일을 실행합니다. 

알림이 제대로 설정되어 있는 경우 빌드가 완료되면 사용자에게 이메일로 알립니다. 또한 VSTS에서 빌드 상태를 확인할 수 있습니다. 실패한 경우 빌드의 세부 정보를 확인 수 있으며 끊어진 부분을 찾을 수 있습니다.

![빌드 성공의 이메일 알림](./media/code-test/email_build_succeed.PNG)

![빌드 성공의 VSTS 알림](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>다음 단계
* [UCI 소득 예측 리포지토리](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)를 참조하여 데이터 과학 시나리오에 대한 단위 테스트의 구체적인 예를 확인하세요.
* 앞서 나온 사용자의 데이터 과학 프로젝트에서 UCI 소득 예측 시나리오의 개요 및 예제를 따릅니다.

## <a name="references"></a>참조
* [Team Data Science Process](https://aka.ms/tdsp)
* [Visual Studio 테스트 도구](https://www.visualstudio.com/vs/features/testing-tools/)
* [VSTS 테스트 리소스](https://www.visualstudio.com/team-services/)
* [데이터 과학 가상 머신](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)