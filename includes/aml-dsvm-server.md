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
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485958"
---
1. [Azure Machine Learning 작업 영역을 만듭니다](../articles/machine-learning/how-to-manage-workspace.md).

1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 이러한 방법 중 하나를 사용하여 복제된 디렉터리에 작업 영역 구성 파일을 추가합니다.

    * [Azure Portal](https://ms.portal.azure.com)에서, 해당 작업 영역의 **개요** 섹션에서 **config.json 다운로드**를 선택합니다. 

    ![config.json 다운로드](./media/aml-dsvm-server/download-config.png)

    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 노트북의 코드를 사용하여 복제된 디렉터리에 새 작업 영역을 만듭니다.

1. 복제된 디렉터리에서 노트북 서버를 시작합니다.

    ```bash
    jupyter notebook
    ```