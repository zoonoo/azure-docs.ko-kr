---
title: GPU 디바이스로 Azure Stack Edge Pro에서 IoT Edge 문제 해결 | Microsoft Docs
description: Azure Stack Edge Pro GPU 디바이스에서 IoT Edge 오류를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 405a9c62a551a011eb6d7b00025c6ae0a563e858
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987849"
---
# <a name="troubleshoot-iot-edge-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 IoT Edge 문제 해결 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 IoT Edge 에이전트에 대한 런타임 응답과 디바이스에 설치된 IoT Edge 서비스에 대한 오류를 검토하여 Azure Stack Edge Pro GPU 디바이스에서 컴퓨팅 관련 오류를 해결하는 방법을 설명합니다.

## <a name="review-iot-edge-runtime-responses"></a>IoT Edge 런타임 응답 검토

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="troubleshoot-iot-edge-service-errors"></a>IoT Edge 서비스 오류 문제 해결

다음 오류는 Azure Stack Edge Pro GPU 디바이스의 IoT Edge 서비스와 관련되어 있습니다.

[!INCLUDE [Troubleshoot IoT Edge errors](../../includes/azure-stack-edge-iot-troubleshoot-compute-error-detail.md)]


## <a name="next-steps"></a>다음 단계

- [IoT Edge와 관련된 Kubernetes 문제 디버깅](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).
- [디바이스 문제 해결](azure-stack-edge-gpu-troubleshoot.md).