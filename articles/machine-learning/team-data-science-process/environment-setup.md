---
title: Azure에서 데이터 과학 환경 설정 - Team Data Science Process
description: 팀 데이터 과학 프로세스에서 사용되는 Azure의 데이터 과학 환경을 설정합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2c66da6717018237aebaf5fdaf803a8ac98f82be
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459111"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>팀 데이터 과학 프로세스에서 사용되는 데이터 과학 환경 설정
팀 데이터 과학 프로세스는 데이터의 저장, 처리 및 분석을 위해 다양한 데이터 과학 환경을 사용합니다. 여기에는 Azure Blob Storage, 여러 유형의 Azure 가상 머신, HDInsight(Hadoop) 클러스터 및 Azure Machine Learning 작업 영역이 포함됩니다. 사용할 환경에 대한 결정은 모델링할 데이터의 유형 및 양과 클라우드에서 해당 데이터의 대상에 따라 달라집니다. 

* 이러한 결정을 내릴 때 고려할 질문에 대한 지침은 [Azure Machine Learning 데이터 과학 환경 계획](plan-your-environment.md)을 참조하세요. 
* 고급 분석을 수행할 때 발생할 수 있는 일부 시나리오의 카탈로그는 [팀 데이터 과학 프로세스 시나리오](plan-sample-scenarios.md)

다음 문서에서는 팀 데이터 과학 프로세스에 사용되는 다양한 데이터 과학 환경을 설정하는 방법에 대해 설명합니다.

* [Azure storage-account](../../storage/common/storage-quickstart-create-account.md)
* [HDInsight(Hadoop) 클러스터](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio 작업 영역](../studio/create-workspace.md)

**Microsoft DSVM(데이터 과학 Virtual Machine)** 은 Azure VM(가상 머신) 이미지로 사용할 수 있습니다. 이 VM은 데이터 분석 및 Machine Learning에 흔히 사용되는 몇 가지 인기 있는 도구로 사전 설치되고 구성됩니다. Windows 및 Linux에는 DSVM를 사용할 수 있습니다. 자세한 내용은 [Linux 및 Windows용 클라우드 기반 데이터 과학 Virtual Machine에 대한 소개](../data-science-virtual-machine/overview.md)를 참조하세요.

다음을 만드는 방법을 알아봅니다.

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
- [심층 학습 VM](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
