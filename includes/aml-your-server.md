---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510990"
---
1. [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)의 지침을 사용하여 Python용 Azure Machine Learning SDK를 설치합니다.

1. [Azure Machine Learning 작업 영역](../articles/machine-learning/how-to-manage-workspace.md)을 만듭니다.

1. [구성 파일](../articles/machine-learning/how-to-configure-environment.md#workspace) 파일(**aml_config/config.json**)을 작성합니다.

1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 복제된 디렉터리에서 노트북 서버를 시작합니다.

    ```bash
    jupyter notebook
    ```