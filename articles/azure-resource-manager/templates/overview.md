---
title: 템플릿 개요
description: 리소스 배포에 Azure 리소스 관리자 템플릿을 사용하는 이점에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b3b5fb383ac89d0968a437f35aab656afa1913f0
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086337"
---
# <a name="what-are-arm-templates"></a>ARM 템플릿이란?

클라우드로의 전환과 함께 많은 팀이 민첩한 개발 방법을 채택했습니다. 이 팀은 빠르게 반복됩니다. 솔루션을 클라우드에 반복적으로 배포해야 하며 인프라가 신뢰할 수 있는 상태임을 알아야 합니다. 인프라가 반복 프로세스의 일부가 되면서 운영과 개발 간의 구분이 사라졌습니다. 팀은 통합된 프로세스를 통해 인프라 및 응용 프로그램 코드를 관리해야 합니다.

이러한 과제를 해결하려면 배포를 자동화하고 인프라 연습을 코드로 사용할 수 있습니다. 코드에서 배포해야 하는 인프라를 정의합니다. 인프라 코드는 프로젝트의 일부가 됩니다. 응용 프로그램 코드와 마찬가지로 인프라 코드를 소스 리포지토리에 저장하고 버전화합니다. 팀의 모든 사람이 코드를 실행하고 유사한 환경을 배포할 수 있습니다.

Azure 솔루션에 대한 코드로 인프라를 구현하려면 ARM(Azure Resource Manager) 템플릿을 사용합니다. 템플릿은 프로젝트의 인프라 및 구성을 정의하는 JSON(JavaScript 개체 표기형) 파일입니다. 이 템플릿은 대상을 만들기 위한 프로그래밍 명령 시퀀스를 작성하지 않고도 배포하려는 대상을 설명할 수 있는 선언적 구문입니다. 템플릿에서 배포할 리소스와 해당 리소스에 대한 속성을 지정합니다.

## <a name="why-choose-arm-templates"></a>ARM 템플릿을 선택해야 하는 이유는 무엇입니까?

ARM 템플릿과 다른 인프라 중 하나를 코드 서비스로 사용하는 방법을 결정하려는 경우 템플릿을 사용할 때의 다음 이점을 고려하십시오.

* **선언적 구문**: ARM 템플릿을 사용하면 전체 Azure 인프라를 선언적으로 만들고 배포할 수 있습니다. 예를 들어 가상 시스템뿐만 아니라 네트워크 인프라, 스토리지 시스템 및 필요한 기타 리소스도 배포할 수 있습니다.

* **반복 가능한 결과**: 개발 수명 주기 전반에 걸쳐 인프라를 반복적으로 배포하고 리소스가 일관된 방식으로 배포된다는 확신을 갖습니다. 템플릿은 idempotent이며, 이는 동일한 템플릿을 여러 번 배포하고 동일한 상태에서 동일한 리소스 형식을 얻을 수 있음을 의미합니다. 업데이트를 나타내는 별도의 템플릿을 많이 개발하는 대신 원하는 상태를 나타내는 하나의 템플릿을 개발할 수 있습니다.

* **오케스트레이션**: 주문 작업의 복잡성에 대해 걱정할 필요가 없습니다. 리소스 관리자는 상호 의존적인 리소스의 배포를 오케스트레이션하여 올바른 순서로 만들어집니다. 가능하면 리소스 관리자는 리소스를 병렬로 배포하므로 배포가 직렬 배포보다 빠르게 완료됩니다. 여러 명령 명령을 통하지 않고 하나의 명령을 통해 템플릿을 배포합니다.

   ![템플릿 배포 비교](./media/overview/template-processing.png)

* **모듈식 파일**: 템플릿을 더 작고 재사용 가능한 구성 요소로 분할하고 배포 시 함께 연결할 수 있습니다. 한 템플릿을 다른 템플릿 내에 중첩할 수도 있습니다.

* **Azure 리소스 만들기**: 템플릿에서 새 Azure 서비스 및 기능을 즉시 사용할 수 있습니다. 리소스 공급자가 새 리소스를 도입하는 즉시 템플릿을 통해 해당 리소스를 배포할 수 있습니다. 새 서비스를 사용하기 전에 도구 나 모듈이 업데이트 될 때까지 기다릴 필요가 없습니다.

* **확장성**: [배포 스크립트를](deployment-script-template.md)사용하여 템플릿에 PowerShell 또는 Bash 스크립트를 추가할 수 있습니다. 배포 스크립트는 배포 하는 동안 리소스를 설정 하는 기능을 확장 합니다. 스크립트는 템플릿에 포함하거나 외부 소스에 저장되고 템플릿에서 참조될 수 있습니다. 배포 스크립트를 사용하면 단일 ARM 템플릿에서 종단 간 환경 설정을 완료할 수 있습니다.

