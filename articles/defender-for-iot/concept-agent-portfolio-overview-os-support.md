---
title: 에이전트 포트폴리오 개요 및 OS 지원(미리 보기)
description: Azure Defender for IoT는 디바이스 유형을 기반으로 하는 에이전트의 많은 포트폴리오를 제공합니다.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: d2e463051d0897afe52981ea2d50ddd1f06bb54d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383425"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>에이전트 포트폴리오 개요 및 OS 지원(미리 보기)

Azure Defender for IoT는 디바이스 유형을 기반으로 하는 에이전트의 많은 포트폴리오를 제공합니다. 

## <a name="standalone-agent"></a>독립 실행형 에이전트

독립 실행형 에이전트는 대부분의 Linux 운영 체제를 포함하며, 이를 이진 패키지로 배포하거나 펌웨어의 일부로 통합될 수 있는 소스 코드로 배포하고 고객의 요구에 따라 수정 및 사용자 지정할 수 있습니다. OS 지원의 예제는 다음과 같습니다. 

| 운영 체제 | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

자세한 내용은 운영 체제 지원을 참조하거나 디바이스 펌웨어의 일부로 통합할 수 있는 소스 코드에 대한 액세스를 요청하려면 계정 관리자에게 문의하거나 <defender_micro_agent@microsoft.com>에 이메일을 보낼 수 있습니다. 

## <a name="azure-rtos-micro-agent"></a>Azure RTOS 마이크로 에이전트

Azure Defender for IoT 마이크로 에이전트는 Azure RTOS를 사용하는 디바이스에 대한 포괄적이고 간단한 보안 솔루션을 제공합니다. Azure Defender for IoT 마이크로 에이전트는 일반적인 위협에 대한 범위 및 RTOS(실시간 운영 체제) 디바이스에서 잠재적 악성 작업을 제공합니다. 마이크로 에이전트는 Azure RTOS NetX 듀어 구성 요소의 일부로 기본 제공되며 디바이스의 네트워크 작업을 모니터링합니다. 

Azure Defender for IoT 마이크로 에이전트는 Azure RTOS NetX 듀어 구성 요소의 일부로 기본 제공되며 디바이스의 네트워크 작업을 모니터링합니다. 마이크로 에이전트는 일반 위협에 대한 범위 및 RTOS(실시간 운영 체제) 디바이스에서 잠재적 악성 작업을 제공하는 포괄적이고 간단한 보안 솔루션으로 구성됩니다.

## <a name="next-steps"></a>다음 단계

[독립 실행형 마이크로 에이전트 개요(미리 보기)](concept-standalone-micro-agent-overview.md)에 대해 자세히 알아보세요.