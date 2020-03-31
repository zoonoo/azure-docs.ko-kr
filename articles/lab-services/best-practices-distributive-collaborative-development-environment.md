---
title: Azure DevTest 랩 리소스의 분산 공동 개발
description: DevTest Labs 리소스를 개발하기 위해 분산 및 공동 개발 환경을 설정하는 모범 사례를 제공합니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170117"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs 리소스의 분산 및 공동 개발을 위한 모범 사례
분산 협업 개발을 통해 서로 다른 팀이나 사용자가 코드 베이스를 개발하고 유지할 수 있습니다. 개발 프로세스가 성공하려면 정보를 생성, 공유 및 통합하는 기능에 따라 달라집니다. 이 키 개발 원칙은 Azure DevTest 랩내에서 사용할 수 있습니다. 랩에는 엔터프라이즈 내의 여러 랩 간에 일반적으로 분산되는 여러 가지 유형의 리소스가 있습니다. 다양한 유형의 리소스는 다음 두 가지 영역에 중점을 두어 있습니다.

- 랩 내에 내부적으로 저장되는 리소스(랩 기반)
- [랩에 연결된 외부 리포지토리에](devtest-lab-add-artifact-repo.md) 저장된 리소스(코드 리포지토리 기반). 

이 문서에서는 여러 팀에서 공동 작업 및 배포를 허용하는 몇 가지 모범 사례를 설명하는 동시에 모든 수준에서 사용자 지정 및 품질을 보장합니다.

## <a name="lab-based-resources"></a>랩 기반 리소스

### <a name="custom-virtual-machine-images"></a>사용자 지정 가상 머신 이미지
야간에 랩에 배포되는 사용자 지정 이미지의 공통 소스를 가질 수 있습니다. 자세한 내용은 [이미지 팩터리](image-factory-create.md)를 참조하십시오.    

### <a name="formulas"></a>수식
[수식은](devtest-lab-manage-formulas.md) 랩에 따라 다르며 배포 메커니즘이 없습니다. 랩 멤버는 수식의 모든 개발을 수행합니다. 

## <a name="code-repository-based-resources"></a>코드 리포지토리 기반 리소스
코드 리포지토리, 아티팩트 및 환경을 기반으로 하는 두 가지 기능이 있습니다. 이 문서에서는 조직 수준 또는 팀 수준에서 사용 가능한 아티팩트 및 환경을 사용자 지정할 수 있도록 리포지토리 및 워크플로를 가장 효과적으로 설정하는 방법과 기능을 설명합니다.  이 워크플로는 표준 [소스 코드 제어 분기 전략을](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)기반으로 합니다. 

### <a name="key-concepts"></a>주요 개념
아티팩트의 소스 정보에는 메타데이터, 스크립트가 포함됩니다. 환경에 대한 소스 정보에는 PowerShell 스크립트, DSC 스크립트, Zip 파일 등과 같은 지원 파일이 있는 메타데이터 및 리소스 관리자 템플릿이 포함됩니다.  

### <a name="repository-structure"></a>리포지토리 구조  
SCC(소스 코드 제어)의 가장 일반적인 구성은 랩에서 사용되는 코드 파일(리소스 관리자 템플릿, 메타데이터 및 스크립트)을 저장하기 위한 다중 계층 구조를 설정하는 것입니다. 특히 비즈니스의 여러 수준에서 관리되는 리소스를 저장하기 위해 서로 다른 리포지토리를 만듭니다.   

- 회사 전체 리소스.
- 사업부/사업부 전체 리소스
- 팀별 리소스입니다.

이러한 각 수준은 마스터 분기가 프로덕션 품질이어야 하는 다른 리포지토리로 연결됩니다. 각 리포지토리의 [분기는](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) 이러한 특정 리소스(아티팩트 또는 템플릿)를 개발하기 위한 것입니다. 이 구조는 여러 리포지토리와 여러 분기를 동시에 조직의 랩에 쉽게 연결할 수 있으므로 DevTest Labs와 잘 일치합니다. 리포지토리 이름은 사용자 인터페이스(UI)에 포함되므로 동일한 이름, 설명 및 게시자가 있을 때 혼동을 방지할 수 있습니다.
     
다음 다이어그램에는 IT 부서에서 유지 관리하는 회사 리포지토리와 R&D 부서에서 유지 관리하는 부서 리포지토리라는 두 개의 리포지토리가 표시됩니다.

![샘플 배포 및 협업 개발 환경](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
이 계층화된 구조는 마스터 브랜치에서 더 높은 수준의 품질을 유지하면서 도모할 수 있으며 랩에 여러 개의 리포지토리를 연결하면 유연성이 향상됩니다.

## <a name="next-steps"></a>다음 단계    
다음 문서를 참조하세요.

- [Azure 포털또는 Azure](devtest-lab-add-artifact-repo.md) [리소스 관리 템플릿을](add-artifact-repository.md) 사용하여 랩에 리포지토리 추가
- [데브테스트 랩 아티팩트](devtest-lab-artifact-author.md)
- [DevTest 랩 환경](devtest-lab-create-environment-from-arm.md).
