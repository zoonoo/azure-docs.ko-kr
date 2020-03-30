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
ms.date: 08/23/2019
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122589"
---
| 컴퓨팅 대상 | 사용 대상 | GPU 지원 | FPGA 지원 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| [로컬&nbsp;&nbsp;웹 서비스](../articles/machine-learning/how-to-deploy-and-where.md#local) | 테스트/디버깅 | &nbsp; | &nbsp; | 제한된 테스트 및 문제 해결에 사용합니다. 하드웨어 가속은 로컬 시스템의 라이브러리 사용에 따라 달라집니다.
| [Azure 기계 학습&nbsp;계산&nbsp;인스턴스 웹 서비스](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | 테스트/디버깅 | &nbsp; | &nbsp; | 제한된 테스트 및 문제 해결에 사용합니다.
| [Azure Kubernetes 서비스(AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | 실시간 추론 |  [예(웹](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) 서비스 배포) | [예](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |대규모 프로덕션 배포에 사용합니다. 배포된 서비스의 빠른 응답 시간 및 자동 크기 조정을 제공합니다. 클러스터 자동 크기 조정은 Azure 기계 학습 SDK를 통해 지원되지 않습니다. AKS 클러스터의 노드를 변경하려면 Azure 포털에서 AKS 클러스터에 대한 UI를 사용합니다. AKS는 디자이너가 사용할 수 있는 유일한 옵션입니다. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | 테스트 또는 개발 | &nbsp;  | &nbsp; | 48GB 미만의 RAM이 필요한 저용량 CPU 기반 워크로드에 사용합니다. |
| [Azure Machine Learning 컴퓨팅 인스턴스](../articles/machine-learning/how-to-use-parallel-run-step.md) | (미리 보기) 일괄&nbsp;처리 추론 | [예(기계](../articles/machine-learning/how-to-use-parallel-run-step.md) 학습 파이프라인) | &nbsp;  | 서버리스 계산에서 일괄 처리 점수를 실행합니다. 일반 및 우선 순위가 낮은 VM을 지원합니다. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (미리 보기) 실시간 추론 | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (미리 보기) IoT&nbsp;모듈 |  &nbsp; | &nbsp; | IoT 디바이스에 ML 모델을 배포하고 제공합니다. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | 비아 IoT 에지 |  &nbsp; | yes | IoT 디바이스에 ML 모델을 배포하고 제공합니다. |

> [!NOTE]
> 로컬, Azure 기계 학습 계산 인스턴스 및 Azure 기계 학습 계산 클러스터와 같은 계산 대상은 학습 및 실험에 대한 GPU를 지원하지만 __웹 서비스로 배포될 때__ 추론을 위해 GPU를 사용하는 것은 Azure Kubernetes 서비스에서만 지원됩니다.
>
> __기계 학습 파이프라인으로 점수를 매기는 경우 추론을__ 위해 GPU를 사용하는 것은 Azure 기계 학습 계산에서만 지원됩니다.