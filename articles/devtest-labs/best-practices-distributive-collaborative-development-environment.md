---
title: Azure DevTest Labs 리소스의 분산 된 공동 작업 개발
description: DevTest Labs 리소스를 개발 하기 위한 분산 및 공동 작업 개발 환경을 설정 하기 위한 모범 사례를 제공 합니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c18bbbfd3ad727811cc28c424381e5caf32b1cfc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483808"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs 리소스의 분산 및 공동 작업 개발에 대 한 모범 사례
분산 된 공동 작업 개발을 사용 하면 다른 팀 이나 사용자가 코드 베이스를 개발 하 고 유지 관리할 수 있습니다. 개발 프로세스는 정보를 만들고, 공유 하 고, 통합 하는 기능에 따라 달라 집니다. 이 주요 개발 원칙은 Azure DevTest Labs 내에서 사용할 수 있습니다. 랩 내에는 기업 내 여러 랩에서 일반적으로 분산 되는 여러 유형의 리소스가 있습니다. 여러 유형의 리소스는 다음과 같은 두 영역으로 집중 됩니다.

- 랩에 내부적으로 저장 된 리소스 (랩 기반)
- [랩에 연결 된 외부 리포지토리에](devtest-lab-add-artifact-repo.md) 저장 된 리소스 (코드 리포지토리 기반) 

이 문서에서는 모든 수준에서 사용자 지정 및 품질을 보장 하면서 여러 팀 간에 공동 작업 및 배포를 허용 하는 몇 가지 모범 사례에 대해 설명 합니다.

## <a name="lab-based-resources"></a>랩 기반 리소스

### <a name="custom-virtual-machine-images"></a>사용자 지정 가상 머신 이미지
매일 밤에 배포 되는 사용자 지정 이미지의 공통 원본을 사용할 수 있습니다. 자세한 내용은 [이미지 팩터리](image-factory-create.md)를 참조 하세요.    

### <a name="formulas"></a>수식
[수식은](devtest-lab-manage-formulas.md) lab에만 적용 되며 배포 메커니즘은 없습니다. 랩 멤버는 모든 수식 개발을 수행 합니다. 

## <a name="code-repository-based-resources"></a>코드 리포지토리 기반 리소스
코드 리포지토리, 아티팩트 및 환경을 기반으로 하는 두 가지 다른 기능이 있습니다. 이 문서에서는 기능에 대해 설명 하 고, 조직 수준 또는 팀 수준에서 사용 가능한 아티팩트와 환경을 사용자 지정 하는 기능을 허용 하도록 리포지토리 및 워크플로를 가장 효과적으로 설정 하는 방법을 설명 합니다.  이 워크플로는 표준 [소스 코드 제어 분기 전략](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)을 기반으로 합니다. 

### <a name="key-concepts"></a>주요 개념
아티팩트의 원본 정보에는 메타 데이터, 스크립트가 포함 됩니다. 환경에 대 한 원본 정보에는 PowerShell 스크립트, DSC 스크립트, Zip 파일 등의 지원 파일을 포함 하는 메타 데이터 및 리소스 관리자 템플릿이 포함 되어 있습니다.  

### <a name="repository-structure"></a>리포지토리 구조  
SCC (소스 코드 제어)에 대 한 가장 일반적인 구성은 랩에서 사용 되는 코드 파일 (리소스 관리자 템플릿, 메타 데이터 및 스크립트)을 저장 하기 위한 다중 계층 구조를 설정 하는 것입니다. 특히 다른 비즈니스 수준에서 관리 되는 리소스를 저장 하기 위해 다른 리포지토리를 만듭니다.   

- 회사 전체 리소스.
- 비즈니스 단위/부서 차원의 리소스
- 팀 특정 리소스.

이러한 각 수준은 마스터 분기가 프로덕션 품질에 필요한 다른 리포지토리에 연결 됩니다. 각 리포지토리의 [분기](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) 는 해당 특정 리소스 (아티팩트 또는 템플릿)를 개발 하기 위한 것입니다. 이 구조는 여러 리포지토리 및 여러 분기를 조직의 랩에서 동시에 쉽게 연결할 수 있으므로 DevTest Labs와 잘 정렬 됩니다. 동일한 이름, 설명 및 게시자가 있는 경우 혼동을 피하기 위해 리포지토리 이름은 UI (사용자 인터페이스)에 포함 됩니다.
     
다음 다이어그램은 IT 부서에서 유지 관리 되는 회사 리포지토리와 R&D 나누기에 의해 유지 관리 되는 나누기 리포지토리에 해당 하는 두 가지 리포지토리를 보여 줍니다.

![샘플 분배 및 공동 개발 환경](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
이 계층화 된 구조를 사용 하면 랩에 연결 된 여러 리포지토리가 있는 동안 마스터 분기에서 더 높은 수준의 품질을 유지 하면서 유연성을 높일 수 있습니다.

## <a name="next-steps"></a>다음 단계    
다음 문서를 참조하세요.

- [Azure Portal](devtest-lab-add-artifact-repo.md) 를 사용 하거나 [Azure 리소스 관리 템플릿을](add-artifact-repository.md) 통해 랩에 리포지토리를 추가 합니다.
- [DevTest Labs 아티팩트](devtest-lab-artifact-author.md)
- [DevTest Labs 환경](devtest-lab-create-environment-from-arm.md).
