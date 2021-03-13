---
title: Azure Resource Manager 템플릿에 대 한 Bicep 언어
description: Azure Resource Manager 템플릿을 통해 Azure에 인프라를 배포 하기 위한 Bicep 언어에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 599cb378da51c5d13e7db3cf45cacf750c5843cc
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419833"
---
# <a name="what-is-bicep-preview"></a>Bicep (미리 보기) 란?

Bicep은 Azure 리소스를 선언적으로 배포 하기 위한 언어입니다. Azure Resource Manager 템플릿 (ARM 템플릿)을 개발 하는 데 JSON 대신 Bicep를 사용할 수 있습니다. Bicep은 간결한 구문, 코드 재사용에 대 한 향상 된 지원 및 향상 된 형식 안전성을 제공 하 여 제작 환경을 간소화 합니다. Bicep은 DSL (도메인별 언어) 이며, 특정 시나리오 또는 도메인에 맞게 설계 되었습니다. 응용 프로그램 작성을 위한 일반적인 프로그래밍 언어인 것은 아닙니다.

템플릿을 만들기 위한 JSON 구문은 자세한 정보를 사용할 수 있으며 복잡 한 식이 필요 합니다. Bicep는 JSON 템플릿의 기능을 잃지 않고이 환경을 개선 합니다. ARM 템플릿의 JSON에 대 한 투명 한 추상화입니다. 각 Bicep 파일은 표준 ARM 템플릿으로 컴파일됩니다. ARM 템플릿에서 유효한 리소스 유형, API 버전 및 속성은 Bicep 파일에서 유효 합니다. 현재 릴리스에는 몇 가지 [알려진 제한 사항이](#known-limitations) 있습니다.

## <a name="get-started"></a>시작

Bicep를 시작 하려면 [도구를 설치](https://github.com/Azure/bicep/blob/main/docs/installing.md)합니다.

도구를 설치한 후 [Bicep 자습서](./bicep-tutorial-create-first-bicep.md)를 사용해 보세요. 자습서 시리즈는 Bicep의 구조 및 기능을 안내 합니다. Bicep 파일을 배포 하 고 ARM 템플릿을 해당 Bicep 파일로 변환 합니다.

동등한 JSON 및 Bicep 파일을 나란히 보려면 [Bicep](https://aka.ms/bicepdemo)를 참조 하세요.

Bicep로 변환 하려는 기존 ARM 템플릿이 있는 경우 [JSON과 Bicep 간에 ARM 템플릿 변환](bicep-decompile.md)을 참조 하세요.

## <a name="bicep-improvements"></a>Bicep 향상

Bicep은 동등한 JSON에 비해 더 쉽고 간결한 구문을 제공 합니다. 식을 사용 하지 않습니다 `[...]` . 대신 함수를 직접 호출 하 고 매개 변수 및 변수에서 값을 가져옵니다. 배포 된 각 리소스에 기호화 된 이름을 지정 하 여 템플릿에서 해당 리소스를 쉽게 참조할 수 있도록 합니다.

예를 들어 다음 JSON은 리소스 속성에서 출력 값을 반환 합니다.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Bicep에서 동일한 출력 식을 작성 하는 것이 더 쉽습니다. 다음 예에서는 템플릿 내에 정의 된 리소스에 대해 기호화 된 이름 **publicIP** 을 사용 하 여 동일한 속성을 반환 합니다.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

구문에 대 한 전체 비교는 [템플릿에 대 한 JSON 및 Bicep 비교](compare-template-syntax.md)를 참조 하세요.

Bicep는 리소스 간의 종속성을 자동으로 관리 합니다. `dependsOn`리소스의 기호화 된 이름이 다른 리소스 선언에서 사용 되는 경우 설정을 피할 수 있습니다.

Bicep를 사용 하면 프로젝트를 여러 모듈로 나눌 수 있습니다.

Bicep 파일의 구조는 JSON 템플릿 보다 더 유연 합니다. 파일의 어디에서 나 매개 변수, 변수 및 출력을 선언할 수 있습니다. JSON에서는 템플릿의 해당 섹션 내에서 모든 매개 변수, 변수 및 출력을 선언 해야 합니다.

Bicep에 대 한 VS Code 확장은 다양 한 유효성 검사 및 intellisense를 제공 합니다. 예를 들어 확장의 intellisense를 사용 하 여 리소스의 속성을 가져올 수 있습니다.

## <a name="known-limitations"></a>알려진 제한 사항

현재 다음과 같은 제한 사항이 있습니다.

* 복사 루프에서 모드 또는 일괄 처리 크기를 설정할 수 없습니다.
* 루프와 조건을 결합할 수 없습니다.
* 와 같은 한 줄 개체와 배열은 `['a', 'b', 'c']` 지원 되지 않습니다.

## <a name="faq"></a>FAQ

**기존 언어를 사용 하는 대신 새 언어를 만드는 이유는 무엇 인가요?**

Bicep를 새로운 언어가 아닌 기존 ARM 템플릿 언어에 대 한 수정 버전으로 간주할 수 있습니다. 구문이 변경 되었지만 핵심 기능과 런타임은 동일 하 게 유지 됩니다.

Bicep를 개발 하기 전에 기존 프로그래밍 언어를 사용 하는 것으로 간주 합니다. 대상 사용자가 다른 언어를 시작 하는 대신 Bicep를 배우는 것이 더 쉬울 것으로 결정 했습니다.

**Terraform 또는 기타 타사 인프라의 에너지를 코드 제공으로 집중 하지 않는 이유는 무엇 인가요?**

사용자 마다 다른 구성 언어와 도구를 선호 합니다. 이러한 모든 도구가 Azure에서 뛰어난 환경을 제공 하 고 싶습니다. Bicep는이 작업의 일부입니다.

Terraform을 사용 하는 데 만족 하는 경우 전환할 이유가 없습니다. Microsoft는 Azure에서 Terraform이 가장 적합 한지 확인 하기 위해 최선을 다하고 있습니다.

ARM 템플릿을 선택한 고객의 경우 Bicep은 제작 환경을 개선 하는 것으로 생각 합니다. 또한 Bicep는 인프라를 코드로 채택 하지 않은 고객을 위해 전환 하는 데 도움이 됩니다.

**Azure에만 Bicep?**

Bicep는 Azure에 전체 솔루션을 배포 하는 데 초점을 맞춘 DSL입니다. 이러한 목표를 충족 하려면 Azure 외부에 있는 일부 Api를 사용 해야 합니다. 이러한 시나리오에 대 한 확장성 지점이 제공 될 예정입니다.

**기존 ARM 템플릿은 어떻게 되나요?**

계속 해 서 항상 그대로 작동 합니다. 변경할 필요가 없습니다. 기본 ARM 템플릿 JSON 언어를 계속 지원 합니다. Bicep 파일은 JSON으로 컴파일되고 JSON은 배포를 위해 Azure에 전송 됩니다.

준비가 되 면 [JSON 파일을 Bicep로 변환할](bicep-decompile.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

[Bicep 자습서](./bicep-tutorial-create-first-bicep.md)를 시작 하세요.
