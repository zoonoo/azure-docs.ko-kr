---
title: Synapse 작업 영역에 대 한 지속적인 통합 및 배달
description: 연속 통합 및 배달을 사용 하 여 작업 영역의 변경 내용을 한 환경 (개발, 테스트, 프로덕션)에서 다른 환경으로 배포 하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: ba5286b16b6e640e968b50174e39a05328e750a4
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797305"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure Synapse 작업 영역에 대 한 지속적인 통합 및 전달

## <a name="overview"></a>개요

CI (연속 통합)는 팀 멤버가 변경 내용을 버전 제어에 커밋할 때마다 코드의 빌드 및 테스트를 자동화 하는 프로세스입니다. CD (연속 배포)는 여러 테스트 또는 스테이징 환경에서 프로덕션 환경으로 빌드, 테스트, 구성 및 배포 하는 프로세스입니다.

Azure Synapse 작업 영역의 경우 CI/CD (지속적인 통합 및 배달)는 모든 엔터티를 한 환경 (개발, 테스트, 프로덕션)에서 다른 환경으로 이동 합니다. 작업 영역을 다른 작업 영역으로 승격 하려면 두 부분으로 구성 됩니다. 작업 영역 리소스 (풀 및 작업 영역)를 만들거나 업데이트 하려면 [Azure Resource Manager 템플릿을](../../azure-resource-manager/templates/overview.md) 사용 합니다. Azure DevOps의 Synapse CI/CD 도구를 사용 하 여 아티팩트 (SQL 스크립트, 노트북, Spark 작업 정의, 파이프라인, 데이터 집합, 데이터 흐름 등)를 마이그레이션합니다. 

이 문서에서는 Azure 릴리스 파이프라인을 사용 하 여 여러 환경에 Synapse 작업 영역의 배포를 자동화 하는 방법을 간략하게 설명 합니다.

## <a name="prerequisites"></a>필수 조건

-   개발에 사용 되는 작업 영역이 스튜디오에서 Git 리포지토리로 구성 되었습니다. [Synapse Studio의 소스 제어](source-control.md)를 참조 하세요.
-   Azure DevOps 프로젝트가 릴리스 파이프라인을 실행할 준비가 되었습니다.

## <a name="set-up-a-release-pipelines"></a>릴리스 파이프라인 설정

