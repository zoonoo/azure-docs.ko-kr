---
title: Azure IoT Edge용 AI 도구 키트 사용 | Microsoft Docs
description: 이 문서에서는 Azure IoT Edge용 AI 도구 키트를 사용하는 방법을 설명합니다.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 9c4d1be552005419ba2e9758a37e02f15fd20155
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268481"
---
# <a name="use-the-ai-toolkit-for-azure-iot-edge"></a>Azure IoT Edge용 AI 도구 키트 사용

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

인공 지능, 기계 학습 및 고급 분석 기능을 Edge 장치에 가져옵니다. Azure IoT Edge용 AI 도구 키트를 사용하면 데이터가 있는 위치에 AI 및 기계 학습을 배포할 수 있습니다. REST API를 사용하여 모델을 Docker 컨테이너로 조작한 다음 Azure IoT Hub가 있는 Edge 디바이스로 모델을 푸시합니다. Azure IoT Edge용 AI 도구 키트는 제한적 또는 간헐적 연결이 있거나 연결이 없는 위치에 지능형 기능, 탄력적 계산 기능 및 클라우드 기능을 제공합니다.

Azure IoT Edge용 AI 도구 키트는 스크립트, 코드 및 배포 가능한 컨테이너의 모음입니다. 예를 들어 Azure Machine Learning 및 Azure IoT Hub를 통한 예측 유지 관리, 이미지 분류 및 음성 처리뿐만 아니라 사용자 지정 모델 배포도 포함되어 있습니다. 도구 키트에 포함된 모델은 있는 그대로 사용할 수 있지만, 개발자가 모든 소스 코드와 데이터를 필요에 따라 사용자 지정하는 데 사용할 수 있습니다.

Azure IoT Edge용 AI 도구 키트 공용 GitHub 리포지토리는 [aka.ms/AI-toolkit](https://aka.ms/AI-toolkit)에 있습니다.