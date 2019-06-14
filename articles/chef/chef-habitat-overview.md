---
title: Habitat 사용하여 Azure에 애플리케이션 배포
description: 애플리케이션을 Azure 가상 머신 및 컨테이너에 일관되게 배포하는 방법을 알아봅니다
keywords: azure, chef, devops, 가상 머신, 개요, 자동화, habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60388814"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Habitat 사용하여 Azure에 애플리케이션 배포
[Habitat](https://www.habitat.sh/)는 애플리케이션 및 해당 자동화를 배포 단위로 번들하는 애플리케이션 패키징 및 런타임 시스템입니다. 이렇게 하면 최고의 애플리케이션 이식성이 생성되므로 애플리케이션을 다시 작성하거나 패키지하지 않고 컨테이너, 가상 머신, 완전 복구 또는 PaaS에 배포할 수 있습니다.

이 문서에서는 Habitat 사용 시의 주요 혜택을 설명합니다.

## <a name="modernize-and-move-legacy-applications"></a>레거시 애플리케이션 현대화 및 이동
레거시 애플리케이션을 Habitat로 다시 패키지하여 이전 운영 체제 및 미들웨어의 제한을 받지 않게 합니다. 그에 따른 아티팩트는 이식 가능하며 클라우드에서 실행되는 가상 머신 또는 컨테이너와 같은 최신 인프라에 쉽게 다시 배포할 수 있습니다.

## <a name="accelerate-container-adoption"></a>컨테이너 채택 가속화
Habitat는 런타임 종속성을 정확하게 표시하여 복잡한 마이크로 서비스 지향 애플리케이션의 지속적인 배포를 해결합니다. 개별 구성 요소의 단순한 파란색/녹색 배포 이상의 정교한 배포 동작을 복잡한 오케스트레이션 흐름 생성 없이 설계합니다.

## <a name="run-any-application-anywhere"></a>모든 위치에서 모든 애플리케이션 실행
Habitat을 사용하여 애플리케이션은 모든 런타임 환경에서 수정되지 않고 실행할 수 있습니다. 완전 복구 및 가상 머신부터 컨테이너(예: Docker), 클러스터 관리 시스템(예: Mesosphere 또는 Kubernetes) 및 PaaS 시스템(예: Pivotal Cloud Foundry)에 이르는 모든 것이 포함됩니다.

## <a name="integrate-into-the-chef-devops-workflow"></a>Chef DevOps 워크플로로 통합
Habitat 프로젝트는 강력한 지원 커뮤니티가 있는 Chef 소프트웨어의 오픈 소스 프로젝트 중 하나입니다. Habitat은 애플리케이션에 뛰어난 자동화 기능을 갖추려면 인프라 자동화로 Chef의 심층 경험을 활용합니다. Chef는 Habitat에 대한 상업적 지원을 제공하고 Habitat와 Chef Automate 간에 원활한 통합을 빌드하여 개발부터 배포까지의 애플리케이션 릴리스 주기를 완전히 자동화합니다.

## <a name="next-steps"></a>다음 단계

* [Habitat 사용해보기](https://www.habitat.sh/learn/)
