---
title: 템플릿 개요
description: 리소스 배포를 위한 Azure Resource Manager 템플릿을 사용 하는 이점에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b3b5fb383ac89d0968a437f35aab656afa1913f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086337"
---
# <a name="what-are-arm-templates"></a>ARM 템플릿이란?

클라우드로 이동 하면 많은 팀에서 agile 개발 방법을 채택 했습니다. 이러한 팀은 빠르게 반복 됩니다. It는 솔루션을 클라우드에 반복적으로 배포 하 고 인프라가 신뢰할 수 있는 상태 인지를 알고 있어야 합니다. 인프라가 반복적인 프로세스의 일부가 되기 때문에 작업과 개발 간의 분할이 사라졌습니다. 팀은 통합 된 프로세스를 통해 인프라 및 응용 프로그램 코드를 관리 해야 합니다.

이러한 문제를 충족 하기 위해 배포를 자동화 하 고 인프라의 사례를 코드로 사용할 수 있습니다. 코드에서 배포 해야 하는 인프라를 정의 합니다. 인프라 코드는 프로젝트의 일부가 됩니다. 응용 프로그램 코드와 마찬가지로, 인프라 코드를 원본 리포지토리에 저장 하 고 버전을 표시 합니다. 팀의 모든 팀이 코드를 실행 하 고 유사한 환경을 배포할 수 있습니다.

Azure 솔루션에 대 한 코드로 서의 인프라를 구현 하려면 ARM (Azure Resource Manager) 템플릿을 사용 합니다. 템플릿은 프로젝트의 인프라 및 구성을 정의 하는 JSON (JavaScript Object Notation) 파일입니다. 이 템플릿은 대상을 만들기 위한 프로그래밍 명령 시퀀스를 작성하지 않고도 배포하려는 대상을 설명할 수 있는 선언적 구문입니다. 템플릿에서 배포할 리소스 및 해당 리소스에 대 한 속성을 지정 합니다.

## <a name="why-choose-arm-templates"></a>ARM 템플릿을 선택 해야 하는 이유

ARM 템플릿과 다른 인프라 중 하나를 코드 서비스로 사용 하는 방법을 결정 하려는 경우 템플릿 사용의 다음과 같은 이점을 고려 하세요.

* **선언 구문**: ARM 템플릿을 사용 하면 전체 Azure 인프라를 선언적으로 만들고 배포할 수 있습니다. 예를 들어 virtual machines 뿐만 아니라 네트워크 인프라, 저장소 시스템 및 필요한 기타 리소스도 배포할 수 있습니다.

* **반복 가능한 결과**: 개발 수명 주기 전반에 걸쳐 인프라를 반복적으로 배포 하 고 리소스가 일관 된 방식으로 배포 되도록 합니다. 템플릿은 idempotent입니다. 즉, 동일한 템플릿을 여러 번 배포 하 고 동일한 상태에서 동일한 리소스 종류를 가져올 수 있습니다. 업데이트를 나타내는 별도의 여러 템플릿을 개발 하는 대신 원하는 상태를 나타내는 하나의 템플릿을 개발할 수 있습니다.

* **오케스트레이션**: 순서 지정 작업의 복잡성에 대해 걱정 하지 않아도 됩니다. 리소스 관리자은 올바른 순서로 생성 되도록 상호 의존적인 리소스의 배포를 오케스트레이션 합니다. 가능 하면 리소스 관리자는 리소스가 병렬로 배포 되므로 배포가 직렬 배포 보다 더 빨리 완료 됩니다. 여러 명령적 명령을 통해서가 아니라 명령 하나를 통해 템플릿을 배포 합니다.

   ![템플릿 배포 비교](./media/overview/template-processing.png)

* **모듈식 파일**: 템플릿을 더 작고 재사용 가능한 구성 요소로 나누고 배포 시 함께 연결할 수 있습니다. 하나의 템플릿을 다른 템플릿 내에 중첩할 수도 있습니다.

