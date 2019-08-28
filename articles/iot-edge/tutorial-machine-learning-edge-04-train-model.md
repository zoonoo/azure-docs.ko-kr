---
title: 모델 학습 및 배포 - Azure IoT Edge의 Machine Learning | Microsoft Docs
description: Azure Machine Learning을 사용하여 기계 학습 모델을 학습한 후 Azure IoT Edge 모듈로 배포할 수 있는 컨테이너 이미지로 모델을 패키지화합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: acf0b1984eb3e68858be6ed68731612448e672f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432702"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>자습서: Azure Machine Learning 모델 학습 및 배포

> [!NOTE]
> 이 문서는 IoT Edge에서 Azure Machine Learning을 사용하는 방법에 대한 자습서 시리즈의 일부입니다. 이 문서로 바로 이동한 경우에는 학습 효과를 극대화할 수 있도록 시리즈의 [첫 번째 문서](tutorial-machine-learning-edge-01-intro.md)부터 시작하는 것이 좋습니다.

이 문서에서는 Azure Notebooks에서 먼저 Azure Machine Learning을 사용하여 기계 학습 모델을 학습한 후 Azure IoT Edge 모듈로 배포할 수 있는 컨테이너 이미지로 모델을 패키지화합니다. Azure Notebooks는 기계 학습 모델을 실험, 학습 및 배포하는 데 사용되는 기본 블록인 Azure Machine Learning 서비스 작업 영역을 활용합니다.

자습서 이 부분은 두 개의 Notebook으로 작업이 분리되어 있습니다.

* **01-turbofan\_regression.ipynb:** 이 Notebook은 Azure Machine Learning을 사용하여 모델을 학습하고 게시하는 단계를 안내합니다. 일반적으로 관련된 단계는 다음과 같습니다.

  1. 학습 데이터 다운로드, 준비 및 살펴보기
  2. 서비스 작업 영역을 사용하여 기계 학습 실험을 생성 및 실행
  3. 실험에서 모델 결과를 평가
  4. 서비스 작업 영역에 가장 적합한 모델을 게시

* **02-turbofan\_deploy\_model.ipynb:** 이 Notebook은 이전 Notebook에서 만든 모델을 사용하여 Azure IoT Edge 디바이스에 배포할 준비가 된 컨테이너 이미지를 만듭니다.

  1. 모델에 대한 채점 스크립트 만들기
  2. 이미지 만들기 및 게시
  3. 이미지를 Azure Container Instance에 웹 서비스로 배포
  4. 웹 서비스를 사용하여 모델과 이미지가 예상대로 작동하는지 확인

이 문서의 단계는 일반적으로 데이터 과학자가 수행할 수 있습니다.

## <a name="set-up-azure-notebooks"></a>Azure Notebooks 설정

Azure Notebooks를 사용하여 2개의 Jupyter Notebook 및 지원 파일을 호스팅합니다. 여기에서는 Azure Notebooks 프로젝트를 만들고 구성합니다. Jupyter 및/또는 Azure Notebooks를 사용한 적이 없는 경우, 다음과 같은 소개 문서를 참조하세요.

