---
title: Azure DevTest Lab을 DevOps 통합 | Microsoft Docs
description: 지속적인 통합 (CI) 내에서 Azure DevTest Labs의 랩 사용 하는 방법을 알아봅니다/엔터프라이즈 환경에서 CD (지속적인 업데이트) 파이프라인입니다.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078925"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Azure DevTest Labs와 Azure DevOps 통합
DevOps는 (Ops) 시스템에 대 한 작업을 사용 하 여 소프트웨어 개발 (Dev)를 통합 하는 소프트웨어 개발 방법론입니다. 이 시스템 정렬을 비즈니스 목표를 사용 하 여 새로운 기능, 업데이트 및 픽스를 전달할 수 있습니다. 이 방법론은 설계 목표, 사용 패턴 및 고객 피드백을 기반으로 하는 새로운 기능에 이르기까지 수정, 복구 및 문제 발생 시 시스템을 강화 합니다. 이 방법의 구성 요소를 쉽게 식별 되는 연속 통합 (CI) / CD (지속적인 업데이트) 파이프라인입니다. CI/CD 파이프라인 정보, 코드 및 일련의 빌드, 테스트 및 배포, 시스템 생성을 포함 하는 단계를 통해 커밋의 리소스를 사용 합니다. 이 문서는 labs 파이프라인 내에서 엔터프라이즈 환경에서 효과적으로 사용 하는 다양 한 방법에 중점을 둡니다. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Labs를 사용 하 여 DevOps 워크플로에서 이점 

### <a name="focused-access"></a>초점 맞춘된 액세스 
랩 구성 요소로 사용 하 여 제한 된 그룹의 사람들과 함께 연결할 특정 에코 시스템을 수 있습니다. 일반적으로 팀 또는 그룹에는 특정 기능이 나 공용 영역에서 작동 하는 랩에 할당 해야 합니다.   

### <a name="infrastructure-replication-in-the-cloud"></a>클라우드에서 인프라 복제 
개발자는 소스 코드 및 도구를 사용 하 여 개발 상자를 포함 하는 개발 에코 시스템을 신속 하 게 설정할 수 있습니다. 개발자는 거의 동일한: production 구성 되는 환경을 만들 수도 있습니다. 더 빠르게 내부 루프 개발 수 있습니다. 

### <a name="pre-production"></a>사전 프로덕션 
CI/CD 파이프라인에 랩 있으면 쉽게 여러 다른 사전 프로덕션 환경 또는 비동기 테스트에 대 한 동시에 실행 되는 컴퓨터에 있습니다. 빌드 에이전트와 같은 다른 지원 인프라를 배포 하 고 랩 내에서 관리할 수 있습니다. 

## <a name="devops-with-devtest-labs"></a>DevTest Labs 사용 하 여 DevOps 

### <a name="development--operation"></a>개발 / 작업 
랩 기능 영역에서 작동 하는 팀에 집중 해야 합니다. 이 일반적인 포커스 도구, 스크립트 또는 Resource Manager 템플릿 등 특정 영역 리소스를 공유할 수 있습니다. 빠르게 변경 내용을 작은 그룹에 부정적인 영향을 제한 하는 동안 수 있습니다. 이러한 공유 리소스는 모든 필요한 코드, 도구 및 구성을 사용 하 여 개발 Vm을 만들려는 개발자를 허용 합니다. 만들 수 있습니다 하거나 동적으로 또는 사용자 지정 기본 이미지를 만드는 시스템입니다. 개발자는 Vm을 만들 수 할 뿐만 아니라 랩에 적절 한 Azure 리소스를 만드는 데 필요한 템플릿을 기반으로 하는 DevTest Labs 환경도 만들 수 있습니다. 다른 사람이 영향을 주지 않고 랩 환경에 대 한 모든 변경 또는 삭제 작업을 수행할 수 있습니다. 제품을 고객의 컴퓨터에 설치 된 독립 실행형 시스템 시나리오를 살펴보겠습니다. 이 시나리오에서 DevTest Labs 아티팩트를 사용 하 여 코드의 더 빠른 내부 루프 테스트에 대 한 고객 구성을 미리 작성 및 추가 소프트웨어를 설치를 포함 하는 VM 만들기를 향상 시켰습니다. 
  
