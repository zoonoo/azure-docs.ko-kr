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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395513"
---
1. [Azure Machine Learning Service 작업 영역 만들기](../articles/machine-learning/service/setup-create-workspace.md#portal)의 지침을 사용하여 Miniconda 환경을 만들고, 작업 영역을 만들고, 작업 영역 구성 파일(**aml_config/config.json**)을 씁니다.

1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 다음 방법 중 하나를 사용하여 작업 영역 구성 파일을 추가합니다.
    * 필수 구성 요소 빠른 시작을 사용하여 만든 **aml_config\config.json** 파일을 복제된 디렉터리에 복사합니다.
    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb)의 코드를 사용하여 새 작업 영역을 만듭니다.
1. 복제된 디렉터리에서 노트북 서버를 시작합니다.
    
    ```shell
    jupyter notebook
    ```