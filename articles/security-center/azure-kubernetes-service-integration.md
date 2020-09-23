---
title: Azure Security Center 및 Azure Kubernetes 서비스
description: Azure Kubernetes Services와 Azure Security Center의 통합에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f1444ea31fe693f1c912a3ecb785bbb1e3aa6333
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977277"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Security Center와 Azure Kubernetes Services 통합

AKS (Azure Kubernetes Service)는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 서비스입니다. 

**Kubernetes 용 Azure Defender** 를 사용 하도록 설정 하 여 AKS 노드, 클라우드 트래픽 및 보안 제어에 대해 깊이 있게 파악할 수 있습니다.

Security Center 및 AKS는 모두 최상의 클라우드 기본 Kubernetes 보안 제품을 형성 합니다.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Security Center Kubernetes 보호의 구성 요소는 무엇 인가요?

Kubernetes에 대 한 Security Center 보호는 두 가지 요소를 조합 하 여 제공 됩니다.

- **가상 컴퓨터에 대 한 Azure Security Center의 위협 방지** -Security Center 다른 vm에서 사용 하는 것과 동일한 Log Analytics 에이전트 Security Center를 사용 하 여 AKS 노드에서 발생 하는 보안 문제를 보여줄 수 있습니다. 또한 에이전트는 컨테이너 관련 분석을 모니터링 합니다.

- **Azure Security Center의 선택적 Azure Defender For Kubernetes 계획** -Kubernetes 요금제는 AKS 서비스를 통해 Kubernetes 하위 시스템에서 로그 및 정보를 수신 합니다. 이러한 로그는 AKS 서비스를 통해 Azure에서 이미 사용할 수 있습니다. Kubernetes에 대해 Azure Defender를 사용 하도록 설정 하는 경우 로그에 대 한 Security Center 액세스 권한을 부여 합니다. 따라서 Security Center AKS 마스터 노드에서 이미 수집 된 데이터를 사용 하 여 AKS 클러스터에 보안 이점을 제공 합니다. Kubernetes 환경에서 Azure Security Center 하 여 검색 된 데이터 중 일부에는 중요 한 정보가 포함 될 수 있습니다.


## <a name="next-steps"></a>다음 단계

Security Center의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조 하세요.

* [Azure Security Center 및 컨테이너 보안](container-security.md)

* [Azure Container Registry와 통합](azure-container-registry-integration.md)

* [Microsoft의 데이터 관리](https://www.microsoft.com/trust-center/privacy/data-management) -microsoft 서비스의 데이터 정책 (Azure, Intune 및 Microsoft 365 포함), microsoft 데이터 관리의 세부 정보 및 데이터에 영향을 주는 보존 정책에 대해 설명 합니다.
