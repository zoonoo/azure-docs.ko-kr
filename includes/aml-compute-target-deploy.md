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
ms.date: 05/30/2019
ms.openlocfilehash: a463ac9f9584cb13cadbcf79674d56b2f8e47c2c
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753081"
---
| 계산 대상 | 사용 현황 | GPU 지원 | FPGA 지원 | 설명 |
| ----- | ----- | ----- | ----- | ----- |
| [로컬 웹 서비스](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | 테스트/디버그 | 아마도 | &nbsp; | 제한 된 테스트 및 문제 해결에 적합 합니다. 하드웨어 가속 라이브러리를 사용 하 여 로컬 시스템에 따라 달라 집니다.
| [AKS(Azure Kubernetes Service)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | 실시간 유추 |  [예](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [예](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |확장성이 뛰어난 프로덕션 배포에 적합합니다. 자동 크기 조정 및 빠른 응답 시간을 제공합니다.  AKS는 시각적 인터페이스에 사용할 수 있는 유일한 옵션입니다. |
| [ACI(Azure Container Instances)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | 테스트 또는 개발 | &nbsp;  | &nbsp; | 낮은 등급, 필요한 CPU 기반 워크 로드에 대 한 좋은 < 48 GB RAM |
| [Azure Machine Learning 컴퓨팅](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (미리 보기) 일괄 처리 유추 | 예 | &nbsp;  | 서버 리스 계산에서 점수 매기기 일괄 처리를 실행 합니다. 일반 및 낮은 우선 순위 Vm 지원합니다. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (미리 보기) IoT 모듈 |  &nbsp; | &nbsp; | 배포 및 IoT 장치에서 기계 학습 모델을 제공 합니다. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | IoT Edge를 통해 |  &nbsp; | 예 | 배포 및 IoT 장치에서 기계 학습 모델을 제공 합니다. |