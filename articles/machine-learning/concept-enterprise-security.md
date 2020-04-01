---
title: 엔터프라이즈 보안
titleSuffix: Azure Machine Learning
description: 인증, 권한 부여, 네트워크 보안, 데이터 암호화 및 모니터링과 같은 Azure 기계 학습을 안전하게 사용합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 359fd7fc787db5710deca75dd562215d25ed9148
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437482"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure 기계 학습을 위한 엔터프라이즈 보안

이 문서에서는 Azure 기계 학습에 사용할 수 있는 보안 기능에 대해 알아봅니다.

클라우드 서비스를 사용하는 경우 필요한 사용자만 액세스를 제한하는 것이 가장 좋습니다. 먼저 서비스에서 사용하는 인증 및 권한 부여 모델을 이해해야 합니다. 또한 클라우드를 사용하여 온-프레미스 네트워크의 네트워크 액세스를 제한하거나 리소스를 안전하게 조인할 수도 있습니다. 데이터 암호화는 미사용 및 서비스 간에 데이터가 이동하는 동안 모두 중요합니다. 마지막으로 서비스를 모니터링하고 모든 활동에 대한 감사 로그를 생성할 수 있어야 합니다.

> [!NOTE]
> 이 문서의 정보는 Azure 기계 학습 파이썬 SDK 버전 1.0.83.1 이상에서 작동합니다.

## <a name="authentication"></a>인증

Azure Active Directory(Azure AD)를 사용하도록 구성된 경우 다단계 인증이 지원됩니다. 인증 프로세스는 다음과 같습니다.

1. 클라이언트는 Azure AD에 서명하고 Azure 리소스 관리자 토큰을 가져옵니다.  사용자와 서비스 주체는 완전히 지원됩니다.
1. 클라이언트는 Azure 리소스 관리자 및 모든 Azure 기계 학습에 토큰을 제공합니다.
1. 기계 학습 서비스는 사용자 계산 대상(예: 기계 학습 계산)에 기계 학습 서비스 토큰을 제공합니다. 이 토큰은 실행이 완료된 후 사용자 계산 대상에서 기계 학습 서비스로 다시 호출하는 데 사용됩니다. 범위는 작업 영역으로 제한됩니다.

