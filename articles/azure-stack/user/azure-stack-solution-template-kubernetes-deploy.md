---
title: Azure Stack에 Kubernetes 클러스터 배포 | Microsoft Docs
description: Azure Stack에 Kubernetes 클러스터를 배포 하는 방법에 알아봅니다.
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 1fb7716ff1b5ce661dff55c3a8dac90a062fad53
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630785"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Azure Stack에 Kubernetes 클러스터 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

> [!Note]  
> Azure Stack에서 Azure 컨테이너 서비스 (ACS) Kubernetes 비공개 미리 보기입니다. 귀하가 Azure Stack 운영자는이 문서의 지침을 수행 하는 데 필요한 Kubernetes Marketplace 항목에 대 한 액세스를 요청 해야 합니다.
<!-- Should "Azure Container Services" be replaced globally with Azure Kubernetes Services? It seems the Azure container page is now redirectin to Azure Kubernetes https://azure.microsoft.com/en-us/services/container-service -->

다음 문서를 배포 하 고 조정 된 단일 작업에서 Kubernetes에 대 한 리소스를 프로 비전 하는 Azure Resource Manager 솔루션 템플릿을 사용 하 여 살펴봅니다. 필요한 Azure Stack 설치에 대 한 필요한 정보를 수집 하려면 템플릿을 생성을을 클라우드에 배포 합니다.

## <a name="kubernetes-and-containers"></a>Kubernetes 및 컨테이너