* **테스트**: ARM 템플릿 도구 키트 (arm-ttk)로 테스트하여 템플릿이 권장 지침을 따르는지 확인할 수 있습니다. 이 테스트 키트는 [GitHub에서](https://github.com/Azure/arm-ttk)다운로드할 수 있는 PowerShell 스크립트입니다. 이 도구 키트를 사용하면 템플릿 언어를 사용하여 전문 지식을 보다 쉽게 개발할 수 있습니다.

* **변경 미리 보기**: 템플릿을 배포하기 전에 [what-if 작업을](template-deploy-what-if.md) 사용하여 변경 내용을 미리 볼 수 있습니다. what-if를 사용하면 생성, 업데이트 또는 삭제할 리소스와 변경될 리소스 속성을 확인할 수 있습니다. what-if 작업은 환경의 현재 상태를 확인하고 상태를 관리할 필요가 없습니다.

* **기본 제공 유효성 검사**: 템플릿은 유효성 검사를 통과한 후에만 배포됩니다. 리소스 관리자는 배포를 시작하기 전에 템플릿을 검사하여 배포가 성공했는지 확인합니다. 배포가 완료된 상태에서 중지될 가능성이 적습니다.

* **추적된 배포**: Azure 포털에서 배포 기록을 검토하고 템플릿 배포에 대한 정보를 얻을 수 있습니다. 배포된 템플릿, 전달된 매개 변수 값 및 모든 출력 값을 볼 수 있습니다. 코드 서비스와 같은 다른 인프라는 포털을 통해 추적되지 않습니다.

   ![배포 기록](./media/overview/deployment-history.png)

* **코드로서의 정책**: [Azure Policy는](../../governance/policy/overview.md) 거버넌스를 자동화하기 위한 코드 프레임워크로서의 정책입니다. Azure 정책을 사용하는 경우 템플릿을 통해 배포할 때 정책 업데이트가 비호환 리소스에서 수행됩니다.

* **배포 청사진**: Microsoft에서 제공하는 [청사진을](../../governance/blueprints/overview.md) 활용하여 규정 및 규정 준수 표준을 충족할 수 있습니다. 이러한 Blueprint에는 다양한 아키텍처를 위한 미리 빌드된 템플릿이 포함됩니다.

* **CI/CD 통합**: 템플릿을 지속적인 통합 및 지속적인 배포(CI/CD) 도구에 통합하여 릴리스 파이프라인을 자동화하여 빠르고 안정적인 애플리케이션 및 인프라 업데이트를 수행할 수 있습니다. Azure DevOps 및 리소스 관리자 템플릿 작업을 사용하여 Azure 파이프라인을 사용하여 ARM 템플릿 프로젝트를 지속적으로 빌드하고 배포할 수 있습니다. 자세한 내용은 파이프라인 및 자습서를 [사용하여 VS 프로젝트를](add-template-to-azure-pipelines.md) 참조하세요. [Tutorial: Continuous integration of Azure Resource Manager templates with Azure Pipelines](./deployment-tutorial-pipeline.md)

* **내보내기 가능한 코드**: 리소스 그룹의 현재 상태를 내보내거나 특정 배포에 사용되는 템플릿을 확인하여 기존 리소스 그룹에 대한 템플릿을 얻을 수 있습니다. [내보낸 템플릿을](export-template-portal.md) 보는 것은 템플릿 구문에 대해 알아보는 유용한 방법입니다.

* **작성 도구**: Visual Studio [코드](use-vs-code-to-create-template.md) 및 템플릿 도구 확장으로 템플릿을 작성할 수 있습니다. intellisense, 구문 강조 표시, 인라인 도움말 및 기타 여러 언어 함수를 얻을 수 있습니다. Visual Studio 코드 외에도 [Visual Studio](create-visual-studio-deployment-project.md)를 사용할 수도 있습니다.

## <a name="template-file"></a>템플릿 파일

템플릿 내에서 JSON의 기능을 확장하는 [템플릿 표현식을](template-expressions.md) 작성할 수 있습니다. 이러한 식은 리소스 관리자에서 제공하는 [함수를](template-functions.md) 사용합니다.

템플릿에는 다음 섹션이 있습니다.

* [매개 변수](template-parameters.md) - 배포 중에 동일한 템플릿을 다른 환경에서 사용할 수 있도록 값을 제공합니다.

* [변수](template-variables.md) - 템플릿에서 다시 사용되는 값을 정의합니다. 매개 변수 값에서 생성할 수 있습니다.

* [사용자 정의 함수](template-user-defined-functions.md) - 템플릿을 단순화하는 사용자 지정 함수를 만듭니다.

* [리소스](template-syntax.md#resources) - 배포할 리소스를 지정합니다.

* 출력 - 배포된 리소스에서 값을 [반환합니다.](template-outputs.md)

## <a name="template-deployment-process"></a>템플릿 배포 프로세스

템플릿을 배포할 때 리소스 관리자는 템플릿을 REST API 작업으로 변환합니다. 예를 들어 리소스 관리자가 다음 리소스 정의로 템플릿을 받는 경우:

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

* 템플릿을 만드는 과정을 안내하는 단계별 자습서는 [자습서: 첫 번째 ARM 템플릿 만들기 및 배포를](template-tutorial-create-first-template.md)참조하십시오.
* 템플릿 파일의 속성에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해를](template-syntax.md)참조하십시오.
* 템플릿 내보내기에 대해 자세히 알아보려면 [빠른 시작: Azure 포털을 사용하여 ARM 템플릿 만들기 및 배포를](quickstart-create-templates-use-the-portal.md)참조하세요.
