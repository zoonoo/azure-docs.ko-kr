---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5accc38a8693fba5934b1182ebdafe8921540d98
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170211"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[\[제한된 미리 보기\]: \[AKS\] AKS에서 권한 있는 컨테이너를 허용하지 않음](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerNoPrivilege_EnforceRegoPolicy.json) |이 정책은 Azure Kubernetes Service 클러스터에서 권한 있는 컨테이너를 생성하는 것을 허용하지 않습니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
|[\[제한된 미리 보기\]: \[AKS\] AKS에서 HTTPS 수신 적용](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/IngressHttpsOnly_EnforceRegoPolicy.json) |이 정책은 Azure Kubernetes Service 클러스터에서 HTTPS 수신을 적용합니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
|[\[제한된 미리 보기\]: \[AKS\] AKS에서 내부 부하 분산 장치 적용](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/LoadbalancerNoPublicIPs_EnforceRegoPolicy.json) |이 정책은 부하 분산 장치가 Azure Kubernetes Service 클러스터의 공용 IP를 갖지 않도록 적용합니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
|[\[제한된 미리 보기\]: \[AKS\] AKS에서 Pod에 레이블 적용](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/PodEnforceLabels_EnforceRegoPolicy.json) |이 정책은 Azure Kubernetes Service 클러스터의 Pod에 대해 지정된 레이블을 적용합니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
|[\[제한된 미리 보기\]: \[AKS\] AKS의 네임스페이스 간에 고유한 수신 호스트 이름 적용](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/IngressHostnamesConflict_EnforceRegoPolicy.json) |이 정책은 Azure Kubernetes Service 클러스터의 네임스페이스에 고유한 수신 호스트 이름을 적용합니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
|[\[제한된 미리 보기\]: \[AKS\] AKS의 허용된 포트에서만 컨테이너 수신 대기 확인](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerAllowedPorts_EnforceRegoPolicy.json) |이 정책은 컨테이너가 Azure Kubernetes Service 클러스터의 허용된 포트만 수신하도록 합니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
|[\[제한된 미리 보기\]: \[AKS\] AKS의 컨테이너에 CPU 및 메모리 리소스 제한이 정의되어 있는지 확인](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerResourceLimits_EnforceRegoPolicy.json) |이 정책은 Azure Kubernetes Service 클러스터의 컨테이너에 CPU 및 메모리 리소스 제한이 정의되도록 합니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
|[\[제한된 미리 보기\]: \[AKS\] AKS에서 허용되는 컨테이너 이미지만 확인](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ContainerAllowedImages_EnforceRegoPolicy.json) |이 정책은 허용된 컨테이너 이미지만 Azure Kubernetes Service 클러스터에서 실행되도록 합니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
|[\[제한된 미리 보기\]: \[AKS\] AKS의 허용된 포트에서만 서비스 수신 대기 확인](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Kubernetes%20service/ServiceAllowedPorts_EnforceRegoPolicy.json) |이 정책은 서비스가 Azure Kubernetes Service 클러스터의 허용된 포트만 수신하도록 합니다. 제한된 미리 보기 정책은 등록된 구독에 대해서만 작동합니다. 등록하려면 https://aka.ms/akspolicyonboarding 으로 이동하세요. 이 정책 사용에 대한 지침은 https://aka.ms/akspolicydoc 로 이동하세요. |EnforceRegoPolicy, 사용 안 함 |1.0.0-미리 보기 |
