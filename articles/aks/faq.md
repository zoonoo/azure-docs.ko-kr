---
title: "Azure Container Service에 대한 질문과 대답"
description: "Azure Container Service에 대한 일반적인 질문에 대한 답변을 제공합니다."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 2b78479c257930669729a7781b3893b3e2064bab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>AKS(Azure Container Service)에 대한 질문과 대답

이 문서에서는 AKS(Azure Container Service)에 대한 질문과 대답을 제공합니다.

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>AKS(Azure Container Service)는 어떤 Azure 지역에 배치되나요? 

- 캐나다 중부 
- 캐나다 동부 
- 미국 중부 
- 미국 동부 
- 동남아시아 
- 서유럽 
- 미국 서부 2 

## <a name="when-will-additional-regions-be-added"></a>추가 지역은 언제 추가되나요? 

추가 지역은 수요가 증가하면 추가됩니다.

## <a name="are-security-updates-applied-to-aks-nodes"></a>보안 업데이트가 AKS 노드에 적용되나요? 

OS 보안 패치는 야간 일정에 따라 클러스터의 노드에 적용되지만 재부팅은 수행되지 않습니다. 필요한 경우 포털 또는 Azure CLI 노드를 통해 노드를 다시 부팅할 수 있습니다. 클러스터를 업그레이드할 때는 최신 Ubuntu 이미지가 사용되고 모든 보안 패치가 적용됩니다(재부팅 수행).

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>고객이 ACS 또는 AKS를 사용하도록 권장하나요? 

AKS(Azure Container Service)가 나중에 GA 대상이 된다는 가정 하에, AKS에 PoC, 개발자 및 테스트 클러스터를 구축하고, 프로덕션 클러스터는 ACS-Kubernetes에 구축하는 것이 좋습니다.  

## <a name="when-will-acs-be-deprecated"></a>ACS는 언제 지원이 종료될 예정인가요? 

ACS는 AKS가 GA가 될 때쯤에 지원이 종료될 예정입니다. 이 날로부터 12개월 후에는 클러스터를 AKS로 마이그레이션할 예정입니다. 이 12개월 기간 동안 모든 ACS 작업을 실행할 수 있습니다.

## <a name="does-aks-support-node-autoscaling"></a>AKS는 노드 자동 크기 조정 기능을 지원하나요? 

노드 자동 크기 조정은 지원되지 않지만 계획 중입니다. 이 오픈 소스 [자동 크기 조정 구현][auto-scaler]을 살펴볼 수 있습니다.

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS를 통해 2개의 리소스 그룹이 생성되는 이유는 무엇인가요? 

두 번째 리소스 그룹은 AKS 배포와 관련된 모든 리소스의 쉬운 삭제를 위해 자동으로 생성됩니다.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault는 AKS와 통합되나요? 

아니요. 통합되지 않지만 이 통합은 계획 중입니다. 당분간은 [Hexadite][hexadite]에서 다음 솔루션을 수행해볼 수 있습니다. 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  