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
ms.date: 01/25/2019
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302206"
---
1. [Azure Machine Learning Python 빠른 시작](../articles/machine-learning/service/quickstart-create-workspace-with-python.md)을 완료하여 작업 영역을 만듭니다.  원한다면 **Notebook 사용** 섹션을 건너뛰어도 됩니다.
1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. 이러한 방법 중 하나를 사용하여 작업 영역 구성 파일을 추가합니다.
    * 필수 구성 요소 빠른 시작을 사용하여 만든 **aml_config\config.json** 파일을 복제된 디렉터리에 복사합니다.
    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 노트북의 코드를 사용하여 복제된 디렉터리에 새 작업 영역을 만듭니다.
1. 복제된 디렉터리에서 노트북 서버를 시작합니다.
    
    ```shell
    jupyter notebook
    ```