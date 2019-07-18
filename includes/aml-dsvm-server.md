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
ms.openlocfilehash: a882a874574395095e98079cd0f8aa4a4987c749
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391766"
---
1. [Azure Machine Learning Service 작업 영역을 만듭니다](../articles/machine-learning/service/setup-create-workspace.md).

1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 이러한 방법 중 하나를 사용하여 복제된 디렉터리에 작업 영역 구성 파일을 추가합니다.

    * [Azure Portal](https://ms.portal.azure.com)에서, 해당 작업 영역의 **개요** 섹션에서 **config.json 다운로드**를 선택합니다. 

    ![config.json 다운로드](./media/aml-dsvm-server/download-config.png)

    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 노트북의 코드를 사용하여 복제된 디렉터리에 새 작업 영역을 만듭니다.

1. 복제된 디렉터리에서 노트북 서버를 시작합니다.

    ```shell
    jupyter notebook
    ```