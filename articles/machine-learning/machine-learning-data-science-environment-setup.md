---
title: "Azure에서 데이터 과학 환경 설정 | Microsoft Docs"
description: "팀 데이터 과학 프로세스에서 사용되는 Azure의 데이터 과학 환경을 설정합니다."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 1796f7a7cd174d7ed6582878d72c59995aac41cb
ms.openlocfilehash: 4f2f66288428aa0aa41abb40ce0e43c4848543ff
ms.lasthandoff: 02/08/2017


---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>팀 데이터 과학 프로세스에서 사용되는 데이터 과학 환경 설정
팀 데이터 과학 프로세스는 데이터의 저장, 처리 및 분석을 위해 다양한 데이터 과학 환경을 사용합니다. 여기에는 Azure Blob Storage, 여러 유형의 Azure 가상 컴퓨터, HDInsight(Hadoop) 클러스터 및 Azure Machine Learning 작업 영역이 포함됩니다. 사용할 환경에 대한 결정은 모델링할 데이터의 유형 및 양과 클라우드에서 해당 데이터의 대상에 따라 달라집니다. 

* 이러한 결정을 내릴 때 고려할 질문에 대한 지침은 [Azure Machine Learning 데이터 과학 환경 계획](machine-learning-data-science-plan-your-environment.md)을 참조하세요. 
* 고급 분석을 수행할 때 발생할 수 있는 일부 시나리오의 카탈로그는 [팀 데이터 과학 프로세스 시나리오](machine-learning-data-science-plan-sample-scenarios.md)

이 메뉴는 팀 데이터 과학 프로세스에서 사용되는 다양한 데이터 과학 환경의 설정 방법을 설명하는 토픽에 연결됩니다.

[!INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

**Microsoft DSVM(데이터 과학 가상 컴퓨터)**은 Azure VM(가상 컴퓨터) 이미지로 사용할 수 있습니다. 이 VM은 데이터 분석 및 Machine Learning에 흔히 사용되는 몇 가지 인기 있는 도구로 사전 설치되고 구성됩니다. Windows 및 Linux에는 DSVM를 사용할 수 있습니다. 자세한 내용은 [Linux 및 Windows용 클라우드 기반 데이터 과학 가상 컴퓨터에 대한 소개](machine-learning-data-science-virtual-machine-overview.md)를 참조하세요.