* **Azure 리소스 만들기**: 템플릿에서 새로운 azure 서비스 및 기능을 즉시 사용할 수 있습니다. 리소스 공급자가 새 리소스를 도입 하는 즉시 템플릿을 통해 해당 리소스를 배포할 수 있습니다. 새 서비스를 사용 하기 전에 도구 또는 모듈이 업데이트 될 때까지 기다릴 필요가 없습니다.

* **확장성**: [배포 스크립트](deployment-script-template.md)를 사용 하 여 템플릿에 PowerShell 또는 Bash 스크립트를 추가할 수 있습니다. 배포 스크립트는 배포 하는 동안 리소스를 설정 하는 기능을 확장 합니다. 스크립트를 템플릿에 포함 하거나 외부 원본에 저장 하 고 템플릿에서 참조할 수 있습니다. 배포 스크립트는 단일 ARM 템플릿에서 종단 간 환경 설정을 완료 하는 기능을 제공 합니다.

* **테스트**: arm 템플릿 도구 키트 (arm-ttk)로 테스트 하 여 템플릿이 권장 지침을 따르는지 확인할 수 있습니다. 이 테스트 키트는 [GitHub](https://github.com/Azure/arm-ttk)에서 다운로드할 수 있는 PowerShell 스크립트입니다. 도구 키트를 사용 하면 템플릿 언어를 사용 하 여 전문 지식을 쉽게 개발할 수 있습니다.

* **변경 내용 미리 보기**: 템플릿을 배포 하기 전에 [가상 작업](template-deploy-what-if.md) 을 사용 하 여 변경 내용 미리 보기를 가져올 수 있습니다. 가상의 경우 생성, 업데이트 또는 삭제 되는 리소스와 변경 될 리소스 속성을 볼 수 있습니다. 가상 작업에서 환경의 현재 상태를 확인 하 고 상태를 관리할 필요가 없습니다.

* **기본 제공 유효성 검사**: 템플릿은 유효성 검사를 통과 한 후에만 배포 됩니다. 리소스 관리자 배포를 시작 하기 전에 템플릿을 확인 하 여 배포에 성공 하는지 확인 합니다. 배포는 반 완료 상태에서 중지 될 가능성이 적습니다.

* **추적 된 배포**: Azure Portal에서 배포 기록을 검토 하 고 템플릿 배포에 대 한 정보를 가져올 수 있습니다. 배포 된 템플릿, 전달 된 매개 변수 값 및 출력 값을 볼 수 있습니다. 코드 서비스를 비롯 한 다른 인프라는 포털을 통해 추적 되지 않습니다.

   ![배포 기록](./media/overview/deployment-history.png)

* **코드를 코드로**: [Azure Policy](../../governance/policy/overview.md) 는 거 버 넌 스를 자동화 하는 코드 프레임 워크로 서의 정책입니다. Azure 정책을 사용 하는 경우 템플릿을 통해 배포 되는 경우 비규격 리소스에서 정책 관리가 수행 됩니다.

* **배포 청사진**: 규정 및 규정 준수 표준을 충족 하기 위해 Microsoft에서 제공 하는 [청사진](../../governance/blueprints/overview.md) 을 활용할 수 있습니다. 이러한 청사진에는 다양 한 아키텍처를 위한 미리 빌드된 템플릿이 포함 되어 있습니다.

* **Ci/cd 통합**: 템플릿을 통합 및 CI/cd (연속 통합 및 연속 배포) 도구에 통합 하 여 빠르고 안정적인 응용 프로그램 및 인프라 업데이트를 위한 릴리스 파이프라인을 자동화할 수 있습니다. Azure DevOps 및 리소스 관리자 템플릿 작업을 사용 하 여 Azure Pipelines를 사용 하 여 ARM 템플릿 프로젝트를 지속적으로 빌드 및 배포할 수 있습니다. 자세한 내용은 [파이프라인을 사용한 VS 프로젝트](add-template-to-azure-pipelines.md) 및 [자습서: Azure Pipelines를 사용 하 여 Azure Resource Manager 템플릿 연속 통합](./deployment-tutorial-pipeline.md)(영문)을 참조 하세요.

* **내보낼**수 있는 코드: 리소스 그룹의 현재 상태를 내보내거나 특정 배포에 사용 된 템플릿을 확인 하 여 기존 리소스 그룹에 대 한 템플릿을 가져올 수 있습니다. [내보낸 템플릿](export-template-portal.md) 보기는 템플릿 구문에 대해 알아보는 데 유용한 방법입니다.

* **작성 도구**: [Visual Studio Code](use-vs-code-to-create-template.md) 및 템플릿 도구 확장을 사용 하 여 템플릿을 제작할 수 있습니다. Intellisense, 구문 강조 표시, 온라인 도움말 및 기타 많은 언어 함수를 얻을 수 있습니다. Visual Studio code 외에도 [Visual studio](create-visual-studio-deployment-project.md)를 사용할 수 있습니다.

## <a name="template-file"></a>템플릿 파일

템플릿 내에서 JSON의 기능을 확장 하는 [템플릿 식을](template-expressions.md) 작성할 수 있습니다. 이러한 식은 리소스 관리자에서 제공 하는 [함수](template-functions.md) 를 사용 합니다.

템플릿에는 다음과 같은 섹션이 있습니다.

* [매개 변수](template-parameters.md) -배포 하는 동안 다른 환경에서 동일한 템플릿을 사용할 수 있도록 하는 값을 제공 합니다.

* [Variables](template-variables.md) -템플릿에서 다시 사용 되는 값을 정의 합니다. 매개 변수 값에서 생성 될 수 있습니다.

* [사용자 정의 함수](template-user-defined-functions.md) -템플릿을 간소화 하는 사용자 지정 함수를 만듭니다.

* [리소스](template-syntax.md#resources) -배포할 리소스를 지정 합니다.

* [출력](template-outputs.md) -배포 된 리소스의 값을 반환 합니다.

## <a name="template-deployment-process"></a>템플릿 배포 프로세스

템플릿을 배포할 때 리소스 관리자 템플릿을 REST API 작업으로 변환 합니다. 예를 들어 리소스 관리자가 다음 리소스 정의로 템플릿을 받는 경우:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Microsoft.Storage 리소스 공급자에게 전송되는 다음 REST API 작업으로 정의를 변환합니다.

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>템플릿 디자인

템플릿 및 리소스 그룹을 정의하는 방법은 사용자 및 솔루션을 관리하려는 방법에 전적으로 달려 있습니다. 예를 들어 단일 템플릿을 통해 3계층 애플리케이션을 단일 리소스 그룹에 배포할 수 있습니다.

![3계층 템플릿](./media/overview/3-tier-template.png)

그러나 단일 템플릿에서 전체 인프라를 정의할 필요가 없습니다. 대부분 배포 요구 사항을 대상, 목적에 특정 템플릿 집합으로 나누는 것이 좋습니다. 서로 다른 솔루션에 이러한 템플릿을 쉽게 다시 사용할 수 있습니다. 특정 솔루션을 배포하려면 모든 필수 템플릿에 연결하는 마스터 템플릿을 만듭니다. 다음 이미지는 세 개의 중첩된 템플릿을 포함하는 부모 템플릿을 통해 3계층 솔루션을 배포하는 방법을 보여 줍니다.

![중첩된 계층 템플릿](./media/overview/nested-tiers-template.png)

계층이 별도 수명 주기를 갖도록 계획하는 경우 3계층을 별도 리소스 그룹에 배포할 수 있습니다. 리소스는 다른 리소스 그룹의 리소스에 계속해서 연결될 수 있습니다.

![계층 템플릿](./media/overview/tier-templates.png)

중첩된 템플릿에 대한 자세한 내용은 [Azure Resource Manager에서 연결된 템플릿 사용](linked-templates.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 템플릿을 만드는 과정을 안내 하는 단계별 자습서는 [자습서: 첫 번째 ARM 템플릿 만들기 및 배포](template-tutorial-create-first-template.md)를 참조 하세요.
* 템플릿 파일의 속성에 대 한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
* 템플릿 내보내기에 대 한 자세한 내용은 [빠른 시작: Azure Portal 사용 하 여 ARM 템플릿 만들기 및 배포](quickstart-create-templates-use-the-portal.md)를 참조 하세요.
