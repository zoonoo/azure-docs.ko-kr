---
title: 독립 실행형 마이크로 에이전트 개요 (미리 보기)
titleSuffix: Azure Defender for IoT
description: IoT 용 Azure Defender 보안 에이전트를 사용 하 여 새 IoT 장치 및 Azure IoT 프로젝트에 직접 보안을 빌드할 수 있습니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/19/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 84afa4205b57481efc3ed9df805cfa7a3657a6c8
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809881"
---
# <a name="standalone-micro-agent-overview-preview"></a>독립 실행형 마이크로 에이전트 개요 (미리 보기)

보안은 IoT 구현자의 거의 보편적인 문제입니다. IoT 장치에는 끝점 모니터링, 보안 상태 관리 및 위협 검색에 대 한 고유한 요구 사항이 있으며,이는 매우 구체적인 성능 요구 사항이 있습니다. 

IoT 용 Azure Defender 보안 에이전트를 사용 하 여 새 IoT 장치 및 Azure IoT 프로젝트에 직접 보안을 빌드할 수 있습니다. 마이크로 에이전트는 이진 패키지로 배포 하거나 소스 코드를 수정 하는 기능을 비롯 한 유연한 배포 옵션을 제공 합니다. 그리고 마이크로 에이전트는 Linux 및 Azure RTOS와 같은 표준 IoT 운영 체제에 사용할 수 있습니다. 

IoT 마이크로 에이전트에 대 한 Azure Defender는 보안 상태 관리, 위협 검색 및 통합 보안 관리를 위한 Microsoft의 다른 보안 도구에 대 한 통합을 끝점으로 표시 합니다. 

## <a name="security-posture-management"></a>보안 상태 관리 

IoT 장치의 보안 상태를 사전에 모니터링 합니다. IoT 용 Azure Defender는 장치 관련 권장 사항과 함께 CIS 벤치 마크를 기반으로 하는 보안 상태 권장 사항을 제공 합니다. OS 구성, 방화벽 구성 및 사용 권한을 포함 하 여 운영 체제 보안에 대 한 가시성을 확보 합니다. 

## <a name="endpoint-iot-and-ot-threat-detection"></a>끝점 IoT 및 OT 위협 감지 

봇 넷, 무차별 암호 대입 시도, crypto 마이너 및 의심 스러운 네트워크 활동 등의 위협을 검색 합니다. 고유한 조직에서 가장 중요 한 위협을 대상으로 지정 하는 사용자 지정 경고를 만듭니다. 

## <a name="flexible-distribution-and-deployment-models"></a>유연한 배포 및 배포 모델 

IoT 마이크로 에이전트에 대 한 Azure Defender는 소스 코드를 포함 하 여 마이크로 에이전트를 펌웨어에 통합 하거나 필요한 항목만 포함 하도록 사용자 지정할 수 있습니다. 마이크로 에이전트는 이진 패키지로도 사용 하거나 다른 Azure IoT 솔루션에 직접 통합할 수 있습니다. 

## <a name="meets-the-needs-of-your-iot-devices-with-minimal-impact"></a>IoT 장치 요구 사항을 충족 하 고 최소한의 영향을 줍니다. 

IoT 마이크로 에이전트의 Azure Defender는 쉽게 배포할 수 있으며 끝점에 대 한 성능 영향은 최소화 합니다. IoT 마이크로 에이전트로 Defender를 사용 하 여 다음을 수행할 수 있습니다.

- **성능 최적화**: IoT 마이크로 에이전트의 Azure Defender는 작은 공간 및 낮은 CPU 사용량을 포함 합니다.  

- **플러그 앤 플레이**: 특정 OS 커널 종속성이 없거나 모든 주요 IoT 운영 체제에 대 한 지원이 필요 합니다. IoT 마이크로 에이전트의 Azure Defender는 자신의 장치를 충족 합니다. 

- **유연한 배포**: 독립 실행형 에이전트로 서, IoT의 마이크로 에이전트에 대 한 Azure Defender는 다양 한 배포 모델 및 유연한 배포를 지원 합니다.

## <a name="next-steps"></a>다음 단계

[마이크로 에이전트 인증 방법 (미리 보기)](concept-security-agent-authentication.md)을 확인 합니다.
