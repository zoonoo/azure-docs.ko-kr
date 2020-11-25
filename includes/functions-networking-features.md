---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021000"
---


| 기능 |[사용 계획](../articles/azure-functions/functions-scale.md#consumption-plan)|[프리미엄 계획](../articles/azure-functions/functions-scale.md#premium-plan)|[전용 계획](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[인바운드 IP 제한 및 프라이빗 사이트 액세스](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅예|✅예|✅예|✅예|✅예|
|[가상 네트워크 통합](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌아니요|✅예(지역)|✅예(지역 및 게이트웨이)|✅예| ✅예|
|[가상 네트워크 트리거(비 HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌아니요| ✅예 |✅예|✅예|✅예|
|[하이브리드 연결](../articles/azure-functions/functions-networking-options.md#hybrid-connections)(Windows 전용)|❌아니요|✅예|✅예|✅예|✅예|
|[아웃바운드 IP 제한](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌아니요| ✅예|✅예|✅예|✅예|