## <a name="cicd-pipeline"></a>CI/CD 파이프라인 
CI/CD 파이프라인 코드 개발자의 끌어오기 요청에서 기존 코드를 사용 하 여 통합 및 프로덕션 환경에 배포를 이동 하는 DevOps의 중요 한 구성 요소 중 하나입니다. 모든 리소스는 랩 내에 있이 필요가 없습니다. 예를 들어 Jenkins 호스트 수 설정할 랩 외부 보다 지속적인 리소스로 합니다. 파이프라인에 랩 통합 하는 몇 가지 특정 예는 다음과 같습니다. 

### <a name="build"></a>빌드 
빌드 파이프라인 릴리스 파이프라인에 전달 될를 함께 테스트 되는 구성 요소 패키지 만들기에 포커스가 있습니다. 랩 빌드 에이전트 및 기타 지원 리소스에 대 한 위치와 빌드 파이프라인의 일부를 수 있습니다. 동적으로 인프라를 구축 하는 기능을 필요 하 게 제어할 수 있습니다. 환경에서 여러 환경을 유지 하는 기능을 사용 하 여 각 빌드 환경 정보의 일부로 빌드 ID를 사용 하 여 특정 빌드에 리소스를 고유 하 게 식별 하는 동안 비동기적으로 실행할 수 있습니다.   

빌드 에이전트에 대 한 액세스를 제한 하는 랩의 기능 보안 증가 하 고 실수로 인 한 손상 가능성을 줄여줍니다.  

### <a name="test"></a>테스트 
자동 및 수동 테스트에 사용할 수 있는 Azure 리소스 (Vm, 환경)의 생성을 자동화 하려면 CI/CD 파이프라인을 DevTest Labs를 통해. Vm은 아티팩트 또는 다양 한 사용자 지정 구성을 테스트 하는 데 필요한 만드는 빌드 프로세스에서 정보를 사용 하는 수식을 사용 하 여 만들어집니다.   

### <a name="release"></a>해제 
DevTest Labs는 코드를 배포 하기 전에 릴리스 섹션에서 확인을 위해 주로 사용 됩니다. 빌드 섹션에 테스트 하는 것이 비슷합니다. DevTest Labs 내 프로덕션 리소스에 배포 해서는 안 됩니다. 

### <a name="customization"></a>사용자 지정 
Azure devops에서 Vm의 조작 및 환경 특정 labs 내에서 허용 하는 기존 태스크가 있습니다. Azure DevOps 서비스는 CI/CD 파이프라인을 관리 하는 한 가지 방법은, 랩 REST Api 호출, PowerShell 스크립트 실행 또는 Azure CLI를 사용 하는 기능을 지 원하는 모든 시스템에 통합할 수 있습니다. 

하지만 일부 CI/CD 파이프라인 관리자 및 Azure DevTest Labs 내 리소스를 관리할 수 있는 기존 오픈 소스 플러그 인 경우 스크립팅을 사용 하 여 일부 사용자 지정 파이프라인의 특정 요구 사항을 충족 해야 합니다.  이 점을 염두: 작업을 실행 하는 경우 서비스 주체 랩에 액세스 하려면 적절 한 역할을 사용 하 여 사용 됩니다. 서비스 주체는 일반적으로 랩에 대 한 참가자 역할 액세스를 해야합니다. 자세한 내용은 [Azure DevOps 연속 통합 및 배달 파이프라인에 Azure DevTests Labs 통합](devtest-lab-integrate-ci-cd-vsts.md)합니다. 
 