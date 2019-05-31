---
title: Azure DevOps Projects 개요 | Microsoft Docs
description: Azure DevOps Projects의 가치 이해
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: 6ae2d0723b195ed59f8c6a7320245509145094df
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60925950"
---
# <a name="overview-of-azure-devops-projects"></a>Azure DevOps Projects 개요

 Azure DevOps Projects를 사용하면 Azure를 쉽게 시작할 수 있습니다. 이 기능을 사용하면 Azure Portal에서 단 몇 단계로 원하는 Azure 서비스에 좋아하는 앱을 빠르게 시작할 수 있습니다. 

 DevOps Projects는 애플리케이션 개발, 배포 및 모니터링에 필요한 모든 것을 설정합니다. DevOps Projects 대시보드를 사용하여 Azure Portal에서 한 번에 코드 커밋, 빌드 및 배포를 모니터링할 수 있습니다.

## <a name="why-should-i-use-devops-projects"></a>DevOps Projects를 사용해야 하는 이유는 무엇인가요?

  DevOps Projects는 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 Azure로 자동 설정합니다.  기존 코드를 사용하여 시작하거나 제공된 샘플 애플리케이션 중 하나를 사용할 수 있습니다. 그런 다음, Virtual Machines, App Service, AKS(Azure Kubernetes Services), Azure SQL Database 및 Azure Service Fabric 등의 다양한 Azure 서비스에 해당 애플리케이션을 신속하게 배포할 수 있습니다.  

  DevOps Projects는 Azure Portal에 DevOps Projects 대시보드 만들어 초기 Git 리포지토리 설정, CI/CD 파이프라인 구성, 모니터링을 위해 Application Insights 리소스 만들기 및 전체 솔루션의 단일 보기 제공 등 DevOps 파이프라인의 초기 구성을 위해 모든 작업을 수행합니다.

DevOps Projects를 사용하여 다음을 수행할 수 있습니다.

* Azure에 애플리케이션 신속 배포
* CI/CD 파이프라인의 설정 자동화
* CI/CD 파이프라인을 제대로 설정하는 방법 확인 및 이해
* 특정 시나리오에 따라 릴리스 파이프라인을 추가로 사용자 지정

## <a name="how-do-i-use-devops-projects"></a>DevOps Projects는 어떻게 사용하나요?

  DevOps Projects는 Azure Portal에서 사용할 수 있습니다. 포털에서 다른 Azure 리소스를 만들 때처럼 DevOps Projects 리소스를 만듭니다. DevOps Projects는 다양한 구성 옵션에 대해 마법사와 같은 단계별 환경을 제공합니다.  

초기 설정의 일부로 여러 구성 옵션을 선택합니다. 이러한 옵션에는 다음이 포함됩니다.

* 제공된 샘플 앱을 사용 또는 자신의 코드 가져오기
* 앱 언어 선택
* 언어를 기준으로 앱 프레임워크 선택
* Azure 서비스 선택(배포 대상)
* 새 Azure DevOps 조직 만들기 또는 기존 조직 사용 
* Azure 구독 선택
* Azure 서비스 위치 선택
* Azure 서비스에 대한 다양한 가격 책정 계층에서 선택

DevOps Projects를 사용하여 Azure Portal의 DevOps Projects 대시보드에서 모든 리소스를 한 곳에서 삭제할 수 있습니다.

## <a name="devops-projects-and-azure-devops-integration"></a>DevOps Projects 및 Azure DevOps 통합

DevOps Projects는 Azure DevOps를 기준으로 합니다. DevOps Projects를 Azure Pipelines에서 CI/CD 파이프라인을 설정하는 데 필요한 모든 작업을 자동화합니다. 새 또는 기존 Azure DevOps 조직에서 Git 리포지토리를 만들고, 샘플 애플리케이션 또는 기존 코드를 새 Git 리포지토리에 커밋합니다.  

자동화는 또한 모든 새로운 코드 커밋이 빌드를 시작할 수 있도록 빌드를 위한 CI 트리거를 설정합니다. DevOps Projects는 CD 트리거를 만들고 원하는 Azure 서비스에 모든 새로운 성공적인 빌드를 배포합니다.  

빌드 및 릴리스 파이프라인은 추가 시나리오에 맞게 사용자 지정할 수 있습니다. 또한 다른 프로젝트에서 사용할 수 있도록 빌드 및 릴리스 파이프라인을 복제할 수 있습니다.

DevOps Project를 만든 후 다음을 수행할 수 있습니다.

* 빌드 및 릴리스 파이프라인 사용자 지정
* 끌어오기 요청을 사용하여 코드 흐름을 관리하고 높은 품질을 유지함
* 코드를 병합하여 품질 표시줄을 높이기 전에 각 커밋 테스트 및 빌드
* 애플리케이션과 함께 백로그 및 문제점 추적

## <a name="how-do-i-start-using-devops-projects"></a>DevOps Projects를 사용하기 시작하려면 어떻게 해야 하나요?

* [DevOps Projects 시작](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>DevOps Projects 비디오

* [Azure DevOps Projects를 사용하여 CI/CD 만들기](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
