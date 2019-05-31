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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123226"
---
1. [Azure Machine Learning Service 작업 영역 만들기](../articles/machine-learning/service/setup-create-workspace.md#portal)의 지침에 따라 다음 작업을 수행합니다.
    * Miniconda 환경 만들기
    * Python용 Azure Machine Learning SDK 설치
    * 작업 영역 만들기
    * 작업 영역 구성 파일(**aml_config/config.json**)을 작성합니다.
    
1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 다음 방법을 사용하여 작업 영역 구성 파일을 추가합니다.
    * 1단계에서 만든 **aml_config/config.json** 파일을 복제된 디렉터리에 복사합니다.

    * [Azure Portal](https://ms.portal.azure.com)에서, 해당 작업 영역의 **개요** 섹션에서 **config.json 다운로드**를 선택합니다. 

    ![config.json 다운로드](./media/aml-dsvm-server/download-config.png)

    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb)의 코드를 사용하여 새 작업 영역을 만듭니다.

1. 복제된 디렉터리에서 노트북 서버를 시작합니다.
    
    ```shell
    jupyter notebook
    ```