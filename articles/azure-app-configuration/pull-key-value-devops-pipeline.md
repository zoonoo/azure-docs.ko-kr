---
title: Azure Pipelines를 사용 하 여 Pull settingsmo 앱 구성
description: Azure Pipelines를 사용 하 여 키-값을 앱 구성 저장소로 가져오는 방법에 대해 알아봅니다.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 4d80c034ab03fed6b9ae2ed0c4c0420afe6f7e32
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728094"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines를 사용 하 여 앱 구성으로 설정 끌어오기

[Azure 앱 구성](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) 작업은 앱 구성 저장소에서 키-값을 끌어오고 Azure 파이프라인 변수로 설정 합니다 .이 변수는 후속 작업에서 사용 될 수 있습니다. 이 작업은 구성 파일의 키-값을 앱 구성 저장소로 푸시하는 [Azure 앱 구성 푸시](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) 작업을 보완 합니다. 자세한 내용은 [Azure Pipelines를 사용 하 여 앱 구성에 설정 밀어넣기](push-kv-devops-pipeline.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- 앱 구성 저장소- [Azure Portal](https://portal.azure.com)에서 무료로 무료로 만듭니다.
- Azure DevOps 프로젝트- [무료로 하나 만들기](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure 앱 구성 작업- [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.)에서 무료로 다운로드 합니다.  

## <a name="create-a-service-connection"></a>서비스 연결 만들기

[서비스 연결](/azure/devops/pipelines/library/service-endpoints) 을 사용 하 여 Azure devops 프로젝트에서 azure 구독의 리소스에 액세스할 수 있습니다.

1. Azure DevOps에서 대상 파이프라인이 포함 된 프로젝트로 이동 하 여 왼쪽 아래에서 **프로젝트 설정을** 엽니다.
1. **파이프라인** 아래에서 **서비스 연결** 을 선택 합니다.
1. 기존 서비스 연결이 없는 경우 화면 중간에서 **서비스 연결 만들기** 단추를 클릭 합니다. 그렇지 않으면 페이지의 오른쪽 위에서 **새 서비스 연결** 을 클릭 합니다.
1. **Azure Resource Manager** 를 선택 합니다.
1. **서비스 사용자 (자동)** 를 선택 합니다.
1. 구독 및 리소스를 입력 합니다. 서비스 연결에 이름을 지정 합니다.

이제 서비스 연결이 만들어졌으므로 할당 된 서비스 사용자의 이름을 찾습니다. 다음 단계에서이 서비스 사용자에 게 새 역할 할당을 추가 합니다.

1. **프로젝트 설정**  >  **서비스 연결** 로 이동 합니다.
1. 이전 섹션에서 만든 서비스 연결을 선택 합니다.
1. **서비스 사용자 관리** 를 선택 합니다.
1. **표시 이름이 표시** 됩니다.

## <a name="add-role-assignment"></a>역할 할당 추가

태스크가 앱 구성 저장소에 액세스할 수 있도록 작업 내에서 사용 되는 서비스 연결에 적절 한 앱 구성 역할을 할당 합니다.

1. 대상 앱 구성 저장소로 이동 합니다. 앱 구성 저장소를 설정 하는 연습은 Azure 앱 구성 빠른 시작 중 하나에서 [앱 구성 저장소 만들기](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) 를 참조 하세요.
1. 왼쪽에서 **Access control (IAM)** 을 선택 합니다.
1. 위쪽에서 **+ 추가** 를 선택 하 고 **역할 할당 추가** 를 선택 합니다.
1. **역할** 아래에서 **앱 구성 데이터 판독기** 를 선택 합니다. 이 역할을 통해 작업은 앱 구성 저장소에서 읽을 수 있습니다. 
1. 이전 섹션에서 만든 서비스 연결과 관련 된 서비스 주체를 선택 합니다.

> [!NOTE]
> 앱 구성 내에서 Azure Key Vault 참조를 해결 하려면 참조 된 Azure 키 자격 증명 모음에서 암호를 읽을 수 있는 권한도 서비스 연결에 부여 되어야 합니다.
  
## <a name="use-in-builds"></a>빌드에 사용

이 섹션에서는 Azure DevOps 빌드 파이프라인에서 Azure 앱 구성 작업을 사용 하는 방법을 설명 합니다.

1. **파이프라인** 파이프라인을 클릭 하 여 빌드 파이프라인 페이지로 이동  >  합니다. 빌드 파이프라인 설명서는  [첫 번째 파이프라인 만들기](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=net%2Ctfs-2018-2%2Cbrowser)를 참조 하세요.
      - 새 빌드 파이프라인을 만드는 경우 **새 파이프라인** 을 클릭 하 고 파이프라인에 대 한 리포지토리를 선택 합니다. 파이프라인의 오른쪽에서 **보조자 표시** 를 선택 하 고 **Azure 앱 구성** 작업을 검색 합니다.
      - 기존 빌드 파이프라인을 사용 하는 경우 **편집** 을 선택 하 여 파이프라인을 편집 합니다. **작업** 탭에서 **Azure 앱 구성** 작업을 검색 합니다.
1. 작업에 필요한 매개 변수를 구성 하 여 앱 구성 저장소에서 키-값을 끌어옵니다. 매개 변수에 대 한 설명은 아래의 **매개 변수** 섹션 및 각 매개 변수 옆에 있는 도구 설명에서 사용할 수 있습니다.
      - **Azure subscription** 매개 변수를 이전 단계에서 만든 서비스 연결의 이름으로 설정 합니다.
      - 앱 구성 **이름을** 앱 구성 저장소의 리소스 이름으로 설정 합니다.
      - 나머지 매개 변수의 기본값은 그대로 둡니다.
1. 빌드를 저장 하 고 큐에 대기 합니다. 빌드 로그에는 태스크를 실행 하는 동안 발생 한 모든 오류가 표시 됩니다.

## <a name="use-in-releases"></a>릴리스에 사용

이 섹션에서는 Azure DevOps 릴리스 파이프라인에서 Azure 앱 구성 작업을 사용 하는 방법을 설명 합니다.

1. **파이프라인** 릴리스를 선택 하 여 릴리스 파이프라인 페이지로 이동  >  합니다. 릴리스 파이프라인 설명서는 [릴리스 파이프라인](/azure/devops/pipelines/release?view=azure-devops)을 참조 하세요.
1. 기존 릴리스 파이프라인을 선택 합니다. 없는 경우 **새 파이프라인** 을 클릭 하 여 새 파이프라인을 만듭니다.
1. 오른쪽 위 모서리에서 **편집** 단추를 선택 하 여 릴리스 파이프라인을 편집 합니다.
1. **단계** 를 선택 하 여 작업을 추가 합니다. 단계에 대 한 자세한 내용은 [단계, 종속성, & 조건 추가](/azure/devops/pipelines/release/environments?view=azure-devops)를 참조 하세요.
1. **+**"에이전트에서 실행"에서를 클릭 한 다음 **작업 추가** 탭에서 **Azure 앱 구성** 작업을 추가 합니다.
1. 작업 내에서 필요한 매개 변수를 구성 하 여 앱 구성 저장소에서 키 값을 끌어옵니다. 매개 변수에 대 한 설명은 아래의 **매개 변수** 섹션 및 각 매개 변수 옆에 있는 도구 설명에서 사용할 수 있습니다.
      - **Azure subscription** 매개 변수를 이전 단계에서 만든 서비스 연결의 이름으로 설정 합니다.
      - 앱 구성 **이름을** 앱 구성 저장소의 리소스 이름으로 설정 합니다.
      - 나머지 매개 변수의 기본값은 그대로 둡니다.
1. 릴리스를 저장 하 고 큐에 대기 합니다. 릴리스 로그에는 태스크를 실행 하는 동안 발생 한 모든 오류가 표시 됩니다.

## <a name="parameters"></a>매개 변수

다음 매개 변수는 Azure 앱 구성 작업에 사용 됩니다.

- **Azure 구독**: 사용 가능한 azure 서비스 연결을 포함 하는 드롭 다운입니다. 사용 가능한 Azure 서비스 연결 목록을 업데이트 하 고 새로 고치려면 텍스트 상자의 오른쪽에 있는 **azure 구독 새로 고침** 단추를 누릅니다.
- **앱 구성 이름**: 선택한 구독에서 사용 가능한 구성 저장소를 로드 하는 드롭다운입니다. 사용 가능한 구성 저장소 목록을 업데이트 하 고 새로 고치려면 텍스트 상자의 오른쪽에 있는 **앱 구성 이름 새로 고침** 단추를 누릅니다.
- **키 필터**: 필터를 사용 하 여 Azure 앱 구성에서 요청 되는 키 값을 선택할 수 있습니다. 값 *은 모든 키 값을 선택 합니다. 에 대 한 자세한 내용은 [키 값 쿼리](concept-key-value.md#query-key-values)를 참조 하세요.
- **레이블**: 앱 구성 저장소에서 키-값을 선택할 때 사용 해야 하는 레이블을 지정 합니다. 레이블이 제공 되지 않으면 레이블이 없는 키-값이 검색 됩니다. , * 문자는 허용 되지 않습니다.
- **Trim Key Prefix**: 변수를 변수로 설정 하기 전에 앱 구성 키에서 잘라내야 하는 접두사를 하나 이상 지정 합니다. 여러 접두사는 줄 바꿈 문자로 구분할 수 있습니다.

## <a name="use-key-values-in-subsequent-tasks"></a>후속 작업에서 키-값 사용

앱 구성에서 인출 되는 키 값은 환경 변수로 액세스할 수 있는 파이프라인 변수로 설정 됩니다. 환경 변수의 키는 접두사 (지정 된 경우)를 지운 후 앱 구성에서 검색 되는 키-값의 키입니다.

예를 들어 후속 태스크가 PowerShell 스크립트를 실행 하는 경우 다음과 같이 ' myBuildSetting ' 키를 포함 하는 키-값을 사용할 수 있습니다.
```powershell
echo "$env:myBuildSetting"
```
그리고 값이 콘솔에 출력 됩니다.

> [!NOTE]
> 앱 구성 내의 Azure Key Vault 참조는 확인 되 고 [비밀 변수로](/azure/devops/pipelines/process/variables#secret-variables)설정 됩니다. Azure 파이프라인에서 비밀 변수는 로그에서 마스킹할 수 있습니다. 이러한 작업은 환경 변수로 작업에 전달 되지 않으며 대신 입력으로 전달 되어야 합니다. 

## <a name="troubleshooting"></a>문제 해결

예기치 않은 오류가 발생 하는 경우 파이프라인 변수를로 설정 하 여 디버그 로그를 사용 하도록 설정할 수 있습니다 `system.debug` `true` .

## <a name="faq"></a>FAQ

**여러 키와 레이블에서 내 구성을 작성 어떻게 할까요??**

여러 레이블에서 구성 해야 하는 경우가 있습니다 (예: default 및 dev). 여러 앱 구성 작업을 하나의 파이프라인에서 사용 하 여이 시나리오를 구현할 수 있습니다. 이후 단계에서 작업을 통해 인출 되는 키 값은 이전 단계의 모든 값을 대체 합니다. 앞의 예제에서 작업은 기본 레이블로 키-값을 선택 하는 데 사용할 수 있으며, 두 번째 작업은 dev 레이블을 사용 하 여 키-값을 선택할 수 있습니다. Dev 레이블을 사용 하는 키는 동일한 키를 기본 레이블로 재정의 합니다.