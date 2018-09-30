---
title: Azure Machine Learning용 개발 환경 | Microsoft Docs
description: Azure Machine Learning 서비스에 사용할 수 있는 개발 환경에 대한 개요입니다. Python 3이 개발 환경의 유일한 요구 사항이지만 Conda 환경도 사용하는 것이 좋습니다. 개발 도구로는 Jupyter Notebook, Azure Notebooks 및 IDE/코드 편집기를 사용하는 것이 좋습니다.
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 260e209bcf00396ee545851684038578c4fd148a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971045"
---
# <a name="development-environment-for-azure-machine-learning"></a>Azure Machine Learning용 개발 환경 

Azure Machine Learning 서비스와 함께 사용할 수 있는 개발 환경에 대해 알아봅니다. 

Azure Machine Learning 서비스는 플랫폼에 구애 받지 않으며 특정한 개발 환경이 필요하지 않습니다. __Python 3__이 필요하며 __Conda__를 사용하여 격리된 환경을 만드는 것이 좋습니다. __이러한 요구 사항을 충족하는 개발 환경이 이미 있는 경우__에는 이 문서를 건너뛰고 [개발 환경 구성](how-to-configure-environment.md) 문서로 이동하면 됩니다.

이 문서의 나머지 부분에서는 권장되는 개발 환경을 설명합니다.

* __Jupyter 노트북__
* __Azure 노트__
* __IDE(통합 개발 환경) 및 텍스트 편집기__
* __데이터 과학 Virtual Machine__

두 가지 노트북과 IDE가 모두 확장이 가능하기 때문에 이들 환경을 비교하기가 어렵습니다. 예를 들어 일부 IDE는 Jupyter Notebook에 대한 클라이언트로 사용할 수 있습니다. 일반적으로, __노트북__은 __대화형 실험__ 및 __시각화__를 위해 설계됩니다. __IDE 및 코드 편집기__는 __코드 품질을 향상__시키고 __외부 시스템(예: 버전 제어)과 통합__할 수 있는 도구를 제공합니다.

> [!TIP]
> 한 가지 환경을 사용한다고 해서 다른 환경을 사용하지 못할 이유는 없습니다. Notebook과 IDE는 도구일 뿐이며 필요한 경우 혼합할 수 있습니다. 예를 들어, 노트북에서 실험을 시작한 다음, IDE에서 편집하고 디버깅하기 위한 Python 스크립트로 내보낼 수 있습니다.
>
> 바로 이런 이유 때문에 Data Science Virtual Machine에는 Jupyter Notebook과 많이 사용되는 몇 가지 Python IDE가 모두 제공됩니다.

## <a name="jupyter-notebooks"></a>Jupyter 노트북

Jupyter Notebook은 [Jupyter 프로젝트](https://jupyter.org/)의 일부입니다. 라이브 코드 및 설명 테스트와 그래픽을 혼합하는 문서를 만드는 대화형 코딩 환경을 제공하는 것에 중점을 둡니다. Jupyter Notebook은 다양한 플랫폼에 설치할 수 있습니다.

Jupyter Notebook을 직접 설치하면 필요에 맞게 환경을 설치하고 구성할 수 있습니다. 단, 시스템 유지 관리를 사용자가 책임져야 합니다.

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com)(미리 보기)는 Azure 클라우드의 노트북 환경입니다. Jupyter를 기반으로 하며 많이 사용되는 라이브러리가 미리 로드되어 있는 환경을 제공합니다. Azure Machine Learning SDK가 Azure Notebooks에 이미 설치되어 있어서 거의 설정을 하지 않고 실험을 시작할 수 있습니다.

Azure Notebooks는 노트북을 공유하는 프로세스를 간소화합니다. Azure Notebooks 인터페이스에서 노트북에 URL을 공유하거나 라이브러리를 공개하거나 소셜 미디어를 공유할 수 있습니다.

Azure Notebooks의 단점은 환경을 완전히 제어할 수 없고 필요한 사용자 지정 소프트웨어를 설치하지 못할 수도 있다는 점입니다. 또한 공유 환경이기 때문에 노트북이 전용 Jupyter Notebook 설치 환경보다 느리게 실행될 수 있습니다.

## <a name="ides-and-code-editors"></a>IDE 및 코드 편집기

Azure Machine Learning에서 작동하는 IDE와 코드 편집기는 많이 있습니다. 유일한 소프트웨어 요구 사항은 Azure Machine Learning SDK이며, 이것은 `pip` 유틸리티를 사용하여 설치할 수 있습니다.

[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 것이 좋습니다. Python 언어와 Azure Machine Learning 모두를 사용할 수 있는 도구를 제공하기 때문입니다. Linux, macOS 및 Windows 플랫폼에서 사용할 수 있습니다.

## <a name="data-science-virtual-machine"></a>데이터 과학 Virtual Machine

DSVM(Data Science Virtual Machine)은 이전 환경을 결합한 것입니다. Jupyter Notebooks, Visual Studio Code 및 Azure Machine Learning SDK가 미리 설치되어 있는 Azure 플랫폼의 VM입니다. VM을 만드는 것은 Azure Notebooks보다 복잡하지만 머신을 처음부터 설정하는 것보다는 덜 복잡합니다. 필요한 소프트웨어가 VM 이미지에 미리 설치되어 있어서, VM이 만들어지기만 하면 Azure Machine Learning으로 신속하게 실험을 시작할 수 있습니다.

DSVM에서는 CPU, GPU 및 메모리와 같은 필요한 계산 리소스를 선택할 수 있습니다. 또한 PyCharm은 물론 TensorFlow, Keras 및 PyTorch와 같이 널리 사용되는 기계 학습 소프트웨어가 미리 설치되어 있습니다. 필요한 소프트웨어가 설치되어 있지 않으면 직접 설치할 수 있습니다.

미리 설치되어 있는 항목에 대한 자세한 내용은 [Data Science Virtual Machine이란?](../data-science-virtual-machine/overview.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

개발 환경에 대해 알아보았으면 [개발 환경을 구성하는 방법](how-to-configure-environment.md)을 알아볼 차례입니다.

