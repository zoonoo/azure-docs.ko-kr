---
title: Azure DevTest 랩에서 이미지 팩터리 만들기 | 마이크로 소프트 문서
description: 이 문서에서는 Git 리포지토리(Azure DevTest Labs)에서 사용할 수 있는 샘플 스크립트를 사용하여 사용자 지정 이미지 팩터리를 설정하는 방법을 보여 주며 있습니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759451"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Azure DevTest 랩에서 사용자 지정 이미지 팩터리 만들기
이 문서에서는 [Git 리포지토리에서](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)사용할 수 있는 샘플 스크립트를 사용하여 사용자 지정 이미지 팩터리를 설정하는 방법을 보여 주며 있습니다.

## <a name="whats-an-image-factory"></a>이미지 팩토리란 무엇입니까?
이미지 팩터리는 원하는 모든 구성으로 정기적으로 이미지를 빌드하고 배포하는 코드로서의 구성 솔루션입니다. 이미지 팩터의 이미지는 항상 최신 상태이며 전체 프로세스가 자동화되면 지속적인 유지 관리가 거의 0에 가까우며. 또한 필요한 모든 구성이 이미 이미지에 있으므로 기본 OS를 통해 VM을 만든 후 시스템을 수동으로 구성하는 데 걸리는 시간을 절약할 수 있습니다.

개발자 데스크톱을 DevTest Labs의 준비 상태로 설정하려면 사용자 지정 이미지를 사용하는 중요한 가속기가 있습니다. 사용자 지정 이미지의 단점은 랩에서 유지 관리해야 할 추가 방법이 있다는 것입니다. 예를 들어 제품의 평가판 버전은 시간이 지남에 따라 만료되거나 새로 릴리스된 보안 업데이트가 적용되지 않으므로 사용자 지정 이미지를 주기적으로 새로 고쳐야 합니다. 이미지 팩터리에서는 소스 코드 제어에 체크 인된 이미지의 정의가 있고 정의에 따라 사용자 지정 이미지를 생성하는 자동화된 프로세스가 있습니다.

이 솔루션을 사용하면 사용자 지정 이미지에서 가상 컴퓨터를 만드는 속도를 높이는 동시에 지속적인 유지 관리 비용을 추가로 제거할 수 있습니다. 이 솔루션을 사용하면 사용자 지정 이미지를 자동으로 만들고 다른 DevTest Lab에 배포하고 이전 이미지를 폐기할 수 있습니다. 다음 비디오에서는 이미지 팩터리에 대해 알아보고 DevTest Labs를 통해 이미지 팩토리를 구현하는 방법에 대해 알아봅니다.  모든 Azure Powershell 스크립트는 자유롭게 사용할 수 [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)있으며 여기에 있습니다.

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>솔루션의 높은 수준 보기
이 솔루션을 사용하면 사용자 지정 이미지에서 가상 컴퓨터를 만드는 속도를 높이는 동시에 지속적인 유지 관리 비용을 추가로 제거할 수 있습니다. 이 솔루션을 사용하면 사용자 지정 이미지를 자동으로 만들어 다른 DevTest 랩에 배포할 수 있습니다. DevTest 랩의 모든 작업을 자동화하기 위한 오케스트레이션 엔진으로 Azure DevOps(이전의 Visual Studio 팀 서비스)를 사용합니다.

![솔루션의 높은 수준 보기](./media/create-image-factory/high-level-view-of-solution.png)

다음과 같은 개별 단계를 실행할 수 있는 [DevTest 랩용 VSTS 확장이 있습니다.](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)

- 사용자 지정 이미지 만들기
- VM 만들기
- VM 삭제
- 환경 만들기
- 환경 삭제
- 환경 채우기

DevTest Labs 확장을 사용하면 DevTest Labs에서 사용자 지정 이미지를 자동으로 만드는 방법을 쉽게 시작할 수 있습니다.

더 복잡한 시나리오에 대해 PowerShell 스크립트를 사용하는 대체 구현이 있습니다. PowerShell을 사용하면 연속 통합 및 연속 전달(CI/CD) 도구 체인에 사용할 수 있는 DevTest Labs를 기반으로 이미지 팩터리를 완전히 자동화할 수 있습니다. 이 대체 솔루션에서 따르는 원칙은 다음과 같습니다.

- 일반적인 업데이트는 이미지 팩터리를 변경할 필요가 없습니다. 예를 들어 새 유형의 사용자 지정 이미지를 추가하고, 이전 이미지를 자동으로 폐기하고, 새 '끝점' DevTest Labs를 추가하여 사용자 지정 이미지를 수신하는 등의 등
- 일반적인 변경 사항은 소스 코드 제어(인프라와 코드)에 의해 뒷받침됩니다.
- 사용자 지정 이미지를 받는 DevTest 랩이 동일한 Azure 구독에 없을 수 있습니다(랩은 구독을 포괄).
- PowerShell 스크립트는 재사용이 가능해야 필요에 따라 추가 팩터리를 회전시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 섹션의 다음 문서로 이동: [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md)