[![Azure 기계 학습의 인증](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

자세한 내용은 [Azure 기계 학습 리소스 및 워크플로에 대한 인증 설정을](how-to-setup-authentication.md)참조하십시오. 이 문서에서는 서비스 주체 및 자동화된 워크플로를 사용하는 것을 포함하여 인증에 대한 정보와 예제를 제공합니다.

### <a name="authentication-for-web-service-deployment"></a>웹 서비스 배포에 대한 인증

Azure Machine Learning은 웹 서비스에 대한 두 가지 형태의 인증( 키 및 토큰)을 지원합니다. 각 웹 서비스는 한 번에 하나의 인증 형식만 사용할 수 있습니다.

|인증 방법|Description|Azure Container Instances|AKS|
|---|---|---|---|
|Key|키는 정적이며 새로 고칠 필요가 없습니다. 키는 수동으로 재생할 수 있습니다.|기본적으로 사용할 수 없게 설정되어 있습니다.| 기본적으로 사용하도록 설정됨|
|토큰|토큰은 지정된 기간 이후에 만료되며 새로 고쳐야 합니다.| 사용할 수 없음| 기본적으로 사용할 수 없게 설정되어 있습니다. |

코드 예제의 경우 [웹 서비스 인증 섹션을](how-to-setup-authentication.md#web-service-authentication)참조하십시오.

## <a name="authorization"></a>권한 부여

여러 작업 영역을 만들 수 있고 각 작업 영역을 여러 사용자와 공유할 수 있습니다. 작업 영역을 공유할 때 다음 역할을 사용자에게 할당하여 작업 영역에 대한 액세스를 제어할 수 있습니다.

* 소유자
* 참가자
* 판독기

다음 표에는 몇 가지 주요 Azure 기계 학습 작업과 이를 수행할 수 있는 역할이 나열되어 있습니다.

| Azure 기계 학습 작업 | 소유자 | 참가자 | 판독기 |
| ---- |:----:|:----:|:----:|
| 작업 영역 만들기 | ✓ | ✓ | |
| 작업 영역 공유 | ✓ | |  |
| 작업 영역을 엔터프라이즈 버전으로 업그레이드 | ✓ | |
| 계산 대상 만들기 | ✓ | ✓ | |
| 계산 대상 첨부 | ✓ | ✓ | |
| 데이터 저장소 연결 | ✓ | ✓ | |
| 실험 실행 | ✓ | ✓ | |
| 실행/메트릭 보기 | ✓ | ✓ | ✓ |
| 모델 등록 | ✓ | ✓ | |
| 이미지 만들기 | ✓ | ✓ | |
| 웹 서비스 배포 | ✓ | ✓ | |
| 모델/이미지 보기 | ✓ | ✓ | ✓ |
| 전화 웹 서비스 | ✓ | ✓ | ✓ |

기본 제공 역할이 요구 사항을 충족하지 않는 경우 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할은 작업 영역 및 기계 학습 계산의 작업에 대해서만 지원됩니다. 사용자 지정 역할은 작업 영역 및 해당 작업 영역의 계산 리소스에 대한 사용 권한을 읽거나 쓰거나 삭제할 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용할 수 있도록 할 수 있습니다. 자세한 내용은 [Azure 기계 학습 작업 영역에서 사용자 및 역할 관리](how-to-assign-roles.md)를 참조하세요.

> [!WARNING]
> Azure 기계 학습은 현재 Azure Active Directory 비즈니스 간 공동 작업에서 지원되지 않습니다.

### <a name="securing-compute-targets-and-data"></a>계산 대상 및 데이터 보안

소유자와 참여자는 작업 영역에 연결된 모든 계산 대상 및 데이터 저장소를 사용할 수 있습니다.  

또한 각 작업 영역에는 작업 영역과 이름이 같은 연결된 시스템 할당관리 ID도 있습니다. 관리되는 ID에는 작업 영역에 사용되는 연결된 리소스에 대한 다음과 같은 사용 권한이 있습니다.

관리되는 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리ID를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)참조하십시오.

| 리소스 | 사용 권한 |
| ----- | ----- |
| 작업 영역 | 참가자 |
| 스토리지 계정 | Storage Blob 데이터 기여자 |
| 주요 자격 증명 모음 | 모든 키, 비밀, 인증서에 대한 액세스 |
| Azure Container Registry | 참가자 |
| 작업 영역을 포함하는 리소스 그룹 | 참가자 |
| 키 자격 증명 모음이 포함된 리소스 그룹(작업 영역이 포함된 것과 다른 경우) | 참가자 |

관리자는 이전 표에 언급된 리소스에 대한 관리되는 ID의 액세스를 취소하지 않는 것이 좋습니다. 재동기화 키 작업을 사용하여 액세스를 복원할 수 있습니다.

Azure Machine Learning은 모든 작업 영역 `aml-` `Microsoft-AzureML-Support-App-`영역에 대한 구독에서 기여자 수준 액세스로 추가 응용 프로그램(이름은 시작 또는 시작)을 만듭니다. 예를 들어 미국 동부에 하나의 작업 영역과 동일한 구독에 북유럽에 하나의 작업 영역이 있는 경우 이러한 응용 프로그램 중 두 개가 표시됩니다. 이러한 응용 프로그램을 사용하면 Azure 기계 학습을 사용하여 계산 리소스를 관리할 수 있습니다.

## <a name="network-security"></a>네트워크 보안

Azure 기계 학습은 계산 리소스에 대한 다른 Azure 서비스에 의존합니다. 컴퓨팅 리소스(컴퓨팅 대상)는 모델을 학습 및 배포하는 데 사용합니다. 가상 네트워크에서 이러한 계산 대상을 만들 수 있습니다. 예를 들어 Azure 데이터 과학 가상 컴퓨터를 사용하여 모델을 학습한 다음 모델을 AKS에 배포할 수 있습니다.  

자세한 내용은 [가상 네트워크에서 실험 및 추론을 실행하는 방법을](how-to-enable-virtual-network.md)참조하세요.

작업 영역에 대해 Azure 개인 링크를 사용하도록 설정할 수도 있습니다. 개인 링크를 사용하면 Azure 가상 네트워크에서 작업 공간으로의 통신을 제한할 수 있습니다. 자세한 내용은 [개인 링크를 구성하는 방법을](how-to-configure-private-link.md)참조하십시오.

> [!TIP]
> 가상 네트워크와 개인 링크를 함께 결합하여 작업 영역과 다른 Azure 리소스 간의 통신을 보호할 수 있습니다. 그러나 일부 조합에는 엔터프라이즈 에디션 작업 영역이 필요합니다. 다음 표를 사용하여 엔터프라이즈 버전에 필요한 시나리오를 이해합니다.
>
> | 시나리오 | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | 가상 네트워크 또는 비공개 링크가 없습니다. | ✔ | ✔ |
> | 개인 링크가 없는 작업 영역입니다. 가상 네트워크의 기타 리소스(Azure 컨테이너 레지스트리 제외) | ✔ | ✔ |
> | 개인 링크가 없는 작업 영역입니다. 비공개 링크가 있는 기타 리소스 | ✔ | |
> | 전용 링크가 있는 작업 영역입니다. 가상 네트워크의 기타 리소스(Azure 컨테이너 레지스트리 제외) | ✔ | ✔ |
> | 개인 링크가 있는 작업 영역 및 기타 리소스 | ✔ | |
> | 전용 링크가 있는 작업 영역입니다. 비공개 링크 또는 가상 네트워크가 없는 기타 리소스 | ✔ | ✔ |
> | 가상 네트워크의 Azure 컨테이너 레지스트리 | ✔ | |
> | 작업 영역에 대한 고객 관리 키 | ✔ | |
> 

> [!WARNING]
> Azure 기계 학습 계산 인스턴스 미리 보기는 개인 링크가 활성화된 작업 영역에서 지원되지 않습니다.
> 
> Azure 기계 학습은 개인 링크가 활성화된 Azure Kubernetes 서비스를 사용하지 않습니다. 대신 가상 네트워크에서 Azure Kubernetes 서비스를 사용할 수 있습니다. 자세한 내용은 [Azure 가상 네트워크 내의 Secure Azure ML 실험 및 추론 작업을](how-to-enable-virtual-network.md)참조하십시오.

## <a name="data-encryption"></a>데이터 암호화.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

> [!IMPORTANT]
> 작업 영역에 중요한 데이터가 포함된 경우 작업 영역을 만드는 동안 [hbi_workspace 플래그를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 설정하는 것이 좋습니다. 이렇게 하면 Microsoft가 진단 목적으로 수집하는 데이터의 양을 제어하고 Microsoft 관리 환경에서 추가 암호화를 사용할 수 있습니다.

Azure에서 미사용 암호화가 작동하는 방식에 대한 자세한 내용은 [미사용 Azure 데이터 암호화를](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)참조하십시오.

#### <a name="azure-blob-storage"></a>Azure Blob 스토리지

Azure Machine Learning은 Azure 기계 학습 작업 영역 및 구독에 연결된 Azure Blob 저장소 계정에 스냅숏, 출력 및 로그를 저장합니다. Azure Blob 저장소에 저장된 모든 데이터는 Microsoft에서 관리하는 키로 미사용 으로 암호화됩니다.

Azure Blob 저장소에 저장된 데이터에 대해 사용자 고유의 키를 사용하는 방법에 대한 자세한 내용은 [Azure Key Vault의 고객 관리 키가 있는 Azure 저장소 암호화를](../storage/common/storage-encryption-keys-portal.md)참조하십시오.

학습 데이터는 일반적으로 Azure Blob 저장소에도 저장되므로 학습 계산 대상에 액세스할 수 있습니다. 이 저장소는 Azure 기계 학습에서 관리되지 않고 대상을 원격 파일 시스템으로 계산하기 위해 탑재됩니다.

키를 __회전하거나 취소해야__ 하는 경우 언제든지 취소할 수 있습니다. 키를 회전할 때 저장소 계정은 새 키(최신 버전)를 사용하여 미사용 데이터를 암호화하기 시작합니다. 키를 취소(비활성화)할 때 저장소 계정은 실패한 요청을 처리합니다. 일반적으로 회전 또는 해지가 효과적이려면 1시간이 걸립니다.

액세스 키 재생에 대한 자세한 내용은 [저장소 액세스 키 재생을](how-to-change-storage-access-key.md)참조하십시오.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure 기계 학습은 Azure Cosmos DB 인스턴스에 메트릭 및 메타데이터를 저장합니다. 이 인스턴스는 Azure 기계 학습에서 관리하는 Microsoft 구독과 연결됩니다. Azure Cosmos DB에 저장된 모든 데이터는 Microsoft에서 관리하는 키로 미사용 으로 암호화됩니다.

사용자 고유의(고객 관리) 키를 사용하여 Azure Cosmos DB 인스턴스를 암호화하려면 작업 영역에서 사용할 전용 Cosmos DB 인스턴스를 만들 수 있습니다. Microsoft 구독에서 호스팅되는 다중 테넌트 Cosmos DB 인스턴스 외부에 실행 기록 정보와 같은 데이터를 저장하려는 경우 이 방법을 권장합니다. 

고객 관리 키를 사용하여 구독에서 Cosmos DB 인스턴스를 프로비전하려면 다음 작업을 수행합니다.

* Cosmos DB의 고객 관리 키 기능을 지원합니다. 이 때 이 기능을 사용하려면 액세스를 요청해야 합니다. 이렇게 하려면 다음 [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)연락처로 문의하십시오.

* 아직 완료되지 않은 경우 구독에 Azure 기계 학습 및 Azure Cosmos DB 리소스 공급자를 등록합니다.

* 구독에 대한 기여자 권한으로 기계 학습 앱(ID 및 액세스 관리)에 권한을 부여합니다.

    ![포털의 ID 및 액세스 관리에서 'Azure 기계 학습 앱'에 대한 권한 부여](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Azure 기계 학습 작업 영역을 만들 때 다음 매개 변수를 사용합니다. 두 매개 변수는 모두 필수이며 SDK, CLI, REST API 및 리소스 관리자 템플릿에서 지원됩니다.

    * `resource_cmk_uri`: 이 매개 변수는 키 자격 증명 모음에서 고객 관리 키의 전체 리소스 URI(키에 [대한 버전 정보 포함)입니다.](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning) 

    * `cmk_keyvault`: 이 매개 변수는 구독의 키 자격 증명 모음의 리소스 ID입니다. 이 키 자격 증명 모음은 Azure 기계 학습 작업 영역에 사용할 동일한 지역 및 구독에 있어야 합니다. 
    
        > [!NOTE]
        > 이 키 자격 증명 모음 인스턴스는 작업 영역을 프로비전할 때 Azure 기계 학습에서 만든 키 자격 증명 모음과 다를 수 있습니다. 작업 영역에 동일한 키 볼트 인스턴스를 사용하려는 경우 [key_vault 매개 변수를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)사용하여 작업 공간을 프로비저닝하는 동안 동일한 키 자격 증명 모음을 전달합니다. 

이 Cosmos DB 인스턴스는 구독에서 Microsoft 관리 리소스 그룹에서 만들어집니다. 

> [!IMPORTANT]
> * 이 Cosmos DB 인스턴스를 삭제해야 하는 경우 이 인스턴스를 사용하는 Azure 기계 학습 작업 영역을 삭제해야 합니다. 
> * 이 코스모스 DB 계정의 기본 [__요청 단위는__](../cosmos-db/request-units.md) __8000으로__설정됩니다. 이 값을 변경하는 것은 지원되지 않습니다. 

키를 __회전하거나 취소해야__ 하는 경우 언제든지 취소할 수 있습니다. 키를 회전할 때 Cosmos DB는 새 키(최신 버전)를 사용하여 미사용 데이터를 암호화하기 시작합니다. 키를 해지(비활성화)할 때 Cosmos DB는 실패한 요청을 처리합니다. 일반적으로 회전 또는 해지가 효과적이려면 1시간이 걸립니다.

Cosmos DB를 사용하면 고객 관리 키에 대한 자세한 내용은 [Azure Cosmos DB 계정에 대한 고객 관리 키 구성을](../cosmos-db/how-to-setup-cmk.md)참조하십시오.

#### <a name="azure-container-registry"></a>Azure Container Registry

레지스트리(Azure 컨테이너 레지스트리)의 모든 컨테이너 이미지는 미사용 암호화됩니다. Azure는 이미지를 저장하기 전에 자동으로 암호화하고 Azure Machine Learning에서 이미지를 가져올 때 이미지를 해독합니다.

사용자 고유의 (고객 관리) 키를 사용하여 Azure 컨테이너 레지스트리를 암호화하려면 사용자 고유의 ACR을 만들고 작업 영역을 프로비전하는 동안 연결하거나 작업 영역 프로비저닝 시 생성되는 기본 인스턴스를 암호화해야 합니다.

기존 Azure 컨테이너 레지스트리를 사용하여 작업 영역을 만드는 예제는 다음 문서를 참조하십시오.

* [Azure CLI를 사용하여 Azure 기계 학습을 위한 작업 영역을 만듭니다.](how-to-manage-workspace-cli.md)
* [Azure 리소스 관리자 템플릿을 사용하여 Azure 기계 학습을 위한 작업 영역 을 만듭니다.](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

고객 관리 키를 사용하여 배포된 Azure 컨테이너 인스턴스(ACI) 리소스를 암호화할 수 있습니다. ACI에 사용되는 고객 관리 키는 작업 공간의 Azure 키 자격 증명 모음에 저장할 수 있습니다. 키 생성에 대한 자세한 내용은 [고객 관리 키를 사용하여 데이터 암호화를](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)참조하십시오.

Azure 컨테이너 인스턴스에 모델을 배포할 때 키를 사용하려면 `AciWebservice.deploy_configuration()`을 사용하여 새 배포 구성을 만듭니다. 다음 매개 변수를 사용하여 키 정보를 제공합니다.

* `cmk_vault_base_url`: 키가 포함된 키 자격 증명 모음의 URL입니다.
* `cmk_key_name`: 키의 이름입니다.
* `cmk_key_version`: 키의 버전입니다.

배포 구성을 만들고 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) 참조
* [배포 위치 및 방법](how-to-deploy-and-where.md)
* [Azure Container Instances에 모델 배포](how-to-deploy-azure-container-instance.md)

ACI에서 고객 관리 키를 사용하는 방법에 대한 자세한 내용은 [고객 관리 키를 사용하여 데이터 암호화를](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)참조하십시오.

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

언제든지 고객 관리 키를 사용하여 배포된 Azure Kubernetes 서비스 리소스를 암호화할 수 있습니다. 자세한 내용은 [Azure Kubernetes 서비스를 통해 사용자 고유의 키 가져오기를](../aks/azure-disk-customer-managed-keys.md)참조하십시오. 

이 프로세스를 사용하면 Kubernetes 클러스터에 배포된 가상 시스템의 데이터와 OS 디스크를 모두 암호화할 수 있습니다.

> [!IMPORTANT]
> 이 프로세스는 AKS K8s 버전 1.17 이상에서만 작동합니다. Azure 기계 학습은 2020년 1월 13일에 AKS 1.17에 대한 지원을 추가했습니다.

#### <a name="machine-learning-compute"></a>기계 학습 컴퓨팅

Azure Storage에 저장된 각 계산 노드의 OS 디스크는 Azure Machine Learning 저장소 계정에서 Microsoft 관리 키로 암호화됩니다. 이 계산 대상은 일시적이며 일반적으로 실행이 큐에 대기되지 않은 경우 클러스터가 축소됩니다. 기본 가상 시스템이 프로비저닝 해제되고 OS 디스크가 삭제됩니다. Azure 디스크 암호화는 OS 디스크에 대해 지원되지 않습니다.

또한 각 가상 시스템에는 OS 작업에 대한 로컬 임시 디스크도 있습니다. 원하는 경우 디스크를 사용하여 학습 데이터를 단계화할 수 있습니다. `hbi_workspace` 디스크는 매개 변수가 로 설정된 작업 `TRUE`영역에 대해 기본적으로 암호화됩니다. 이 환경은 실행 기간 동안에만 수명이 짧으며 암호화 지원은 시스템 관리 키로만 제한됩니다.

#### <a name="azure-databricks"></a>Azure Databricks

Azure 데이터 브릭은 Azure 기계 학습 파이프라인에서 사용할 수 있습니다. 기본적으로 Azure Databricks에서 사용하는 데이터브릭 파일 시스템(DBFS)은 Microsoft 관리 키를 사용하여 암호화됩니다. 고객 관리 키를 사용하도록 Azure Databricks를 구성하려면 [기본(루트) DBFS에서 고객 관리 키 구성](/azure/databricks/security/customer-managed-keys-dbfs)을 참조하십시오.

### <a name="encryption-in-transit"></a>전송 중 암호화

TLS를 사용하여 Azure Machine Learning 마이크로 서비스 간의 내부 통신을 보호하고 점수 매기기 끝점에 대한 외부 호출을 보호할 수 있습니다. 모든 Azure 저장소 액세스는 보안 채널을 통해서도 발생합니다.

자세한 내용은 [TLS 사용을 참조하여 Azure 기계 학습을 통해 웹 서비스를 보호합니다.](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)

### <a name="using-azure-key-vault"></a>Azure Key Vault 사용

Azure 기계 학습은 작업 영역과 연결된 Azure Key Vault 인스턴스를 사용하여 다양한 종류의 자격 증명을 저장합니다.

* 연결된 저장소 계정 연결 문자열
* Azure 컨테이너 리포지토리 인스턴스에 대한 암호
* 데이터 저장소에 문자열 연결

Azure HDInsight 및 VM과 같은 대상을 계산하는 SSH 암호 및 키는 Microsoft 구독과 연결된 별도의 키 자격 증명 모음에 저장됩니다. Azure 기계 학습은 사용자가 제공한 암호 나 키를 저장하지 않습니다. 대신 자체 SSH 키를 생성, 권한 부여 및 저장하여 VM 및 HDInsight에 연결하여 실험을 실행합니다.

각 작업 영역에는 작업 영역과 이름이 같은 연결된 시스템 할당된 관리 ID가 있습니다. 이 관리되는 ID는 키 자격 증명 모음의 모든 키, 암호 및 인증서에 액세스할 수 있습니다.

## <a name="data-collection-and-handling"></a>데이터 수집 및 처리

### <a name="microsoft-collected-data"></a>마이크로 소프트는 데이터를 수집

Microsoft는 리소스 이름(예: 데이터 집합 이름 또는 기계 학습 실험 이름) 또는 진단을 위해 작업 환경 변수와 같은 비사용자 식별 정보를 수집할 수 있습니다. 이러한 모든 데이터는 Microsoft 소유 구독에서 호스팅되는 저장소에 Microsoft 관리 키를 사용하여 저장되며 [Microsoft의 표준 개인 정보 보호 정책 및 데이터 처리 표준을](https://privacy.microsoft.com/privacystatement)따릅니다.

또한 환경 변수에 중요한 정보(예: 계정 키 암호)를 저장하지 않는 것이 좋습니다. 환경 변수는 당사에 의해 기록, 암호화 및 저장됩니다. 마찬가지로 [runid의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)이름을 지정할 때사용자 이름이나 비밀 프로젝트 이름과 같은 중요한 정보를 포함하지 마십시오. 이 정보는 Microsoft 지원 엔지니어가 액세스할 수 있는 원격 분석 로그에 나타날 수 있습니다.

작업 영역을 프로비전하는 동안 매개 `hbi_workspace` 변수를 설정하여 수집되는 진단 데이터를 옵트아웃할 `TRUE` 수 있습니다. 이 기능은 AzureML 파이썬 SDK, CLI, REST API 또는 Azure 리소스 관리자 템플릿을 사용할 때 지원됩니다.

### <a name="microsoft-generated-data"></a>마이크로소프트에서 생성한 데이터

자동화된 기계 학습과 같은 서비스를 사용하는 경우 Microsoft는 여러 모델을 학습하기 위해 사전 처리된 일시적인 데이터를 생성할 수 있습니다. 이 데이터는 작업 공간의 데이터 스토어에 저장되며 액세스 제어 및 암호화를 적절하게 적용할 수 있습니다.

[배포된 끝점에서 기록된 진단 정보를](how-to-enable-app-insights.md) Azure Application Insights 인스턴스로 암호화할 수도 있습니다.

## <a name="monitoring"></a>모니터링

### <a name="metrics"></a>메트릭

Azure 모니터 메트릭을 사용하여 Azure 기계 학습 작업 영역에 대한 메트릭을 보고 모니터링할 수 있습니다. Azure [포털에서](https://portal.azure.com)작업 영역을 선택한 다음 **메트릭을 선택합니다.**

[![작업 영역에 대한 예제 메트릭을 보여주는 스크린샷](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

메트릭에는 실행, 배포 및 등록에 대한 정보가 포함됩니다.

자세한 내용은 [Azure 모니터의 메트릭을](/azure/azure-monitor/platform/data-platform-metrics)참조하십시오.

### <a name="activity-log"></a>활동 로그

작업 영역의 활동 로그를 보고 작업 영역에서 수행되는 다양한 작업을 볼 수 있습니다. 로그에는 작업 이름, 이벤트 개시자 및 타임스탬프와 같은 기본 정보가 포함됩니다.

이 스크린샷은 작업 영역의 활동 로그를 보여 주며,

[![작업 영역의 활동 로그를 보여주는 스크린샷](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

점수 매기기 요청 세부 정보는 응용 프로그램 인사이트에 저장됩니다. 응용 프로그램 인사이트는 작업 영역을 만들 때 구독에서 만들어집니다. 기록된 정보에는 다음과 같은 필드가 포함됩니다.

* HTTP Method
* UserAgent
* 계산 유형
* 요청Url
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure 기계 학습 작업 영역의 일부 작업은 활동 로그에 정보를 기록하지 않습니다. 예를 들어 교육 실행시작과 모델 등록은 기록되지 않습니다.
>
> 이러한 작업 중 일부는 작업 영역의 **활동** 영역에 표시되지만 이러한 알림은 활동을 시작한 사람을 나타내지 않습니다.

## <a name="data-flow-diagrams"></a>데이터 흐름 다이어그램

### <a name="create-workspace"></a>작업 영역 만들기

다음 다이어그램은 작업 영역 만들기 워크플로를 보여 주며,

* 지원되는 Azure 기계 학습 클라이언트(Azure CLI, Python SDK, Azure 포털) 중 하나에서 Azure AD에 로그인하고 적절한 Azure 리소스 관리자 토큰을 요청합니다.
* Azure 리소스 관리자를 호출하여 작업 영역을 만듭니다. 
* Azure 리소스 관리자는 Azure 기계 학습 리소스 공급자에게 작업 영역을 프로비전합니다.

추가 리소스는 작업 영역을 만드는 동안 사용자의 구독에서 만들어집니다.

* 키 볼트 (비밀을 저장하려면)
* Azure 저장소 계정(Blob 및 파일 공유 포함)
* Azure 컨테이너 레지스트리(추론/채점 및 실험을 위해 Docker 이미지를 저장함)
* 원격 분석을 저장하는 응용 프로그램 인사이트

사용자는 필요에 따라 작업 영역에 연결된 다른 계산 대상(예: Azure Kubernetes Service 또는 VM)을 프로비전할 수도 있습니다.

[![작업 영역 워크플로 만들기](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>소스 코드 저장(교육 스크립트)

다음 다이어그램은 코드 스냅숏 워크플로를 보여 주며 있습니다.

Azure 기계 학습 작업 영역과 연결된 소스 코드(학습 스크립트)를 포함하는 디렉터리(실험)가 있습니다. 이러한 스크립트는 로컬 컴퓨터와 클라우드(구독용 Azure Blob 저장소)에 저장됩니다. 코드 스냅숏은 기록 감사를 위해 실행 또는 검사에 사용됩니다.

[![코드 스냅샷 워크플로](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>학습

다음 다이어그램은 교육 워크플로우를 보여 주며 있습니다.

* Azure 기계 학습은 이전 섹션에 저장된 코드 스냅숏에 대한 스냅숏 ID로 호출됩니다.
* Azure Machine Learning은 실행 ID(선택 사항)와 기계 학습 서비스 토큰을 생성하며, 나중에 기계 학습 계산/VM과 같은 계산 대상에서 기계 학습 서비스와 통신하는 데 사용됩니다.
* 관리되는 계산 대상(예: 기계 학습 계산) 또는 VM과 같은 관리되지 않는 계산 대상을 선택하여 교육 작업을 실행할 수 있습니다. 다음은 두 시나리오에 대한 데이터 흐름입니다.
   * Microsoft 구독의 키 자격 증명에서 SSH 자격 증명으로 액세스하는 VM/HDInsight입니다. Azure 기계 학습은 다음과 같은 계산 대상에서 관리 코드를 실행합니다.

   1. 환경을 준비합니다. 도커는 VM 및 로컬 컴퓨터에 대한 옵션입니다. Docker 컨테이너에서 실행 중인 실험이 어떻게 작동하는지 알아보려면 기계 학습 계산의 다음 단계를 참조하십시오.
   1. 코드를 다운로드합니다.
   1. 환경 변수 및 구성을 설정합니다.
   1. 사용자 스크립트(이전 섹션에서 언급한 코드 스냅숏)를 실행합니다.

   * 작업 영역 관리 ID를 통해 액세스되는 기계 학습 계산입니다.
기계 학습 컴퓨팅은 관리되는 컴퓨팅 대상(즉, Microsoft에서 관리)이므로 Microsoft 구독에서 실행됩니다.

   1. 필요한 경우 원격 도커 구조가 시작됩니다.
   1. 관리 코드는 사용자의 Azure 파일 공유에 기록됩니다.
   1. 컨테이너는 초기 명령으로 시작됩니다. 즉, 이전 단계에서 설명한 바와 같이 관리 코드입니다.

#### <a name="querying-runs-and-metrics"></a>실행 및 메트릭 쿼리

아래 흐름 다이어그램에서 이 단계는 학습 계산 대상이 Cosmos DB 데이터베이스의 저장소에서 Azure Machine Learning에 실행 메트릭을 다시 쓸 때 발생합니다. 클라이언트는 Azure 기계 학습을 호출할 수 있습니다. 기계 학습은 Cosmos DB 데이터베이스에서 메트릭을 가져와 클라이언트로 반환합니다.

[![교육 워크플로우](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>웹 서비스 만들기

다음 다이어그램은 추론 워크플로를 보여 주십입니다. 추론 또는 모델 점수 매기기는 배포된 모델이 예측에 사용되는 단계이며, 프로덕션 데이터에서 가장 일반적으로 사용됩니다.

세부 정보는 다음과 같습니다.

* 사용자는 Azure 기계 학습 SDK와 같은 클라이언트를 사용하여 모델을 등록합니다.
* 사용자는 모델, 점수 파일 및 기타 모델 종속성을 사용하여 이미지를 만듭니다.
* Docker 이미지가 만들어지고 Azure 컨테이너 레지스트리에 저장됩니다.
* 웹 서비스는 이전 단계에서 만든 이미지를 사용하여 계산 대상(컨테이너 인스턴스/AKS)에 배포됩니다.
* 점수 매기기 요청 세부 정보는 사용자의 구독에 있는 응용 프로그램 통찰력에 저장됩니다.
* 원격 분석도 Microsoft/Azure 구독으로 푸시됩니다.

[![추론 워크플로우](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [TLS를 통해 안전한 Azure 기계 학습 웹 서비스](how-to-secure-web-service.md)
* [웹 서비스로 배포된 기계 학습 모델 사용](how-to-consume-web-service.md)
* [일괄 처리 예측 실행 방법](how-to-use-parallel-run-step.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure 가상 네트워크에서 Azure 기계 학습 사용](how-to-enable-virtual-network.md)
* [추천 시스템 빌드 모범 사례](https://github.com/Microsoft/Recommenders)
* [Azure에서 실시간 추천 API 빌드](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
