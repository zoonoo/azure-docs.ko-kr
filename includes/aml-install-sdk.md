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
ms.date: 07/27/2018
ms.openlocfilehash: 12f0f17a7c25696d2c8ff5b427f4b103617e5678
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010750"
---
명령 프롬프트 또는 셸 창에서 numpy 및 cython을 사용하여 conda 패키지 관리자 환경을 만들고 활성화합니다. 이 예제에서는 Python 3.6을 사용합니다.

  + Windows에서:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       conda activate myenv
       ```

  + Linux 또는 macOS:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       source activate myenv
       ```

SDK를 설치합니다.
   ```sh 
   pip install azureml-sdk
   ```
