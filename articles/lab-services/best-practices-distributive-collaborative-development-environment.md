---
title: Azure DevTest Labs 리소스의 공동 개발 distributed | Microsoft Docs
description: DevTest Labs 리소스를 개발 하는 분산 및 공동 작업 개발 환경을 설정 하는 모범 사례를 제공 합니다.
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
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: d8892b2d00008c9d67f8bc28d1abb7d562dfd95c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079887"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs 리소스 분산 및 공동 작업 개발에 대 한 모범 사례
분산된 된 공동 작업 개발에는 서로 다른 팀 이나 개발 및 코드를 기본 유지 관리 하 게 수 있습니다. 찾기가 성공 하려면 개발 프로세스 만들기, 공유 및 정보를 통합 하는 기능에 따라 달라 집니다. Azure DevTest Labs 내에서이 주요 개발 원칙을 사용할 수 있습니다. 여러 유형의 랩 내에서 일반적으로 기업 내에서 다른 labs 간에 분산 된 리소스가 있습니다. 다양 한 유형의 리소스는 두 가지 영역으로 초점을 맞추었습니다.

- 랩 (랩 기반) 내에서 내부적으로 저장 되는 리소스
- 에 저장 된 리소스 [랩에 연결 된 외부 리포지토리](devtest-lab-add-artifact-repo.md) (코드 리포지토리 기반). 

이 문서에서는 사용자 지정 및 모든 수준의 품질을 보장 하면서 여러 팀에서 공동 작업 및 배포를 허용 하는 몇 가지 모범 사례를 설명 합니다.

## <a name="lab-based-resources"></a>랩 기반 리소스

### <a name="custom-virtual-machine-images"></a>사용자 지정 가상 머신 이미지
사용자 지정 이미지를 매일 밤 마다 랩에 배포 되는 일반적인 소스를 할 수 있습니다. 자세한 내용은 [이미지 팩터리](image-factory-create.md)합니다.    

### <a name="formulas"></a>수식
[수식](devtest-lab-manage-formulas.md) 랩 별 및 배포 메커니즘 필요는 없습니다. 랩 멤버 수식의 모든 개발 작업을 수행 합니다. 

## <a name="code-repository-based-resources"></a>코드 리포지토리 기반 리소스
코드 리포지토리, 아티팩트 및 환경에 기반한 있는 두 가지 다른 기능이 있습니다. 이 문서에서는 가장 효과적으로 사용 가능한 아티팩트 및 조직 수준 또는 팀 수준에서 환경에서 사용자 지정할 수 있도록 리포지토리 및 워크플로를 설정 하는 방법과 기능을 통해 이동 합니다.  이 워크플로 표준을 기반으로 [소스 코드 제어 분기 전략](/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)합니다. 

### <a name="key-concepts"></a>주요 개념
아티팩트에 대 한 원본 정보 메타 데이터를 스크립트에 포함 됩니다. 환경에 대 한 원본 정보를 PowerShell 스크립트, DSC 스크립트, Zip 파일 등 모든 지원 파일을 사용 하 여 메타 데이터 및 Resource Manager 템플릿을 포함합니다.  

### <a name="repository-structure"></a>리포지토리 구조  
소스 코드 제어 (SCC)에 대 한 가장 일반적인 구성을 lab에서 사용 되는 코드 파일 (Resource Manager 템플릿, 메타 데이터 및 스크립트)을 저장 하는 것에 대 한 다중 계층 구조를 설정 하는 것입니다. 특히, 비즈니스의 다양 한 수준에서 관리 되는 리소스를 저장할 다른 리포지토리를 만듭니다.   

- 회사 전체의 리소스입니다.
- 비즈니스 단위/부서 전체 리소스
- 팀에 특정 리소스입니다.

각 수준의 다른 리포지토리의 마스터 분기는 프로덕션 품질의 되어야 할 위치를 연결 합니다. 합니다 [분기](/devops/repos/git/git-branching-guidance?view=azure-devops) 에서 각 리포지토리에 이러한 특정 리소스 (아티팩트 또는 템플릿)의 개발에 대 한 합니다. 이 구조는 조직의 랩에 동시에 여러 리포지토리 및 여러 분기를 쉽게 연결할 수 있는 DevTest Labs를 통해 잘 맞춥니다. 리포지토리 이름이 혼동을 피하기 위해 이름이 동일한 경우 (UI) 사용자 인터페이스, 설명 및 게시자에 포함 됩니다.
     
다음 다이어그램은 리포지토리 두 개를 보여 줍니다: IT 부서에서 유지 되는 회사 리포지토리 및 r&d 부서에서 유지 관리 부서 리포지토리.

![샘플 분배 하 고 공동 개발 환경](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
이 계층된 구조 개발 하면 여러 리포지토리에 랩 연결 보다 유연 하 게 하는 동안 마스터 분기에서 더 높은 수준의 품질을 유지 하면서 수 있습니다.

## <a name="next-steps"></a>다음 단계    
다음 문서를 참조하세요.

- 중 하나를 사용 하 여 랩에 리포지토리를 추가 합니다 [Azure portal](devtest-lab-add-artifact-repo.md) 통하거나 [Azure Resource Management 템플릿](add-artifact-repository.md)
- [DevTest Labs 아티팩트](devtest-lab-artifact-author.md)
- [DevTest Labs 환경](devtest-lab-create-environment-from-arm.md)합니다.
