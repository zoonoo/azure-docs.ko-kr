---
title: Kubernetes에 IoT 에지를 설치하는 방법 | 마이크로 소프트 문서
description: 로컬 개발 클러스터 환경을 사용하여 Kubernetes에 IoT Edge를 설치하는 방법에 대해 알아봅니다.
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471288"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes에 IoT 에지를 설치하는 방법 (미리보기)

IoT Edge는 Kubernetes와 통합하여 탄력적이고 가용성이 높은 인프라 계층으로 사용할 수 있습니다. 이 지원은 고급 IoT 에지 솔루션에 적합한 위치입니다.

![k8s 소개](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>이 통합을 위한 좋은 정신 모델은 Kubernetes를 Linux 및 Windows 이외에 IoT Edge 응용 프로그램이 실행할 수 있는 또 다른 운영 환경으로 생각하는 것입니다.

## <a name="architecture"></a>Architecture 
Kubernetes에서 IoT Edge는 에지 워크로드 배포를 위한 사용자 지정 리소스 정의(CRD)를 제공합니다. *Custom Resource Definition* IoT Edge Agent는 클라우드 관리 원하는 상태를 로컬 클러스터 상태와 조정하는 *CRD 컨트롤러의* 역할을 맡습니다.

모듈 수명은 모듈 가용성을 유지하고 배치를 선택하는 Kubernetes 스케줄러에 의해 관리됩니다. IoT Edge는 위에서 실행되는 에지 애플리케이션 플랫폼을 관리하여 IoT Hub에 지정된 원하는 상태를 에지 클러스터의 상태와 지속적으로 조정합니다. 애플리케이션 모델은 여전히 IoT Edge 모듈 및 경로를 기반으로 하는 친숙한 모델입니다. IoT Edge 에이전트 컨트롤러는 포드, 배포, 서비스 등과 같은 Kubernetes 네이티브 구문에 *IoT* Edge의 응용 프로그램 모델을 자동 변환합니다.

다음은 고급 아키텍처 다이어그램입니다.

![쿠베르네츠 아치](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

에지 배포의 모든 구성 요소는 장치와 관련된 Kubernetes 네임스페이스로 범위가 지정되므로 여러 에지 장치와 해당 배포 간에 동일한 클러스터 리소스를 공유할 수 있습니다.

>[!NOTE]
>Kubernetes의 IoT Edge는 [공개 미리 보기입니다.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>자습서 및 참조 

자세한 내용은 [Kubernetes 미리 보기 문서 미니 사이트에서 IoT Edge를](https://aka.ms/edgek8sdoc) 참조하세요.
