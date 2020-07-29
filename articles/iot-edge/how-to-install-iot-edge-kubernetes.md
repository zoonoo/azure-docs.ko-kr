---
title: Kubernetes에 IoT Edge를 설치 하는 방법 | Microsoft Docs
description: 로컬 개발 클러스터 환경을 사용 하 여 Kubernetes에 IoT Edge를 설치 하는 방법에 대해 알아봅니다.
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471288"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes에 IoT Edge를 설치 하는 방법 (미리 보기)

IoT Edge를 사용 하 여 복원 력이 높은 고가용성 인프라 계층으로 사용 하는 Kubernetes와 통합할 수 있습니다. 다음은이 지원이 높은 수준의 IoT Edge 솔루션에 적합 한 위치입니다.

![k8s 소개](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>이러한 통합에 적합 한 멘 탈 모델은 Linux 및 Windows 외에도 응용 프로그램을 실행할 수 IoT Edge 다른 운영 환경으로 Kubernetes 생각 하는 것입니다.

## <a name="architecture"></a>Architecture 
Kubernetes에서 IoT Edge는에 지 작업 배포에 대 한 CRD ( *사용자 지정 리소스 정의* )를 제공 합니다. IoT Edge 에이전트는 로컬 클러스터 상태를 사용 하 여 클라우드 관리 desired 상태를 조정 하는 *CRD 컨트롤러* 의 역할을 가정 합니다.

모듈 수명은 Kubernetes scheduler에서 관리 하며, 모듈 가용성을 유지 하 고 배치를 선택 합니다. IoT Edge는 위쪽에서 실행 되는 edge 응용 프로그램 플랫폼을 관리 하 고, IoT Hub에 지정 된 원하는 상태를에 지 클러스터의 상태로 지속적으로 조정 합니다. 응용 프로그램 모델은 IoT Edge 모듈과 경로를 기반으로 하는 친숙 한 모델입니다. IoT Edge 에이전트 컨트롤러는 *자동* 번역 IoT Edge의 응용 프로그램 모델을 pod, 배포, 서비스 등의 Kubernetes 기본 구문으로 수행 합니다.

개략적인 아키텍처 다이어그램은 다음과 같습니다.

![kubernetes 아치](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Edge 배포의 모든 구성 요소는 장치와 관련 된 Kubernetes 네임 스페이스로 범위가 지정 되므로 동일한 클러스터 리소스를 여러 edge 장치 및 해당 배포에서 공유할 수 있습니다.

>[!NOTE]
>Kubernetes의 IoT Edge는 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)상태입니다.

## <a name="tutorials-and-references"></a>자습서 및 참조 

심층 자습서 및 참조를 포함 한 자세한 내용은 [Kubernetes preview docs 미니 사이트의 IoT Edge](https://aka.ms/edgek8sdoc) 을 참조 하세요.
