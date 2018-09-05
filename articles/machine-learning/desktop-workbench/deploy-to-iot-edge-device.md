---
title: Azure Machine Learning 모델을 Azure IoT Edge 장치에 배포 | Microsoft Docs
description: 이 문서에서는 Azure IoT Edge 장치에 Azure Machine Learning 모델을 배포하는 방법에 대해 설명합니다.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 24d3cf0c4b1a1283e7a6a7f61f0bb23dae7143d5
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43113562"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Azure Machine Learning 모델을 Azure IoT Edge 장치에 배포

Azure Machine Learning 모델을 Docker 기반 웹 서비스로 컨테이너화할 수 있습니다. Azure IoT Edge를 사용하여 장치에 원격으로 컨테이너를 배포할 수 있습니다. 이러한 서비스를 함께 사용하여 빠른 응답 시간 및 적은 데이터 전송에 대한 에지에서 모델을 실행합니다. 

추가 스크립트 및 지침은 [Azure IoT Edge용 AI 도구](http://aka.ms/AI-toolkit)에서 찾을 수 있습니다.

## <a name="operationalize-the-model"></a>모델 운영

Azure IoT Edge 모듈은 컨테이너 이미지를 기반으로 합니다. IoT Edge 장치에 Machine Learning 모델을 배포하려면 Docker 이미지를 만들어야 합니다.

[Azure Machine Learning 모델 관리 웹 서비스 배포](model-management-service-deploy.md)의 지침에 따라 모델을 운영화하여 모델이 포함된 Docker 이미지를 만듭니다.

## <a name="deploy-to-azure-iot-edge"></a>Azure IoT Edge에 배포

모델의 이미지를 만든 후 모든 Azure IoT Edge 장치에 배포할 수 있습니다. 모든 Machine Learning 모델이 IoT Edge 장치에서 실행될 수 있습니다. 

### <a name="set-up-an-iot-edge-device"></a>IoT Edge 장치 설정

Azure IoT Edge 설명서를 사용하여 장치를 준비합니다. 

1. [Azure IoT Hub를 사용하여 장치를 등록](../../iot-edge/how-to-register-device-portal.md)합니다. 이 프로세스의 출력은 물리적 장치를 구성하는 데 사용할 수 있는 연결 문자열입니다. 
2. 물리적 장치에 IoT Edge 런타임을 설치하고, 연결 문자열을 사용하여 구성합니다. [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) 또는 [Linux](../../iot-edge/how-to-install-iot-edge-linux.md) 장치에 런타임을 설치할 수 있습니다.  


### <a name="find-the-machine-learning-container-image-location"></a>Machine Learning 컨테이너 이미지 위치 찾기
Machine Learning 컨테이너 이미지의 위치가 필요합니다. 컨테이너 이미지 위치를 찾으려면:

1. [Azure Portal](http://portal.azure.com/)에 로그인합니다.
2. **Azure Container Registry**에서 검사할 레지스트리를 선택합니다.
3. 레지스트리에서 **리포지토리**를 클릭하면 모든 리포지토리와 해당 이미지 목록이 표시됩니다.

Azure Portal에서 컨테이너 레지스트리를 보는 동안 컨테이너 레지스트리 자격 증명을 검색합니다. 해당 개인 레지스트리에서 이미지를 끌어올 수 있도록 이러한 자격 증명을 IoT Edge 장치에 부여 받아야 합니다. 

1. 컨테이너 레지스트리에서 **액세스 키**를 클릭합니다. 
2. 아직 설정되지 않은 경우 관리 사용자를 **사용하도록 설정**합니다. 
3. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 저장합니다. 

### <a name="deploy-the-container-image-to-your-device"></a>장치에 컨테이너 이미지 배포

컨테이너 이미지 및 컨테이너 레지스트리 자격 증명을 사용하여 IoT Edge 장치에 기계 학습 모델을 배포할 준비가 되었습니다. 

[Azure Portal에서 IoT Edge 모듈 배포](../../iot-edge/how-to-deploy-modules-portal.md)의 지침을 따라 IoT Edge 장치에서 모델을 시작합니다. 











