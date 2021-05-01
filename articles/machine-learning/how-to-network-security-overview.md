---
title: 가상 네트워크 격리 및 보안 개요
titleSuffix: Azure Machine Learning
description: 격리된 Azure Virtual Network와 Azure Machine Learning을 함께 사용하여 작업 영역 리소스 및 컴퓨팅 환경을 보호합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/02/2021
ms.topic: how-to
ms.custom: devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: e6b8a4bbbe596ec06f7f9b445dbaa439e1207e46
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888713"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>가상 네트워크 격리 및 개인 정보 개요

이 문서에서는 VNet(가상 네트워크)을 사용하여 Azure Machine Learning에서 네트워크 통신을 보호하는 방법을 알아봅니다. 이 문서에서는 예제 시나리오를 사용하여 전체 가상 네트워크를 구성하는 방법을 보여 줍니다.

이 문서는 5부로 구성된 Azure Machine Learning 워크플로 보호 과정을 안내하는 시리즈의 1부입니다. 개념을 먼저 이해하려면 이 개요 문서를 읽어 보는 것이 좋습니다. 

이 시리즈의 다른 문서는 다음과 같습니다.

**1. VNet 개요** > [2. 작업 영역 보호](how-to-secure-workspace-vnet.md) > [3. 학습 환경 보호](how-to-secure-training-vnet.md) > [4. 추론 환경 보호](how-to-secure-inferencing-vnet.md) > [5. 스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 사용자가 다음 항목에 익숙하다고 가정합니다.
+ [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
+ [IP 네트워킹](../virtual-network/public-ip-addresses.md)
+ [Azure Private Link](how-to-configure-private-link.md)
+ [NSG(네트워크 보안 그룹)](../virtual-network/network-security-groups-overview.md)
+ [네트워크 방화벽](../firewall/overview.md)
## <a name="example-scenario"></a>예제 시나리오

이 섹션에서는 개인 IP 주소를 사용하여 Azure Machine Learning 통신을 보호하기 위해 일반적인 네트워크 시나리오를 설정하는 방법을 알아봅니다.

아래 표에는 서비스가 VNet을 사용하는 경우와 사용하지 않는 경우 Azure Machine Learning 네트워크의 여러 부분에 액세스하는 방법이 비교되어 있습니다.

| 시나리오 | 작업 영역 | 연결된 리소스 | 컴퓨팅 환경 학습 | 추론 컴퓨팅 환경 |
|-|-|-|-|-|-|
|**가상 네트워크 없음**| 공용 IP | 공용 IP | 공용 IP | 공용 IP |
|**가상 네트워크의 보안 리소스**| 개인 IP(프라이빗 엔드포인트) | 공용 IP(서비스 엔드포인트) <br> **또는** <br> 개인 IP(프라이빗 엔드포인트) | 프라이빗 IP | 프라이빗 IP  | 

* **작업 영역** - VNet에서 프라이빗 엔드포인트를 만들어 작업 영역의 개인 링크에 연결합니다. 프라이빗 엔드포인트는 여러 개의 개인 IP 주소를 통해 VNet에 작업 영역을 연결합니다.
* **연결된 리소스** - 서비스 엔드포인트 또는 프라이빗 엔드포인트를 사용하여 Azure storage, Azure Key Vault, Azure Container Services 등의 작업 영역 리소스에 연결합니다.
    * **서비스 엔드포인트** 는 Azure 서비스에 대한 가상 네트워크의 ID를 제공합니다. 가상 네트워크에서 서비스 엔드포인트를 사용하면 가상 네트워크 규칙을 추가하여 가상 네트워크에 대한 Azure 서비스 리소스를 보호할 수 있습니다. 서비스 엔드포인트는 공용 IP 주소를 사용합니다.
    * **프라이빗 엔드포인트** 는 Azure Private Link를 통해 제공되는 서비스에 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet에 효과적으로 가져옵니다.
* **학습 컴퓨팅 액세스** - Azure Machine Learning 컴퓨팅 인스턴스 및 Azure Machine Learning 컴퓨팅 클러스터와 같은 학습 컴퓨팅 대상에 개인 IP 주소를 사용하여 안전하게 액세스합니다. 
* **추론 컴퓨팅 액세스** - 개인 IP 주소를 사용하여 AKS(Azure Kubernetes Services) 컴퓨팅 클러스터에 액세스합니다.


다음 5개 섹션에서는 위에서 설명한 네트워크 시나리오를 보호하는 방법을 보여 줍니다. 네트워크를 보호하려면 다음을 수행해야 합니다.

1. [**작업 영역 및 연결된 리소스**](#secure-the-workspace-and-associated-resources)를 보호합니다.
1. [**학습 환경**](#secure-the-training-environment)을 보호합니다.
1. [ **추론 환경**](#secure-the-inferencing-environment)을 보호합니다.
1. 필요에 따라 [**스튜디오 기능을 사용**](#optional-enable-studio-functionality)합니다.
1. [**방화벽 설정**](#configure-firewall-settings)을 구성합니다.
1. [DNS 이름 확인](#custom-dns)을 구성합니다.
## <a name="secure-the-workspace-and-associated-resources"></a>작업 영역 및 연결된 리소스 보호

작업 영역 및 연결된 리소스를 보호하려면 다음 단계를 수행합니다. 이러한 단계를 통해 서비스가 가상 네트워크에서 통신할 수 있습니다.

1. VNet과 작업 영역 간에 통신을 사용하도록 설정하려면 [프라이빗 링크 사용 작업 영역](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)을 만듭니다.
1. __서비스 엔드포인트__ 또는 __프라이빗 엔드포인트__ 중 _하나_ 를 사용하여 다음 서비스를 가상 네트워크에 추가합니다. 또한 신뢰할 수 있는 Microsoft 서비스가 다음 서비스에 액세스할 수 있도록 허용해야 합니다.
    
    | 서비스 | 엔드포인트 정보 | 신뢰할 수 있는 정보 허용 |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [서비스 엔드포인트](../key-vault/general/overview-vnet-service-endpoints.md)</br>[프라이빗 엔드포인트](../key-vault/general/private-link-service.md) | [신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 우회하도록 허용](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Azure Storage 계정__ | [서비스 엔드포인트](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)</br>[프라이빗 엔드포인트](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints) | [신뢰할 수 있는 Azure 서비스에 대한 액세스 권한 부여](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure Container Registry__ | [서비스 엔드포인트](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)</br>[프라이빗 엔드포인트](../container-registry/container-registry-private-link.md) | [신뢰할 수 있는 서비스 허용](../container-registry/allow-access-trusted-services.md) |


![작업 영역 및 연결된 리소스가 VNet 내부의 서비스 엔드포인트 또는 프라이빗 엔드포인트를 통해 서로 통신하는 방법을 보여 주는 아키텍처 다이어그램](./media/how-to-network-security-overview/secure-workspace-resources.png)

이 단계를 완료하는 방법에 대한 자세한 지침은 [Azure Machine Learning 작업 영역 보호](how-to-secure-workspace-vnet.md)를 참조하세요. 

### <a name="limitations"></a>제한 사항

가상 네트워크 내에서 작업 영역 및 연결된 리소스를 보호하는 경우 다음과 같은 제한 사항이 있습니다.
- Azure Government 또는 Azure 중국 21Vianet 지역에서는 프라이빗 링크로 Azure Machine Learning 작업 영역을 사용할 수 없습니다.
- 모든 리소스는 동일한 VNet 뒤에 있어야 합니다. 그러나 동일한 VNet 내의 서브넷은 허용됩니다.

## <a name="secure-the-training-environment"></a>학습 환경 보호

이 섹션에서는 Azure Machine Learning에서 교육 환경을 보호하는 방법을 알아봅니다. 또한 네트워크 구성이 함께 작동하는 방식을 이해하기 위해 Azure Machine Learning이 학습 작업을 완료하는 방법을 알아봅니다.

학습 환경을 보호하려면 다음 단계를 수행합니다.

1. 학습 작업을 실행하기 위해 Azure Machine Learning [컴퓨팅 인스턴스 및 가상 네트워크의 컴퓨팅 클러스터](how-to-secure-training-vnet.md#compute-instance)를 만듭니다.
1. Batch Service가 컴퓨팅 리소스에 작업을 제출할 수 있도록 [Azure Batch 서비스에서 인바운드 통신을 허용](how-to-secure-training-vnet.md#mlcports)합니다. 

![관리형 컴퓨팅 클러스터 및 인스턴스를 보호하는 방법을 보여 주는 아키텍처 다이어그램](./media/how-to-network-security-overview/secure-training-environment.png)

이 단계를 완료하는 방법에 대한 자세한 지침은 [학습 환경 보호](how-to-secure-training-vnet.md)를 참조하세요. 

### <a name="example-training-job-submission"></a>학습 작업 제출 예 

이 섹션에서는 Azure Machine Learning이 교육 작업을 제출하기 위해 서비스 간에 안전하게 통신하는 방법을 알아봅니다. 통신을 보호하기 위해 모든 구성이 함께 작동하는 방법을 보여 줍니다.

1. 클라이언트는 서비스 또는 프라이빗 엔드포인트로 보호되는 스토리지 계정에 학습 스크립트 및 학습 데이터를 업로드합니다.

1. 클라이언트는 프라이빗 엔드포인트를 통해 Azure Machine Learning 작업 영역에 학습 작업을 제출합니다.

1. Azure Batch 서비스는 작업 영역에서 작업을 수신하고, 컴퓨팅 리소스로 프로비전된 공용 부하 분산 장치를 통해 학습 작업을 컴퓨팅 환경으로 전송합니다. 

1. 컴퓨팅 리소스는 작업을 수신하고 학습을 시작합니다. 컴퓨팅 리소스는 안전한 스토리지 계정에 액세스하여 학습 파일을 다운로드하고 출력을 업로드합니다.

### <a name="limitations"></a>제한 사항

- Azure 컴퓨팅 인스턴스 및 Azure 컴퓨팅 클러스터는 작업 영역 및 연결된 리소스와 동일한 VNet, 지역 및 구독에 있어야 합니다. 

## <a name="secure-the-inferencing-environment"></a>추론 환경 보안

이 섹션에서는 추론 환경을 보호하는 데 사용할 수 있는 옵션을 알아봅니다. 대규모 프로덕션 배포에는 AKS(Azure Kubernetes Services) 클러스터를 사용하는 것이 좋습니다.

가상 네트워크에서는 AKS 클러스터에 대한 두 가지 옵션을 사용할 수 있습니다.

- VNet에 기본 AKS 클러스터를 배포하거나 연결합니다.
- 프라이빗 AKS 클러스터를 VNet에 연결합니다.

**기본 AKS 클러스터** 에는 공용 IP 주소를 사용하는 컨트롤 플레인이 있습니다. 배포 중에 기본 AKS 클러스터를 VNet에 추가하거나 클러스터를 만든 후에 연결할 수 있습니다.

**프라이빗 AKS 클러스터** 에는 개인 IP를 통해서만 액세스할 수 있는 컨트롤 플레인이 있습니다. 클러스터를 만든 후에 프라이빗 AKS 클러스터를 연결해야 합니다.

기본 및 프라이빗 클러스터를 추가하는 방법에 대 한 자세한 지침은 [추론 환경 보호](how-to-secure-inferencing-vnet.md)를 참조하세요. 

다음 네트워크 다이어그램은 프라이빗 AKS 클러스터가 가상 네트워크에 연결된 보호되는 Azure Machine Learning 작업 영역을 보여 줍니다.

![프라이빗 AKS 클러스터를 가상 네트워크에 연결하는 방법을 보여 주는 아키텍처 다이어그램입니다. AKS 컨트롤 평면은 고객 VNet의 외부에 배치됩니다.](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>제한 사항
- AKS 클러스터는 작업 영역 및 연결된 리소스와 동일한 VNet에 속해야 합니다. 

## <a name="optional-enable-public-access"></a>선택 사항: 공용 액세스 사용

프라이빗 엔드포인트를 사용하여 VNet 뒤의 작업 영역을 보호하고 공용 인터넷을 통한 액세스를 계속 허용할 수 있습니다. 초기 구성은 [작업 영역 및 연결 리소스를 보호](#secure-the-workspace-and-associated-resources)하는 것과 같습니다. 

프라이빗 링크를 사용하여 작업 영역을 보호한 후에는 [공용 액세스를 사용 하도록 설정](how-to-configure-private-link.md#enable-public-access)합니다. 그러면 공용 인터넷과 VNet 모두에서 작업 영역에 액세스할 수 있습니다.

### <a name="limitations"></a>제한 사항

- 공용 인터넷을 통해 Azure Machine Learning 스튜디오를 사용하는 경우 디자이너와 같은 일부 기능에서 데이터에 액세스하지 못할 수 있습니다. 이 문제는 VNet 뒤에서 보호되는 서비스에 데이터를 저장하는 경우에 발생합니다. 예를 들어 Azure Storage 계정이 있습니다.
## <a name="optional-enable-studio-functionality"></a>선택 사항: 스튜디오 기능 사용

[작업 영역 보호](#secure-the-workspace-and-associated-resources) > [학습 환경 보호](#secure-the-training-environment) > [추론 환경 보호](#secure-the-inferencing-environment) > **스튜디오 기능 사용** > [방화벽 설정 구성](#configure-firewall-settings)

스토리지가 VNet에 있는 경우 먼저 추가 구성 단계를 수행하여 [스튜디오](overview-what-is-machine-learning-studio.md)에서 전체 기능을 사용하도록 설정해야 합니다. 기본적으로 다음 기능은 사용할 수 없습니다.

* 스튜디오에서 데이터 미리 보기
* 디자이너에서 데이터 시각화
* 디자이너에서 모델 배포
* AutoML 실험 제출
* 레이블 지정 프로젝트 시작

VNet 내부에서 전체 스튜디오 기능을 사용하려면 [가상 네트워크에서 Azure Machine Learning 스튜디오 사용](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio)을 참조하세요. 스튜디오는 서비스 엔드포인트나 프라이빗 엔드포인트를 사용하여 스토리지 계정을 지원합니다.

### <a name="limitations"></a>제한 사항

[ML 지원 데이터 레이블 지정](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling)은 가상 네트워크 뒤에 보호되는 기본 스토리지 계정을 지원하지 않습니다. ML 지원 데이터 레이블 지정에는 기본이 아닌 스토리지 계정을 사용해야 합니다. 기본이 아닌 스토리지 계정은 가상 네트워크 뒤에서 보호할 수 있습니다. 

## <a name="configure-firewall-settings"></a>방화벽 설정 구성하기

Azure Machine Learning 작업 영역 리소스 및 공용 인터넷에 대한 액세스를 제어하도록 방화벽을 구성합니다. Azure Firewall을 권장하지만 다른 방화벽 제품을 사용하여 네트워크를 보호할 수 있습니다. 방화벽을 통한 통신을 허용하는 방법에 대한 질문이 있는 경우 사용 중인 방화벽에 대한 문서를 참조하세요.

방화벽 설정에 대한 자세한 내용은 [방화벽 뒤에 작업 영역 사용](how-to-access-azureml-behind-firewall.md)을 참조하세요.

## <a name="custom-dns"></a>사용자 지정 DNS

가상 네트워크에 대한 사용자 지정 DNS 솔루션을 사용해야 하는 경우 작업 영역에 대한 호스트 레코드를 추가해야 합니다.

필요한 도메인 이름 및 IP 주소에 대한 자세한 내용은 [사용자 지정 DNS 서버에서 작업 영역을 사용하는 방법](how-to-custom-dns.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서는 5부로 구성된 가상 네트워크 시리즈의 1부입니다. 가상 네트워크를 보호하는 방법을 알아보려면 나머지 문서를 참조하세요.

* [2부: 가상 네트워크 개요](how-to-secure-workspace-vnet.md)
* [3부: 학습 환경 보호](how-to-secure-training-vnet.md)
* [4부: 추론 환경 보호](how-to-secure-inferencing-vnet.md)
* .[5부: 스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)

또한 이름 확인을 위한 [사용자 지정 DNS](how-to-custom-dns.md) 사용에 대한 문서를 참조하세요.