* **빠른 시작:** [Notebook 만들기 및 공유](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **자습서:** [Python을 사용하여 Jupyter Notebook을 만들고 실행](../notebooks/tutorial-create-run-jupyter-notebook.md)

이전의 개발자 가상 머신처럼, Azure Notebooks를 사용하면 연습 환경을 일관되게 유지할 수 있습니다.

> [!NOTE]
> 일단 설치하면, Azure Notebooks 서비스를 원하는 머신에서 액세스할 수 있습니다. 설치하는 동안, 필요한 파일이 모두 있는 개발 가상 머신을 사용해야 합니다.

### <a name="create-an-azure-notebooks-account"></a>Azure Notebooks 계정 만들기

Azure Notebook 계정은 Azure 구독에서 독립적입니다. Azure Notebooks를 사용하려면 계정을 만들어야 합니다.

1. [Azure Notebooks](https://notebooks.azure.com)로 이동합니다.

2. 페이지의 오른쪽 위에서 **로그인**을 클릭합니다.

3. 회사 또는 학교 계정(Azure Active Directory)이나 개인 계정(Microsoft 계정)으로 로그인합니다.

4. Azure Notebooks를 전에 사용한 적이 없으면, Azure Notebooks 앱에 액세스 권한을 부여하라는 메시지가 표시됩니다.

5. Azure Notebooks에 대한 사용자 ID를 만듭니다.

### <a name="upload-jupyter-notebooks-files"></a>Jupyter 노트북 파일 업로드

이 단계에서는 새로운 Azure Notebooks 프로젝트를 만들어서 여기에 파일을 업로드합니다. 특히 다음과 같은 파일을 업로드합니다.

* **01-turbofan\_regression.ipynb**: Azure 스토리지 계정에서 디바이스 도구에 의해 생성된 데이터를 다운로드하고, 분류자 학습을 위한 데이터를 살펴보고 준비하며, 모델을 학습하고, Test\_FD003.txt 파일에 있는 테스트 데이터 세트를 사용하여 데이터를 테스트하고, 마지막으로 Machine Learning Service 작업 영역에 분류자 모델을 저장하는 과정을 안내하는 Jupyter Notebook 파일입니다.

* **02-turbofan\_deploy\_model.ipynb:** Machine Learning Service 작업 영역에 저장된 분류자 모델을 사용하여 컨테이너 이미지를 생성하는 과정을 안내하는 Jupyter Notebook입니다. 이미지가 생성되면 Notebook이이미지를 웹 서비스로 배포하는 과정을 안내하기 때문에 이미지가 예상대로 작동하는지 확인할 수 있습니다. 검사된 이미지는 이 자습서의 [사용자 지정 IoT Edge 모듈 만들기 및 배포](tutorial-machine-learning-edge-06-custom-modules.md) 부분에서 Edge 디바이스에 배포됩니다.

* **Test\_FD003.txt:** 이 파일은 학습된 분류자의 유효성을 검사할 때 테스트 세트로 사용할 데이터를 포함합니다. 예제의 단순성을 위해, 원래 컨테스트에 제공된 테스트 데이터가 테스트 세트로 사용됩니다.

* **RUL\_FD003.txt:** 이 파일은 Test\_FD003.txt 파일에 있는 각 디바이스의 마지막 주기에 대한 RUL을 포함합니다. 데이터에 대한 자세한 설명은 C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan에서 **readme.txt** 및 **Damage Propagation Modeling.pdf** 파일을 참조하세요.

* **Utils.py:** 데이터 작업을 위한 일련의 Python 유틸리티 함수를 포함합니다. 첫 번째 Notebook에는 함수에 대한 자세한 설명이 있습니다.

* **README.md:** Notebook 사용을 설명하는 추가 정보입니다.

새 프로젝트를 만들어서 Notebook에 파일을 업로드합니다.

1. 맨 위 메뉴 모음에서 **내 프로젝트**를 선택합니다.

1. **+ 새 프로젝트**를 선택합니다. 이름 및 ID를 제공합니다. 프로젝트를 공개하거나 추가 정보가 있을 필요는 없습니다.

1. **업로드**와 **컴퓨터에서**를 차례로 선택합니다.

1. **파일 선택**을 선택합니다.

1. **C:\source\IoTEdgeAndMlSample\AzureNotebooks**로 이동합니다. 모든 파일을 선택하고 **열기**를 클릭합니다.

1. **업로드**를 선택하여 업로드를 시작하고 프로세스가 완료되면 **완료**를 선택합니다.

## <a name="run-azure-notebooks"></a>Azure Notebooks 실행

프로젝트가 만들어졌으면 **01-turbofan\_regression.ipynb** Notebook을 실행합니다.

1. turbofan 프로젝트 페이지에서 **01-turbofan\_regression.ipynb**를 선택합니다.

    ![실행할 첫 번째 Notebook 선택](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. 메시지가 나타나면 대화 상자에서 Python 3.6 Kernel을 선택하고 **Set Kernel**(커널 설정)을 선택합니다.

3. Notebook이 **신뢰할 수 없음**으로 나열되면 Notebook의 오른쪽 위에서 **신뢰할 수 없음** 위젯을 클릭합니다. 대화 상자가 열리면, **신뢰**를 선택합니다.

4. Notebook의 지침을 따릅니다.

    * `Ctrl + Enter` 키는 셀을 실행합니다.
    * `Shift + Enter` 키는 셀을 실행하고 다음 셀로 이동합니다.
    * 셀이 실행 중이면 **[\*]** 와 같이 대괄호 사이에 별표가 있습니다. 완료되면 별표가 숫자로 바뀌고 관련된 출력이 아래에 나타납니다. 셀은 이전 셀의 작업에 기반하는 경우가 많기 때문에 한 번에 하나의 셀만 실행할 수 있습니다.

5. **01-turbofan\_regression.ipynb** Notebook 실행을 마치면 프로젝트 페이지로 돌아갑니다.

6. **02-turbofan\_deploy\_model.ipynb**를 열고 이 섹션의 단계를 반복하여 두 번째 Notebook을 실행합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Notebooks에서 실행되는 2개의 Jupyter Notebook을 통해 turbofan 디바이스의 데이터를 사용하여 RUL(잔여 수명) 분류자를 학습하고, 분류자를 모델로 저장하고, 컨테이너 이미지를 만들고, 이미지를 웹 서비스로 배포하여 테스트했습니다.

계속해서 다음 문서를 진행하면 IoT Edge 디바이스를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [IoT Edge 디바이스 구성](tutorial-machine-learning-edge-05-configure-edge-device.md)
