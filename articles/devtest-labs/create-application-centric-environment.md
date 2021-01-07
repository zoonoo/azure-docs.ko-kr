---
title: 응용 프로그램 중심 환경 만들기-Azure
description: 이 문서에서는 Cloud Shell 집단 및 Microsoft Azure를 사용 하 여 응용 프로그램 중심 환경을 만드는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918265"
---
# <a name="create-an-application-centric-environment"></a>응용 프로그램 중심 환경 만들기

[Quali의 CloudShell 집단](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) 는 Azure 및 Kubernetes를 비롯 한 클라우드 기술에 대 한 복잡 한 응용 프로그램 중심 환경을 위해 대규모 인프라 자동화를 제공 하기 위한 SaaS 플랫폼입니다. CloudShell 집단는 개발자 팀이 값 스트림 전체에 복잡 한 응용 프로그램을 배포 하는 데 도움이 되는 Azure DevTest Labs를 보완 합니다.

이 문서에서는 CloudShell 집단 및 Microsoft Azure를 사용 하 여 응용 프로그램 중심 환경을 만드는 방법을 보여 줍니다.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>CloudShell 집단 및 Microsoft Azure를 사용 하 여 환경 설정

1. [집단](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview)무료 평가판에 등록 하세요.

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="평가판에 가입":::    
1. Azure 계정 (여기에서[단계 보기](https://colonysupport.quali.com/hc/articles/360008222234))을 연결 합니다.

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="집단 시작":::     
1. 사용자를 공간에 초대 합니다.
1. YAML 파일을 사용 하 여 첫 번째 청사진을 만듭니다 ([여기에서 단계 보기](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. GitHub 또는 BitBucket의 청사진 리포지토리를 집단에 연결 합니다.
    1. 집단 샘플 청사진을 기반으로 사용 하 고 적절 하 게 수정 합니다.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="스트레스 테스트":::    
    1. 다른 사람이 사용할 수 있도록 청사진을 게시 합니다.
1. 집단를 사용 하 여 응용 프로그램 환경을 샌드박스에 시작 합니다.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="집단를 사용 하 여 응용 프로그램 환경을 샌드박스에 시작":::    

> [!NOTE]
> Azure DevOps에서 청사진을 CI/CD 워크플로의 일부로 통합할 수도 있습니다 ([여기에서 단계 보기](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Azure DevOps 파이프라인에 연결":::    

## <a name="next-steps"></a>다음 단계

[집단 데모 요청](https://info.quali.com/cloudshell-colony-demo-request)
