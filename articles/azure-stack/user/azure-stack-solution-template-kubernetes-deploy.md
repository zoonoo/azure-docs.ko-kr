---
title: Azure Stack 컨테이너를 사용 하는 Kubernetes 배포 | Microsoft Docs
description: Kubernetes에서 컨테이너를 사용 하 여 Azure Stack을 사용 하 여 배포 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 77275ec274a9c76918874007cfe564eea09e6de5
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877196"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Azure Stack을 사용 하 여 컨테이너를 사용 하는 Kubernetes 배포

*적용 대상 Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

> [!Note]  
> Azure Stack에서 Kubernetes 미리 보기입니다. Azure Stack 연결이 끊긴된 시나리오는 현재 미리 보기에서 지원 되지 않습니다.

배포 및 조정 된 단일 작업에서 Kubernetes에 대 한 리소스를 설정 하려면이 문서의 단계를 수행할 수 있습니다. Azure Resource Manager 솔루션 템플릿을 사용 하는 단계입니다. 에서는 필요한 Azure Stack 설치에 대 한 필수 정보를 수집 하 템플릿을 생성을 클라우드에 배포 합니다. Azure Stack 템플릿 글로벌 Azure에서 제공 되는 동일한 관리 되는 AKS 서비스를 사용 하지 않습니다.

## <a name="kubernetes-and-containers"></a>Kubernetes 및 컨테이너

Azure Stack에서 ACS 엔진에서 생성 하는 Azure Resource Manager 템플릿을 사용 하 여 Kubernetes를 설치할 수 있습니다. [Kubernetes](https://kubernetes.io) 는 배포를 자동화 하기 위한 오픈 소스 시스템 크기 조정 및 컨테이너의 응용 프로그램 관리. A [컨테이너](https://www.docker.com/what-container) 는 이미지에에서 포함 되어 있습니다. 그러나 포함 코드, 특정 라이브러리 및 설정을 실행 하기 위해 런타임 코드와 같은 응용 프로그램을 실행 하는 데 필요한 리소스 컨테이너 등 컨테이너 이미지는 VM과 달리 VM에와 같습니다.

Kubernetes에서 사용할 수 있습니다.

- 초 후에 배포할 수 있는 확장성이 매우 뛰어난, 업그레이드, 응용 프로그램을 개발 합니다. 
- 응용 프로그램의 설계를 간소화 하 고 다른 Helm 응용 프로그램에서 안정성을 개선 합니다. [Helm](https://github.com/kubernetes/helm)은 Kubernetes 응용 프로그램을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다.
- 쉽게 모니터링 하 고 응용 프로그램의 상태를 진단 합니다.

만 노드에서 클러스터를 지 원하는 데 필요한 계산 사용량에 대해 부과 됩니다. 자세한 내용은 [사용 및 Azure Stack에서 청구](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback)합니다.

## <a name="deploy-kubernetes-to-use-containers"></a>컨테이너를 사용 하는 Kubernetes 배포

Azure Stack에서 Kubernetes 클러스터를 배포 하는 단계 id 관리 서비스에 따라 달라 집니다. Azure Stack의 설치에서 사용 하는 id 관리 솔루션을 확인 합니다. 사용자 id 관리 서비스를 확인 하려면 Azure Stack 관리자에 게 문의 합니다.

- **Azure AD(Azure Active Directory)**  
Azure AD를 사용 하는 경우 클러스터 설치에 대 한 지침을 참조 하세요 [Azure Active Directory (Azure AD)를 사용 하 여 Azure Stack에 Kubernetes 배포](azure-stack-solution-template-kubernetes-azuread.md)합니다.

- **Active Directory 페더레이션 서비스 (AD FS)**  
AD FS를 사용 하는 경우 클러스터 설치에 대 한 지침을 참조 하세요 [Active Directory Federated Services (AD FS)를 사용 하 여 Azure Stack에 Kubernetes 배포](azure-stack-solution-template-kubernetes-adfs.md)합니다.

## <a name="connect-to-your-cluster"></a>클러스터에 연결

이제 클러스터에 연결할 준비가 되었습니다. 마스터 클러스터 리소스 그룹에 있을 수 있고 이름이 `k8s-master-<sequence-of-numbers>`합니다. 마스터에 연결할 SSH 클라이언트를 사용 합니다. 마스터에서 사용할 수 있습니다 **kubectl**, 클러스터를 관리 하려면 Kubernetes 명령줄 클라이언트입니다. 자세한 내용은 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)합니다.

알게 될 것을 **Helm** 패키지 관리자를 설치 하 고 클러스터에 앱을 배포 하는 데 유용 합니다. 를 설치 하 고 클러스터에서 Helm 사용에 대 한 참조 [helm.sh](https://helm.sh/)합니다.

## <a name="next-steps"></a>다음 단계

[Kubernetes 대시보드를 사용 하도록 설정](azure-stack-solution-template-kubernetes-dashboard.md)

[Kubernetes를 추가할 Marketplace (Azure Stack 연산자)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure Active Directory (Azure AD)를 사용 하 여 Azure Stack에 Kubernetes 배포](azure-stack-solution-template-kubernetes-azuread.md)

[Active Directory Federated Services (AD FS)를 사용 하 여 Azure Stack에 Kubernetes 배포](azure-stack-solution-template-kubernetes-adfs.md)

[Azure의 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
