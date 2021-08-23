---
title: Microsoft Learn의 Bicep 검색
description: Microsoft Learn for Bicep에서 사용할 수 있는 단위의 개요를 제공합니다.
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: 0238741f4ce6a246c9fd4c8279a55beb494e52ad
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112017850"
---
# <a name="bicep-in-microsoft-learn"></a>Microsoft Learn의 Bicep

Bicep을 사용하여 Azure에 인프라를 배포하는 단계별 지침을 위해 Microsoft Learn은 여러 가지 학습 모듈을 제공합니다.

## <a name="introductory-path"></a>소개 경로

[Bicep을 사용하여 Azure에서 리소스 배포 및 관리](/learn/paths/bicep-deploy/) 학습 경로를 시작하는 것이 가장 좋습니다. 코드 제공 인프라 개념을 소개합니다. 경로는 점점 더 복잡해지는 Bicep 파일을 빌드하는 단계를 안내합니다.

이 경로에는 다음 모듈이 포함됩니다.

| 학습 모듈 | Description |
| ------------ | ----------- |
| [Bicep을 사용하는 코드 제공 인프라 소개](/learn/modules/introduction-to-infrastructure-as-code-using-bicep/) | 이 모듈에서는 코드 제공 인프라, Azure Resource Manager 및 Bicep을 사용하여 클라우드 배포를 신속하고 안전하게 확장하는 이점을 설명합니다. Bicep이 적합한 배포 도구인 배포 유형을 결정하는 데 도움이 됩니다. |
| [첫 번째 Bicep 템플릿 빌드](/learn/modules/deploy-azure-resources-by-using-bicep-templates/) | 이 모듈에서는 Bicep 템플릿 내에서 Azure 리소스를 정의합니다. 배포의 일관성과 안정성을 개선하고 필요한 수동 작업을 줄이며 환경 간에 배포를 확장합니다. 템플릿은 매개 변수, 변수, 식 및 모듈을 사용하여 유연하고 재사용이 가능합니다. |
| [매개 변수를 사용하여 재사용 가능한 Bicep 템플릿 빌드](/learn/modules/build-reusable-bicep-templates-parameters/) | 이 모듈에서는 Bicep 매개 변수를 사용하여 각 배포 중에 템플릿에 대한 정보를 제공하는 방법을 설명합니다. 매개 변수를 이해하고 작업하기 쉽게 만드는 매개 변수 데코레이터에 대해 알아봅니다. 또한 보안 정보를 사용할 때 매개 변수 값을 제공하고 보호할 수 있는 다양한 방법에 대해서도 알아봅니다. |
| [조건 및 루프를 사용하여 유연한 Bicep 템플릿 빌드](/learn/modules/build-flexible-bicep-templates-conditions-loops/) | 특정 제약 조건이 있는 경우에만 조건을 사용하여 리소스를 배포하는 방법을 알아봅니다. 또한 루프를 사용하여 유사한 속성이 있는 여러 리소스를 배포하는 방법을 알아봅니다. |
| [Bicep을 사용하여 자식 및 확장 리소스 배포](/learn/modules/child-extension-bicep-templates/) | 이 모듈에서는 Bicep 코드에서 다양한 Azure 리소스를 배포하는 방법을 보여줍니다. 자식 및 확장 리소스에 대해 알아보고 Bicep 내에서 자식 및 확장 리소스를 정의하고 사용하는 방법을 알아봅니다. Bicep 템플릿 또는 모듈 외부에서 만든 리소스를 작업하려면 Bicep을 사용하세요. |
| [Bicep을 사용하여 구독, 관리 그룹 및 테넌트에 리소스 배포](/learn/modules/deploy-resources-scopes-bicep/) | 구독, 관리 그룹 및 테넌트 범위에서 Azure 리소스를 배포합니다. 이러한 리소스의 용도, 리소스를 사용하는 이유 및 Bicep 코드를 만들어 배포하는 방법을 알아봅니다. 또한 한 번의 작업으로 여러 범위에 배포할 수 있는 단일 Bicep 파일 세트를 만드는 방법을 알아봅니다. |
| [배포 스크립트를 사용하여 템플릿 확장](/learn/modules/extend-resource-manager-template-deployment-scripts/) | 배포 스크립트를 사용하여 Bicep 파일 또는 ARM 템플릿(Azure Resource Manager 템플릿)에 사용자 지정 단계를 추가하는 방법을 알아봅니다. |

## <a name="other-modules"></a>기타 모듈

위의 경로 외에도 다음 모듈에는 Bicep 콘텐츠가 포함되어 있습니다.

| 학습 모듈 | Description |
| ------------ | ----------- |
| [가상(what-if)을 사용하여 Azure 배포 변경 내용 미리 보기](/learn/modules/arm-template-whatif/) | 이 모듈에서는 가상 작업을 사용하여 변경 내용을 미리 보는 방법을 알려줍니다. 가상(what-if)을 사용하면 Bicep 파일이 예상한 변경 내용만 만들 수 있습니다. |

## <a name="next-steps"></a>다음 단계

* Bicep에 대한 간단한 소개는 [Bicep 빠른 시작](quickstart-create-bicep-use-visual-studio-code.md)을 참조하세요.
* Bicep 파일을 개선하는 방법에 대한 제안은 [Bicep에 대한 모범 사례](best-practices.md)를 참조하세요.