1.  [Azure DevOps](https://dev.azure.com/)에서 릴리스에 대해 만든 프로젝트를 엽니다.

1.  페이지의 왼쪽에서 **파이프라인** 을 선택한 다음 **릴리스** 를 선택합니다.

    ![파이프라인, 릴리스 선택](media/create-release-1.png)

1.  **새 파이프라인** 을 선택하거나, 기존 파이프라인이 있는 경우에는 **새로 만들기** 를 선택한 다음 **새 릴리스 파이프라인** 을 선택합니다.

1.  **빈 작업** 템플릿을 선택합니다.

    ![빈 작업 선택](media/create-release-select-empty.png)

1.  **단계 이름** 상자에 사용자 환경의 이름을 입력합니다.

1.  **아티팩트 추가** 를 선택한 다음 Development Synapse Studio를 사용 하 여 구성 된 git 리포지토리를 선택 합니다. 풀 및 작업 영역의 ARM 템플릿을 관리 하는 데 사용한 git 리포지토리를 선택 합니다. GitHub를 원본으로 사용 하는 경우 GitHub 계정 및 풀 리포지토리에 대 한 서비스 연결을 만들어야 합니다. [서비스 연결](/azure/devops/pipelines/library/service-endpoints) 에 대 한 자세한 내용은 

    ![게시 분기 추가](media/release-creation-github.png)

1.  리소스 업데이트를 위한 ARM 템플릿 분기를 선택 합니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    ![ARM 템플릿 추가](media/release-creation-arm-branch.png)

1.  **기본 분기** 에 대한 리포지토리의 [게시 분기](source-control.md#configure-publishing-settings)를 선택합니다. 기본적으로 이 게시 분기는 `workspace_publish`입니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    ![아티팩트 추가](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>ARM 리소스 만들기 및 업데이트에 대 한 스테이지 작업 설정 

작업 영역 및 풀을 포함 하 여 리소스를 만들거나 업데이트 하는 Azure Resource Manager 배포 작업을 추가 합니다.

1. 단계 보기에서 **단계 작업 보기** 를 선택합니다.

    ![단계 보기](media/release-creation-stage-view.png)

1. 새 작업을 만듭니다. **ARM 템플릿 배포** 를 검색 한 다음 **추가** 를 선택 합니다.

1. 배포 작업에서 대상 작업 영역에 대 한 구독, 리소스 그룹 및 위치를 선택 합니다. 필요한 경우 자격 증명을 제공합니다.

1. **작업** 목록에서 **리소스 그룹 만들기 또는 업데이트** 를 선택합니다.

1. **템플릿** 상자 옆에 있는 줄임표 단추( **...** )를 선택합니다. 대상 작업 영역의 Azure Resource Manager 템플릿 찾아보기

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수** 상자 옆에서 매개 변수 파일을 선택합니다.

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 재정의** 상자 옆에 대상 작업 영역에 대 한 원하는 매개 변수 값을 입력 합니다. 

1. **배포 모드** 에 대해 **증분** 을 선택합니다.
    
    ![작업 영역 및 풀 배포](media/pools-resource-deploy.png)

1. 필드 권한 부여 및 업데이트 작업 영역 역할 할당에 대 한 **Azure PowerShell** 를 추가 합니다. 릴리스 파이프라인을 사용 하 여 Synapse 작업 영역을 만드는 경우 파이프라인의 서비스 주체가 기본 작업 영역 관리자로 추가 됩니다. PowerShell을 실행 하 여 다른 계정에 작업 영역에 대 한 액세스 권한을 부여할 수 있습니다. 
    
    ![권한 부여](media/release-creation-grant-permission.png)

 > [!WARNING]
> 전체 배포 모드에서는 리소스 그룹에 있지만 새 리소스 관리자 템플릿에 지정 되지 않은 리소스는 **삭제** 됩니다. 자세한 내용은 [Azure Resource Manager 배포 모드](../../azure-resource-manager/templates/deployment-modes.md) 를 참조 하세요.

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>아티팩트 배포에 대 한 단계 작업 설정 

[Synapse workspace deployment](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) extension을 사용 하 여 데이터 집합, SQL 스크립트, 노트북, spark 작업 정의, 데이터 흐름, 파이프라인, 연결 된 서비스, 자격 증명 및 IR (Integration Runtime)와 같은 Synapse 작업 영역에 다른 항목을 배포 합니다.  

1. **Azure DevOps marketplace** 에서 확장을 검색 하 고 가져옵니다 (https://marketplace.visualstudio.com/azuredevops) 

     ![확장 가져오기](media/get-extension-from-market.png)

1. 확장을 설치할 조직을 선택 합니다. 

     ![확장 설치](media/install-extension.png)

1. Azure DevOps 파이프라인의 서비스 원칙이 구독 권한이 부여 되 고 대상 작업 영역에 대 한 작업 영역 관리자로 할당 되었는지 확인 합니다. 

1. 새 작업을 만듭니다. **Synapse 작업 영역 배포** 를 검색 한 다음 **추가** 를 선택 합니다.

     ![확장 추가](media/add-extension-task.png)

1.  작업 **에서 ...를 선택 합니다** . **템플릿 상자 옆** 의 템플릿 파일을 선택 합니다.

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수** 상자 옆에서 매개 변수 파일을 선택합니다.

1. 연결, 리소스 그룹 및 대상 작업 영역의 이름을 선택 합니다. 

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 재정의** 상자 옆에 대상 작업 영역에 대 한 원하는 매개 변수 값을 입력 합니다. 

    ![Synapse 작업 영역 배포](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> CI/CD 시나리오에서는 서로 다른 환경에서의 IR(통합 런타임) 형식이 동일해야 합니다. 예를 들어 개발 환경에 자체 호스팅 IR이 있는 경우 테스트 및 프로덕션과 같은 다른 환경에서 동일한 IR이 자체 호스팅 유형이어야 합니다. 마찬가지로 여러 단계에서 통합 런타임을 공유하는 경우 개발, 테스트 및 프로덕션과 같은 모든 환경에서 통합 런타임을 연결된 자체 호스팅으로 구성해야 합니다.

## <a name="create-release-for-deployment"></a>배포용 릴리스 만들기 

모든 변경 내용을 저장 한 후 **릴리스 만들기** 를 선택 하 여 수동으로 릴리스를 만들 수 있습니다. 릴리스 만들기를 자동화하려면 [Azure DevOps 릴리스 트리거](/azure/devops/pipelines/release/triggers)를 참조하세요.

   ![릴리스 만들기 선택](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

Synapse 작업 영역에서 Git 통합을 사용 하 고 변경 내용을 개발에서 테스트로 이동 하는 CI/CD 파이프라인을 사용 하는 경우 다음 모범 사례를 따르는 것이 좋습니다.

-   **Git 통합**. Git 통합을 사용 하 여 development Synapse 작업 영역을 구성 합니다. 테스트 및 프로덕션 작업 영역에 대 한 변경 내용은 CI/CD를 통해 배포 되며 Git 통합이 필요 하지 않습니다.
-   **아티팩트 마이그레이션 전에 풀을 준비** 합니다. 개발 작업 영역의 풀에 연결 된 SQL 스크립트 또는 노트북이 있는 경우 다른 환경에서 동일한 풀 이름이 필요 합니다. 
-   **IaC (Infrastructure As Code)**. 설명 모델에서 인프라 (네트워크, 가상 컴퓨터, 부하 분산 장치 및 연결 토폴로지)를 관리 하는 것은 DevOps 팀에서 소스 코드에 대해 사용 하는 것과 동일한 버전 관리를 사용 합니다. 
-   **기타**. [ADF 아티팩트의 모범 사례를](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd) 참조 하세요.

## <a name="troubleshooting-artifacts-deployment"></a>아티팩트 배포 문제 해결 

### <a name="use-the-synapse-workspace-deployment-task"></a>Synapse workspace 배포 작업 사용

Synapse에서 모든 종류의 아티팩트는 ADF와는 다른 ARM 리소스가 아닙니다. ARM 템플릿 배포 작업을 사용 하 여 Synapse 아티팩트를 배포할 수 없습니다.
 
### <a name="unexpected-token-error-in-release"></a>릴리스에 예기치 않은 토큰 오류가 있습니다.

매개 변수 파일에 이스케이프 되지 않은 매개 변수 값이 있는 경우 릴리스 파이프라인은 예기치 않은 토큰 오류로 인해 파일을 구문 분석 하지 못합니다. 매개 변수를 가져오려면 매개 변수를 재정의 하거나 KeyVault를 재정의 하는 것이 좋습니다. 해결 방법으로 두 번 이스케이프할 수도 있습니다.