Azure Stack에서 Azure 컨테이너 서비스 (ACS) 엔진에서 생성 하는 Azure Resource Manager 템플릿을 사용 하 여 Kubernetes를 설치할 수 있습니다. [Kubernetes](https://kubernetes.io) 는 배포를 자동화 하기 위한 오픈 소스 시스템 크기 조정 및 컨테이너의 응용 프로그램 관리. A [컨테이너](https://www.docker.com/what-container) VM에는 이미지에 포함 되어 있습니다. 컨테이너 이미지에는 VM과 달리 코드, 특정 라이브러리 및 설정을 실행 하기 위해 런타임 코드와 같은 응용 프로그램을 실행 하는 데 필요한 리소스만 포함 됩니다.

Kubernetes에서 사용할 수 있습니다.

- 초 후에 배포할 수 있는 확장성이 매우 뛰어난, 업그레이드, 응용 프로그램을 개발 합니다. 
- 응용 프로그램의 설계를 간소화 하 고 다른 Helm 응용 프로그램에서 안정성을 개선 합니다. [Helm](https://github.com/kubernetes/helm)은 Kubernetes 응용 프로그램을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다.
- 모니터링 및 확장을 사용 하 여 응용 프로그램의 상태를 진단 하 고 업그레이드 기능을 쉽게 합니다.

## <a name="prerequisites"></a>필수 조건 

시작 하려면 적절 한 권한이 있고 Azure Stack에 사용할 준비가 되어 있는지에 있는지를 확인 합니다.

1. Azure Active Directory (Azure AD) 테 넌 트에서 응용 프로그램을 만들 수 있는지 확인 합니다. Kubernetes 배포에 대 한 이러한 권한이 필요 합니다.

    에 대 한 지침은 권한을 확인 하십시오 [Azure Active Directory 확인 권한을](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)합니다.

1. SSH 공용 및 개인 키 쌍을 Azure Stack에서 Linux VM에 로그인을 생성 합니다. 클러스터를 만들 때 공개 키를 해야 합니다.

    키 생성에 대 한 지침을 참조 하세요 [SSH 키 생성](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)합니다.

1. Azure Stack 테 넌 트 포털에서 유효한 구독이 있는 새 응용 프로그램을 추가 하려면 사용할 수 있는 주소가 충분 한 공용 IP 있는지 확인 합니다.

    Azure Stack에 클러스터를 배포할 수 없습니다 **관리자** 구독 합니다. 사용자 * * 구독을 사용 해야 합니다. 

## <a name="create-a-service-principal-in-azure-ad"></a>Azure AD에서 서비스 주체 만들기

1. 전역에 로그인 [Azure portal](http://portal.azure.com)합니다.
1. Azure Stack 인스턴스와 연결 된 Azure AD 테 넌 트를 사용 하 여 서명 되었는지 확인 합니다.
1. Azure AD 응용 프로그램을 만듭니다.

    a. 선택 **Azure Active Directory** > **+ 앱 등록** > **새 응용 프로그램 등록**합니다.

    나. 입력을 **이름** 응용 프로그램입니다.

    다. 선택 **웹 앱 / API**합니다.

    d. 입력 `http://localhost` 에 대 한 합니다 **로그온 URL**합니다.

    다. **만들기**를 클릭합니다.

1. 기록해는 **응용 프로그램 ID**합니다. 클러스터를 만들 때 ID가 필요 합니다. ID로 참조 되 **서비스 주체 클라이언트 ID**합니다.

1. 선택 **설정을** > **키**합니다.

    a. 입력 된 **설명을**합니다.

    나. 선택 **무기한** 에 대 한 **Expires**합니다.

    다. **저장**을 선택합니다. 키 문자열을 기록 하십시오. 클러스터를 만들 때 키 문자열을 해야 합니다. 키로 참조 되는 **서비스 주체 클라이언트 비밀**합니다.



## <a name="give-the-service-principal-access"></a>서비스 주체 액세스를 제공 합니다.

보안 주체가 리소스를 만들 수 있도록 구독에 대 한 서비스 주체 액세스를 제공 합니다.

1.  에 로그인 합니다 [관리 포털](https://adminportal.local.azurestack.external)합니다.

1. 선택 **더 많은 서비스** > **사용자 구독** > **+ 추가**합니다.

1. 사용자가 만든 구독을 선택 합니다.

1. 선택 **액세스 제어 (IAM)** > 선택 **+ 추가**합니다.

1. 선택 된 **소유자** 역할입니다.

1. 주 서비스에 대해 만든 응용 프로그램 이름을 선택 합니다. 검색 상자에 이름을 입력 해야 합니다.

1. **저장**을 클릭합니다.

## <a name="deploy-a-kubernetes-cluster"></a>Kubernetes 클러스터 배포

1. 엽니다는 [Azure Stack 포털](https://portal.local.azurestack.external)합니다.

1. 선택 **+ 새로 만들기** > **계산** > **Kubernetes 클러스터**합니다. **만들기**를 클릭합니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

1. 선택 **기본 사항** 에 Kubernetes 클러스터를 만듭니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 입력 된 **Linux VM Admin Username**합니다. Kubernetes 클러스터의 일부인 Linux Virtual Machines 및 dvm이 대 한 사용자 이름입니다.

1. 입력 된 **SSH 공개 키** dvm이 고 Kubernetes 클러스터의 일부로 생성 하는 모든 Linux 컴퓨터에 대 한 인증에 사용 합니다.

1. 입력 된 **마스터 프로필 DNS 접두사** 지역에 고유한 합니다. 같은 지역 고유 이름 이어야 합니다 `k8s-12345`합니다. 하려고 선택 리소스 그룹과 동일한 이름을 모범 사례로 좋습니다.

    > [!Note]  
    > 각 클러스터에 대 한 마스터 프로필 새롭고 고유한 DNS 접두사를 사용 합니다.

1. 입력 된 **에이전트 풀 프로필 수**입니다. 수는 클러스터의 에이전트 수를 포함합니다. 있을 수 있습니다 1에서 4.

1. 입력 된 **서비스 주체 ClientId** Kubernetes Azure 클라우드 공급자가 사용 됩니다.

1. 입력 된 **서비스 주체 클라이언트 비밀** 서비스 주체 응용 프로그램을 만들 때 만든 합니다.

1. 입력 된 **Kubernetes Azure Cloud Provider 버전이**합니다. Kubernetes Azure 공급자에 대 한 버전입니다. Azure Stack에는 각 Azure Stack 버전에 대 한 사용자 지정 Kubernetes 빌드를 해제합니다.

1. 선택 하면 **구독** id입니다.

1. 새 리소스 그룹의 이름을 입력 하거나 기존 리소스 그룹을 선택 합니다. 리소스 이름은 영숫자 및 소문자 있어야 합니다.

1. 선택 합니다 **위치** 리소스 그룹입니다. Azure Stack 설치에 대해 선택 하는 지역입니다.

### <a name="specify-the-azure-stack-settings"></a>Azure Stack 설정 지정

1. 선택 된 **Azure Stack 스탬프 설정**합니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. 입력 된 **Arm 끝점을 테 넌 트**합니다. Kubernetes 클러스터에 대 한 리소스 그룹을 만들려면 연결 하는 Azure Resource Manager 끝점입니다. 통합 시스템에 Azure Stack 연산자에서 끝점을 가져올 해야 합니다. 에 Azure Stack 개발 키트 ASDK ()를 사용할 수 `https://management.local.azurestack.external`입니다.

1. 입력 된 **테 넌 트 ID** 테 넌 트에 대 한 합니다. 이 값을 찾는 데 도움을 참조 하세요 [테 넌 트 ID 가져오기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)합니다. 

## <a name="connect-to-your-cluster"></a>클러스터에 연결

이제 클러스터에 연결할 준비가 되었습니다. 마스터 클러스터 리소스 그룹에 있을 수 있고 이름이 `k8s-master-<sequence-of-numbers>`합니다. 마스터에 연결할 SSH 클라이언트를 사용 합니다. 마스터에서 사용할 수 있습니다 **kubectl**, 클러스터를 관리 하려면 Kubernetes 명령줄 클라이언트입니다. 자세한 내용은 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)합니다.

알게 될 것을 **Helm** 패키지 관리자를 설치 하 고 클러스터에 앱을 배포 하는 데 유용 합니다. 를 설치 하 고 클러스터에서 Helm 사용에 대 한 참조 [helm.sh](https://helm.sh/)합니다.

## <a name="next-steps"></a>다음 단계

[(Azure Stack 운영자)에 대 한 Marketplace에 Kubernetes 클러스터를 추가](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure의 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
