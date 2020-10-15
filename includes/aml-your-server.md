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
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "79486134"
---
1. [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)의 지침을 사용하여 Python용 Azure Machine Learning SDK를 설치합니다.

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