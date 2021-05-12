---
title: Azure Resource Manager용 Bicep 언어
description: Azure Resource Manager 템플릿을 통해 Azure에 인프라를 배포하는 데 사용하는 Bicep 언어에 대해 설명합니다.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 74028c682b48a492c2e8f13bef538d1694370cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955910"
---
# <a name="what-is-bicep-preview"></a>Bicep(미리 보기)이란?

Bicep은 Azure 리소스를 선언적으로 배포하기 위한 언어입니다. ARM 템플릿(Azure Resource Manager 템플릿)을 개발하는 데 JSON 대신 Bicep을 사용할 수 있습니다. Bicep은 간결한 구문, 코드 재사용에 대한 향상된 지원 및 향상된 형식 안전성을 제공하여 제작 환경을 간소화합니다. Bicep은 DSL(도메인 특정 언어)이며, 특정 시나리오 또는 도메인에 맞게 설계되었습니다. 애플리케이션 작성을 위한 일반적인 프로그래밍 언어는 아닙니다.

템플릿을 만들기 위한 JSON 구문은 장황할 수 있으며 복잡한 식이 필요합니다. Bicep은 JSON 템플릿의 기능을 잃지 않고 관련 경험을 개선합니다. ARM 템플릿의 JSON에 대한 투명한 추상화입니다. 각 Bicep 파일은 표준 ARM 템플릿으로 컴파일됩니다. ARM 템플릿에서 유효한 리소스 종류, API 버전, 속성은 Bicep 파일에서 유효합니다. 현재 릴리스에는 몇 가지 [알려진 제한 사항](#known-limitations)이 있습니다.

Bicep은 현재 미리 보기로 제공됩니다. 개발 상황을 살펴보려면 [Bicep 프로젝트 리포지토리](https://github.com/Azure/bicep)를 참조하세요.

Bicep에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>시작

Bicep을 시작하려면 [도구를 설치](bicep-install.md)합니다.

도구를 설치한 후 [Bicep 자습서](./bicep-tutorial-create-first-bicep.md)를 사용하세요. 자습서 시리즈는 Bicep의 구조 및 기능을 안내합니다. Bicep 파일을 배포하고 ARM 템플릿을 동등한 Bicep 파일로 변환합니다.

동등한 JSON 및 Bicep 파일을 나란히 보려면 [Bicep 플레이그라운드](https://aka.ms/bicepdemo)를 참조하세요.

Bicep으로 변환하려는 기존 ARM 템플릿이 있는 경우 [JSON과 Bicep 간에 ARM 템플릿 변환](bicep-decompile.md)을 참조하세요.

## <a name="bicep-improvements"></a>Bicep 개선 사항

Bicep은 동등한 JSON에 비해 더 쉽고 간결한 구문을 제공합니다. `[...]` 식을 사용하지 않아도 됩니다. 대신 함수를 직접 호출하고 매개 변수 및 변수에서 값을 가져옵니다. 배포된 각 리소스에 심볼 이름을 지정하여 템플릿에서 해당 리소스를 쉽게 참조할 수 있게 됩니다.

예를 들어 다음 JSON은 리소스 속성에서 출력 값을 반환합니다.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Bicep에서는 동일한 출력 식을 더 쉽게 작성할 수 있습니다. 다음 예제에서는 템플릿 내에 정의된 리소스에 대해 심볼 이름 **publicIP** 를 사용하여 동일한 속성을 반환합니다.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

구문 전체 비교는 [템플릿에 대한 JSON 및 Bicep 비교](compare-template-syntax.md)를 참조하세요.

Bicep은 리소스 간의 종속성을 자동으로 관리합니다. 리소스의 심볼 이름이 다른 리소스 선언에서 사용되는 경우 `dependsOn`을 설정하지 않아도 됩니다.

Bicep을 사용하면 프로젝트를 여러 모듈로 나눌 수 있습니다.

Bicep 파일의 구조는 JSON 템플릿보다 더 유연합니다. 파일의 어디에서나 매개 변수, 변수, 출력을 선언할 수 있습니다. JSON에서는 템플릿의 해당 섹션 내에서 모든 매개 변수, 변수, 출력을 선언해야 합니다.

Bicep에 대한 VS Code 확장은 다양한 유효성 검사 및 IntelliSense를 제공합니다. 예를 들어 확장의 IntelliSense를 사용하여 리소스의 속성을 가져올 수 있습니다.

## <a name="known-limitations"></a>알려진 제한 사항

현재 다음과 같은 제한 사항이 있습니다.

* 복사 루프에서 모드 또는 배치 크기를 설정할 수 없습니다.
* 루프와 조건을 결합할 수 없습니다.
* `['a', 'b', 'c']`와 같은 한 줄 개체와 배열은 지원되지 않습니다.

## <a name="faq"></a>FAQ

**기존 언어를 사용하지 않고 새 언어를 만드는 이유는 무엇인가요?**

Bicep은 새로운 언어가 아닌 기존 ARM 템플릿 언어에 대한 수정 버전으로 간주될 수 있습니다. 구문이 변경되었지만, 핵심 기능과 런타임은 동일하게 유지됩니다.

Bicep을 개발하기 전에는 기존 프로그래밍 언어를 사용하는 것을 고려했습니다. 대상 사용자가 다른 언어를 배우기 시작하는 것보다 Bicep을 배우기가 더 쉬우리라 판단했습니다.

**Terraform 또는 기타 타사 IaC(Infrastructure as Code) 제품에 에너지를 집중하지 않는 이유는 무엇인가요?**

사용자마다 선호하는 구성 언어와 도구가 다릅니다. 모든 도구가 Azure에서 뛰어난 경험을 제공하기를 원합니다. Bicep은 그 노력의 일부입니다.

Terraform을 사용하는 데 만족하는 경우 전환할 이유가 없습니다. Microsoft는 Azure에서 Terraform이 가장 적합한지 확인하기 위해 최선을 다하고 있습니다.

ARM 템플릿을 선택한 고객의 경우 Bicep이 제작 환경을 개선할 것이라 생각합니다. 또한 Bicep은 IaC를 채택하지 않은 고객의 전환에 도움이 됩니다.

**Azure에만 Bicep을 사용할 수 있나요?**

Bicep은 Azure에 완벽한 솔루션을 배포하는 데 초점을 맞춘 DSL입니다. 해당 목표를 충족하려면 Azure 외부에 있는 일부 API를 사용해야 합니다. 해당 시나리오에 대한 확장성 지점을 제공할 예정입니다.

**사용하고 있던 기존 ARM 템플릿은 어떻게 되나요?**

기존 ARM 템플릿은 계속해서 지금까지와 똑같이 작동합니다. ARM 템플릿을 변경할 필요가 없습니다. 기본 ARM 템플릿 JSON 언어를 계속 지원할 것입니다. Bicep 파일은 JSON으로 컴파일되고 해당 JSON은 배포를 위해 Azure에 전송됩니다.

원할 때 [JSON 파일을 Bicep으로 변환](bicep-decompile.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Bicep 자습서](./bicep-tutorial-create-first-bicep.md)로 시작하세요.
