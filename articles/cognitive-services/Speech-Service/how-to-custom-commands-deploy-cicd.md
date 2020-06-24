---
title: Azure DevOps (미리 보기)를 사용 하 여 연속 배포
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램에 대 한 연속 배포를 설정 하는 방법에 대해 알아봅니다. 연속 배포 워크플로를 지 원하는 스크립트를 만듭니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: f4dde6831902c0d15d5f985208e382963125d200
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308063"
---
# <a name="continuous-deployment-with-azure-devops"></a>Azure DevOps를 사용 하 여 연속 배포

이 문서에서는 사용자 지정 명령 응용 프로그램에 대 한 연속 배포를 설정 하는 방법에 대해 알아봅니다. CI/CD 워크플로를 지 원하는 스크립트가 제공 됩니다.

## <a name="prerequisite"></a>필수 조건
> [!div class = "checklist"]
> * 개발용 사용자 지정 명령 응용 프로그램 (DEV)
> * 프로덕션에 대 한 사용자 지정 명령 응용 프로그램 (PROD)
> * [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) 등록

## <a name="exportimportpublish"></a>내보내기/가져오기/게시

스크립트는 [Cognitive Services 음성 도우미-사용자 지정 명령](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)에서 호스팅됩니다. Bash 디렉터리의 스크립트를 리포지토리로 복제 합니다. 동일한 경로를 유지 관리 해야 합니다.

### <a name="set-up-a-pipeline"></a>파이프라인 설정 

1. **Azure DevOps-파이프라인** 으로 이동 하 고 "새 파이프라인"을 클릭 합니다.
1. **연결** 섹션에서 이러한 스크립트가 있는 리포지토리의 위치를 선택 합니다.
1. **Select** 섹션에서 리포지토리를 선택 합니다.
1. **구성** 섹션에서 "시작 파이프라인"을 선택 합니다.
1. 다음에는 YAML 파일이 포함 된 편집기를 가져오고 "단계" 섹션을이 스크립트로 바꿉니다.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. 이러한 스크립트는 지역을 사용 하 고 있다고 가정 합니다 .이 경우에는 `westus2` 작업의 인수를 적절 하 게 업데이트 하지 않는 것입니다.

    > [!div class="mx-imgBorder"]
    > ![전송 작업 페이로드](media/custom-commands/cicd-new-pipeline-yaml.png)

1. "저장 후 실행" 단추에서 드롭다운을 열고 "저장"을 클릭 합니다.

### <a name="hook-up-the-pipeline-with-your-application"></a>응용 프로그램과 파이프라인 후크

1. 파이프라인의 기본 페이지로 이동 합니다.
1. 오른쪽 위 모서리 드롭다운에서 **파이프라인 편집**을 선택 합니다. YAML 편집기를 가져옵니다. 
1. "실행" 단추 옆의 오른쪽 위 모서리에서 **변수**를 선택 합니다. **새 변수**를 클릭 합니다.
1. 다음 변수를 추가 합니다.
    
    | 변수 | Description |
    | ------- | --------------- | ----------- |
    | SourceAppId | 개발 응용 프로그램의 ID |
    | TargetAppId | PROD 응용 프로그램의 ID |
    | SubscriptionKey | 두 응용 프로그램 모두에 사용 되는 구독 키 |
    | 문화권 | 응용 프로그램의 문화권 (예: en-us) |

    > [!div class="mx-imgBorder"]
    > ![전송 작업 페이로드](media/custom-commands/cicd-edit-pipeline-variables.png)

1. "실행"을 클릭 한 다음 실행 중인 "작업"을 클릭 합니다. 

    "소스 응용 프로그램 내보내기", "대상 앱으로 가져오기" & "대상 앱 학습 및 게시"를 포함 하는 실행 작업 목록이 표시 됩니다.

## <a name="deploy-from-source-code"></a>소스 코드에서 배포

리포지토리에 응용 프로그램의 정의를 유지 하려는 경우 소스 코드에서 배포 하는 스크립트를 제공 합니다. 스크립트가 bash에 있기 때문에 Windows를 사용 하는 경우 [Linux 하위 시스템](https://docs.microsoft.com/windows/wsl/install-win10)을 설치 해야 합니다.

스크립트는 [Cognitive Services 음성 도우미-사용자 지정 명령](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)에서 호스팅됩니다. Bash 디렉터리의 스크립트를 리포지토리로 복제 합니다. 동일한 경로를 유지 관리 해야 합니다.

### <a name="prepare-your-repository"></a>리포지토리 준비

1. 응용 프로그램에 대 한 디렉터리를 만듭니다 .이 예제에서는 "apps" 라는 하나를 만듭니다.
1. 아래 bash 스크립트의 인수를 업데이트 하 고를 실행 합니다. 응용 프로그램의 대화 상자 모델을 myapp.js파일에 가져옵니다.
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | 인수 | Description |
    | ------- | --------------- | ----------- |
    | region | 응용 프로그램의 지역 (예: westus2)입니다. |
    | subscriptionkey | 음성 리소스의 구독 키입니다. |
    | appId | 내보내려는 사용자 지정 명령의 응용 프로그램 ID입니다. |

1. 이러한 변경 내용을 리포지토리에 푸시합니다.

### <a name="set-up-a-pipeline"></a>파이프라인 설정 

1. **Azure DevOps-파이프라인** 으로 이동 하 고 "새 파이프라인"을 클릭 합니다.
1. **연결** 섹션에서 이러한 스크립트가 있는 리포지토리의 위치를 선택 합니다.
1. **Select** 섹션에서 리포지토리를 선택 합니다.
1. **구성** 섹션에서 "시작 파이프라인"을 선택 합니다.
1. 다음에는 YAML 파일이 포함 된 편집기를 가져오고 "단계" 섹션을이 스크립트로 바꿉니다.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > 이러한 스크립트는 지역 westus2를 사용 하 고 있다고 가정 합니다 .이 경우에는 작업의 인수를 적절 하 게 업데이트 하지 않는 것입니다.

1. "저장 후 실행" 단추에서 드롭다운을 열고 "저장"을 클릭 합니다.

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>대상 응용 프로그램과 파이프라인 후크

1. 파이프라인의 기본 페이지로 이동 합니다.
1. 오른쪽 위 모서리 드롭다운에서 **파이프라인 편집**을 선택 합니다. YAML 편집기를 가져옵니다. 
1. "실행" 단추 옆의 오른쪽 위 모서리에서 **변수**를 선택 합니다. **새 변수**를 클릭 합니다.
1. 다음 변수를 추가 합니다.

    | 변수 | Description |
    | ------- | --------------- | ----------- |
    | TargetAppId | PROD 응용 프로그램의 ID |
    | SubscriptionKey | 두 응용 프로그램 모두에 사용 되는 구독 키 |
    | 문화권 | 응용 프로그램의 문화권 (예: en-us) |

1. "실행"을 클릭 한 다음 실행 중인 "작업"을 클릭 합니다.
    "앱 가져오기" & "학습 및 앱 게시"를 포함 하는 실행 중인 작업 목록이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub의 샘플을 참조 하세요.](https://aka.ms/speech/cc-samples)