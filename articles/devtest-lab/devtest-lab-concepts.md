---
title: "DevTest Labs 개념 | Microsoft 문서"
description: "DevTest Lab의 기본 개념과 Azure 가상 컴퓨터를 쉽게 만들고 관리하고 모니터링할 수 있는 방법 알아보기"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e1b087b9e952d9045e8580d4074e7655d165a08


---
# <a name="devtest-labs-concepts"></a>DevTest Lab 개념
> [!NOTE]
> 이 문서는 세 파트로 구성된 시리즈의 파트 3입니다.
> 
> 1. [DevTest Lab이란?](devtest-lab-overview.md)
> 2. [DevTest Lab을 선택해야 하는 이유](devtest-lab-why.md)
> 3. **[DevTest Lab 개념](devtest-lab-concepts.md)**
> 
> 

## <a name="overview"></a>개요
다음은 DevTest Lab 개념 및 정의 목록입니다.

## <a name="artifacts"></a>아티팩트
VM이 프로비전된 후 응용 프로그램을 배포하고 구성하기 위해 아티팩트가 사용됩니다. 아티팩트는 다음과 같을 수 있습니다.

* VM에 설치하려는 도구(예: 에이전트, Fiddler 및 Visual Studio)
* VM에서 실행하려는 작업(예: 리포지토리 복제)
* 테스트하려는 응용 프로그램

아티팩트는 배포를 수행하고 구성을 적용하기 위한 지침이 포함된 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) JSON 파일입니다. 

## <a name="artifact-repositories"></a>아티팩트 리포지토리
아티팩트 리포지토리는 아티팩트가 체크 인되는 Git 리포지토리입니다. 동일한 아티팩트 리포지토리를 조직에 있는 여러 개의 랩에 추가하면 다시 사용하거나 공유할 수 있습니다.

## <a name="base-images"></a>기본 이미지
기본 이미지는 VM을 빠르게 만들기 위해 미리 설치되고 구성된 모든 도구와 설정이 포함된 VM 이미지입니다. 기존 기본 항목을 선택하고 테스트 에이전트를 설치하기 위한 아티팩트를 추가하여 VM을 프로비전할 수 있습니다. 그런 다음 프로비전된 VM을 기본으로 설정하면 프로비전된 각각의 VM에 대한 테스트 에이전트를 다시 설치하지 않고도 기본을 사용할 수 있습니다.

## <a name="formulas"></a>수식
기본 이미지와 더불어, 수식은 신속한 VM 프로비전을 위한 메커니즘을 제공합니다. DevTest Lab에 포함된 수식은 랩 VM을 만드는 데 사용되는 기본 속성 값의 목록입니다. 수식을 사용하면, 매번 같은 속성을 지정할 필요 없이, 동일한 속성(예: 기본 이미지, VM 크기, 가상 네트워크, 아티팩트) 집합을 포함하는 VM을 만들 수 있습니다. 수식을 통해 VM을 만들 때, 기본값을 그대로 사용하거나 수정할 수 있습니다.

## <a name="caps"></a>캡
캡은 랩에서 낭비를 최소화하는 메커니즘입니다. 예를 들어 캡을 설정하여 사용자당 또는 랩에서 만들 수 있는 VM의 수를 제한할 수 있습니다.

## <a name="policies"></a>정책
정책은 랩에서의 비용 제어에 도움이 됩니다. 예를 들어 정의된 일정에 따라 VM이 자동으로 종료되도록 정책을 만들 수 있습니다.

## <a name="security-levels"></a>보안 수준
보안 액세스는 Azure 역할 기반 액세스 제어(RBAC)를 통해 결정됩니다. 액세스의 작동 방식을 이해하기 위해 RBAC에 의해 정의된 대로 사용 권한, 역할 및 범위 사이의 차이점을 이해하는 데 도움을 줍니다. 

* 사용 권한 - 특정 작업에 대해 정의된 액세스입니다(예: 모든 가상 컴퓨터에 대한 읽기 액세스). 
* 역할 - 그룹화되고 사용자에게 할당될 수 있는 사용 권한의 집합입니다. 예를 들어 *구독 소유자* 역할은 구독 내의 모든 리소스에 대한 액세스를 보유합니다. 
* 범위 - Azure 리소스의 계층 구조 내 수준입니다(예: 리소스 그룹, 단일 랩 또는 전체 구독).

DevTest Labs의 범위 내에 사용자 사용 권한을 정의하는 두 가지 유형의 역할(랩 소유자 및 랩 사용자)이 있습니다.

* 랩 소유자 - 랩 소유자는 랩 내의 모든 리소스에 대한 액세스 권한을 보유합니다. 따라서 랩 소유자는 정책을 수정하고 VM을 읽고 쓰고, 가상 네트워크를 변경하는 등의 작업을 할 수 있습니다. 
* 랩 사용자 - 랩 사용자는 VM, 정책 및 가상 네트워크와 같은 모든 랩 리소스를 볼 수 있지만 정책 또는 다른 사용자가 만든 VM을 수정할 수 없습니다. 

DevTest Lab에서 사용자 지정 역할을 만드는 방법을 보려면 [특정 랩 정책에 사용자 권한 부여](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)문서를 참조하세요.

범위는 계층적이므로 사용자가 특정 범위에서 사용 권한을 가진 경우 포함된 모든 하위 수준 범위에서 해당 사용 권한이 자동으로 부여됩니다. 예를 들어 사용자가 구독 소유자의 역할에 할당되면 모든 가상 컴퓨터, 모든 가상 네트워크 및 모든 랩을 포함하는 구독의 모든 리소스에 대한 액세스를 가집니다. 따라서 구독 소유자는 자동으로 랩 소유자의 역할을 상속합니다. 그러나 반대의 경우는 적용되지 않습니다. 랩 소유자는 구독 수준보다 낮은 범위인 랩에 대한 액세스를 가집니다. 따라서 랩 소유자는 랩 외부에 있는 가상 컴퓨터 또는 가상 네트워크 또는 리소스를 볼 수 없습니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
[DevTest Lab에서 랩 만들기](devtest-lab-create-lab.md)




<!--HONumber=Nov16_HO3-->


