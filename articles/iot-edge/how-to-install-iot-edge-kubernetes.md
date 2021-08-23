---
title: Kubernetes에 IoT Edge를 설치하는 방법 | Microsoft Docs
description: 로컬 개발 클러스터 환경을 사용하여 Kubernetes에 IoT Edge를 설치하는 방법에 대해 알아봅니다.
author: kgremban
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: c2ca09f036bbd15e87d6e633ba470e56637bf272
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535472"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes에 IoT Edge를 설치하는 방법(미리 보기)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge를 사용하여 복원력이 높은 고가용성 인프라 계층으로 이를 사용하는 Kubernetes와 통합할 수 있습니다. 이 지원이 포괄적인 IoT Edge 솔루션에 적합한 경우는 다음과 같습니다.

![k8s 소개](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>통합이 적합한 멘탈 모델은 Linux 및 Windows 외에 IoT Edge 애플리케이션을 실행할 수 있는 다른 운영 환경으로 Kubernetes를 고려하는 경우입니다.

## <a name="architecture"></a>Architecture 
Kubernetes에서 IoT Edge는 에지 워크로드 배포에 대한 CRD(‘사용자 지정 리소스 정의’)를 제공합니다. IoT Edge 에이전트는 로컬 클러스터 상태를 사용하여 클라우드 관리에 필요한 상태를 조정하는 ‘CRD 컨트롤러’의 역할을 담당합니다.

모듈 수명은 모듈 가용성을 유지하고 배치를 선택하는 Kubernetes 스케줄러에서 관리합니다. IoT Edge는 이를 기반으로 실행되는 에지 애플리케이션 플랫폼을 관리하고, IoT Hub에 지정된 필요한 상태를 에지 클러스터의 상태를 사용하여 지속적으로 조정합니다. 애플리케이션 모델은 IoT Edge 모듈과 경로를 기반으로 하는 친숙한 모델입니다. IoT Edge 에이전트 컨트롤러는 Pod, 배포, 서비스 등의 Kubernetes 기본 구문에 맞게 ‘자동’ 번역 IoT Edge의 애플리케이션 모델을 수행합니다.

개략적인 아키텍처 다이어그램은 다음과 같습니다.

![kubernetes 아키텍처](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

에지 배포의 모든 구성 요소는 디바이스와 관련된 Kubernetes 네임스페이스로 범위가 지정되므로 동일한 클러스터 리소스를 여러 에지 디바이스 및 관련 배포에서 공유할 수 있습니다.

>[!NOTE]
>Kubernetes를 기반으로 하는 IoT Edge는 [퍼블릭 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다.

## <a name="tutorials-and-references"></a>자습서 및 참조 

자세한 내용은 심화된 내용을 다루는 자습서와 참조가 수록된 [Kubernetes 기반 IoT Edge 미리 보기 문서 미니 사이트](https://aka.ms/edgek8sdoc)를 참조하세요.
