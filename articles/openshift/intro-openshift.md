---
title: Azure Red Hat OpenShift 소개
description: 컨테이너 기반 애플리케이션을 배포 및 관리하기 위한 Microsoft Azure Red Hat OpenShift의 기능 및 이점을 알아봅니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: a3bdc3673474b778aa7c1003e48e215bac6d05bf
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628523"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft *Azure Red Hat OpenShift* 서비스를 사용하면 완전 관리형 [OpenShift](https://www.openshift.com/) 클러스터를 배포할 수 있습니다.

Azure Red Hat OpenShift는 [Kubernetes](https://kubernetes.io/)를 확장합니다. Kubernetes를 사용하여 프로덕션에서 컨테이너를 실행하려면 추가 도구와 리소스가 필요합니다. 여기에는 이미지 레지스트리, 스토리지 관리, 네트워킹 솔루션, 로깅 및 모니터링 도구를 저글링해야 하는 경우가 포함됩니다. 이 모든 도구를 함께 버전화하고 테스트해야 합니다. 컨테이너 기반 애플리케이션을 빌드하려면 미들웨어, 프레임워크, 데이터베이스 및 CI/CD 도구를 사용하여 더 많은 통합 작업을 해야 합니다. Azure Red Hat OpenShift는 이 모든 항목을 단일 플랫폼에 결합하여 IT 팀이 운영하기 쉽도록 하는 동시에 애플리케이션 팀이 실행하는 데 필요한 사항을 제공합니다.

Azure Red Hat OpenShift는 Red Hat과 Microsoft가 공동으로 설계, 운영 및 지원하여 통합된 지원 경험을 제공합니다. 가상 머신을 작동할 필요가 없으며, 패치도 필요하지 않습니다. 사용자를 대신하여 Red Hat 및 Microsoft가 마스터, 인프라 및 애플리케이션 노드를 패치, 업데이트 및 모니터링합니다. Azure Red Hat OpenShift 클러스터는 사용자의 Azure 구독에 배포되고 사용자의 Azure 청구서에 포함됩니다.

자신의 고유한 레지스트리, 네트워킹, 스토리지 및 CI/CD 솔루션을 선택하거나, 기본 제공 솔루션을 사용하여 자동 소스 코드 관리, 컨테이너 및 애플리케이션 빌드, 배포, 크기 조정, 상태 관리 등을 수행할 수 있습니다. Azure Red Hat OpenShift는 Azure Active Directory를 통해 통합된 로그온 환경을 제공합니다.

시작하려면 [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서를 완료하세요.

## <a name="access-security-and-monitoring"></a>액세스, 보안 및 모니터링

향상된 보안 및 관리를 위해 Azure Red Hat OpenShift를 Azure AD(Azure Active Directory)와 연결할 수 있으며, Kubernetes RBAC(역할 기반 액세스 제어)를 사용할 수 있습니다. 클러스터 및 리소스의 상태를 모니터링할 수도 있습니다.

## <a name="cluster-and-node"></a>클러스터 및 노드

Azure Red Hat OpenShift 노드는 Azure 가상 머신에서 실행됩니다. 스토리지를 노드 및 Pod에 연결하고, 클러스터 구성 요소를 업그레이드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Red Hat OpenShift에서 다음을 위한 필수 구성 요소를 알아봅니다.

> [!div class="nextstepaction"]
> [개발 환경 설정](tutorial-create-cluster.md)
