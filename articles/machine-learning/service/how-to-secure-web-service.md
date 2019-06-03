---
title: SSL을 사용 하 여 웹 서비스를 보호 합니다.
titleSuffix: Azure Machine Learning service
description: HTTPS를 사용 하 여 Azure Machine Learning 서비스를 통해 배포 된 웹 서비스를 보호 하는 방법에 알아봅니다. HTTPS 전송 계층 보안 (TLS)를 보안 소켓 레이어 (SSL)에 대 한 대체를 사용 하 여 클라이언트에서 데이터를 보호 합니다. 또한 클라이언트는 웹 서비스의 id를 확인 하려면 HTTPS를 사용 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 779a34800057284ce77b6d76e030ddca5fadc25f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393817"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>SSL을 사용 하 여 Azure 기계 학습을 통해 웹 서비스에 보안 설정

이 문서에서는 Azure Machine Learning 서비스를 통해 배포 된 웹 서비스를 보호 하는 방법을 보여 줍니다.

사용할 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 웹 서비스에 대 한 액세스를 제한 하 여 클라이언트에서 제출 하는 데이터를 보호 합니다. HTTPS는 둘 간의 통신을 암호화 하 여 클라이언트와 웹 서비스 간의 보안 통신을 수 있습니다. 암호화 사용 [전송 계층 보안 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)합니다. TLS는 여전히 라고도 *Secure Sockets Layer* TLS 선행 되었습니다 (SSL).

> [!TIP]
> Azure Machine Learning SDK 안전한 통신을 위해 관련 된 속성에 대 한 "SSL" 라는 용어를 사용 합니다. 웹 서비스 사용 하지 않는 것은 아닙니다 *TLS*합니다. SSL은 방금 자주 인식 된 용어입니다.

TLS 및 SSL 둘 다 *디지털 인증서*, 암호화 및 id 확인에 도움이 되는 합니다. 디지털 인증서 작업에 대 한 자세한 내용은 Wikipedia 항목을 참조 하세요 [공개 키 인프라](https://en.wikipedia.org/wiki/Public_key_infrastructure)합니다.

> [!WARNING]
> 웹 서비스에 대 한 HTTPS를 사용 하지 않는 경우 데이터의 서비스에서 전송 되는 인터넷에서 다른 사람이 볼 수 있습니다.
>
> HTTPS 클라이언트를 연결 하는 서버의 신뢰성 확인 있습니다. 이 기능에 대해 클라이언트를 보호 [중간자 개입](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) 공격입니다.

다음은 웹 서비스를 보호 하는 일반적인 프로세스입니다.

1. 도메인 이름을 가져옵니다.

2. 디지털 인증서를 받습니다.

3. 배포 또는 ssl 사용 웹 서비스를 업데이트 합니다.

4. 웹 서비스를 가리키도록 DNS를 업데이트합니다.

> [!IMPORTANT]
> Azure Kubernetes Service (AKS)를 배포 하는 경우에 사용자 고유의 인증서를 구입 하거나 Microsoft에서 제공 하는 인증서를 사용 하 여 수 있습니다. Microsoft에서 발급 한 인증서를 사용 하는 경우에 도메인 이름 또는 SSL 인증서를 가져올 필요가 없습니다. 자세한 내용은 참조는 [SSL을 사용 하도록 설정 하 고 배포](#enable) 이 문서의 섹션입니다.

약간의 차이가 있습니다 웹 서비스에서 보안을 유지 하면 [배포 대상](how-to-deploy-and-where.md)합니다.

## <a name="get-a-domain-name"></a>도메인 이름 가져오기

도메인 이름을 이미 소유 중인 없는 경우 구매에서 하나를 *도메인 이름 등록 기관*합니다. 프로세스 및 가격 등록자 간에 다릅니다. 등록 기관은 도메인 이름을 관리 하기 위한 도구를 제공 합니다. 이러한 도구를 사용 하 여 정규화 된 도메인 이름 (FQDN)에 매핑할 (www 같은\.contoso.com) 웹 서비스를 호스팅하는 IP 주소입니다.

## <a name="get-an-ssl-certificate"></a>SSL 인증서 가져오기

여러 가지 방법으로 SSL 인증서 (디지털 인증서)을 가져오려고 합니다. 하나를 구입 하는 가장 일반적인 것을 *기관* (CA). 인증서를 가져오는 위치에 관계 없이 다음 파일이 필요 합니다.

* **인증서**. 인증서에는 전체 인증서 체인을 포함 해야 하며 해당 "PEM으로 인코딩되어야 합니다."
* **키**. 키를 PEM으로 인코딩된 수도 있어야 합니다.

인증서를 요청 하는 경우에 웹 서비스에 사용 하려는 주소의 FQDN을 제공 해야 합니다 (예: www\.contoso.com). 인증서에 스탬프가 지정 되어 있는 주소와 클라이언트를 사용 하는 주소는 웹 서비스의 id를 확인 하려면 비교 됩니다. 해당 주소가 일치 하지 않는 경우 클라이언트는 오류 메시지를 가져옵니다.

> [!TIP]
> 와 같은 유틸리티를 기관을 제공할 수 없으면 인증서 및 키를 PEM으로 인코딩된 파일로 사용할 수 있습니다 [OpenSSL](https://www.openssl.org/) 서식을 변경 합니다.

> [!WARNING]
> 사용 하 여 *자체 서명 된* 개발 작업에 인증서입니다. 프로덕션 환경에는 해당 사용 하지 마세요. 자체 서명된 인증서로 인해 클라이언트 애플리케이션에 문제가 발생할 수 있습니다. 자세한 내용은 클라이언트 응용 프로그램을 사용 하는 네트워크 라이브러리에 대 한 설명서를 참조 하세요.

## <a id="enable"></a> SSL을 사용 하도록 설정 및 배포

Ssl 사용 서비스를 배포 (또는 다시 배포)를 설정 합니다 *ssl_enabled* 적용 될 경우 항상 "True"로 매개 변수입니다. 설정 합니다 *ssl_certificate* 값으로 매개 변수를 *인증서* 파일입니다. 설정 된 *ssl_key* 의 값에는 *키* 파일.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>AKS 및 프로그래밍 가능한 필드 FPGA (gate array)에 배포

  > [!NOTE]
  > 이 섹션의 정보 시각적 인터페이스에 대 한 보안 웹 서비스를 배포 하는 경우에 적용 됩니다. Python SDK를 사용 하 여 익숙하지 참조 [Python 용 Azure Machine Learning SDK 란?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)합니다.

AKS에 배포 하는 경우에 새 AKS 클러스터를 만들 수도 있고 기존 연결 수 있습니다.
  
-  새 클러스터를 만드는 경우 사용할  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-)** 합니다.
- 기존 클러스터에 연결 하는 경우 사용할  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-)** 합니다. 둘 다 반환에 구성 개체를 **enable_ssl** 메서드.

합니다 **enable_ssl** 메서드는 Microsoft에서 제공 하는 인증서 또는 인증서를 구입 하는 사용할 수 있습니다.

  * Microsoft에서 발급 한 인증서를 사용 하는 경우 사용 해야 합니다 *leaf_domain_label* 매개 변수입니다. 이 매개 변수는 서비스에 대 한 DNS 이름을 생성합니다. 예를 들어 값이 "myservice" 만듭니다의 도메인 이름을 "myservice\<임의의 문자 6 >.\< azureregion >. cloudapp.azure.com "여기서 \<azureregion > 서비스를 포함 하는 영역입니다. 사용할 수 있습니다 합니다 *overwrite_existing_domain* 매개 변수는 기존 파일 덮어쓰기 *leaf_domain_label*합니다.

    Ssl 사용 서비스를 배포 (또는 다시 배포)를 설정 합니다 *ssl_enabled* 적용 될 경우 항상 "True"로 매개 변수입니다. 설정 합니다 *ssl_certificate* 값으로 매개 변수를 *인증서* 파일입니다. 설정 된 *ssl_key* 의 값에는 *키* 파일.

    > [!IMPORTANT]
    > Microsoft에서 발급 한 인증서를 사용 하면 사용자 고유의 인증서 또는 도메인 이름을 구입할 필요가 없습니다.

    다음 예제에서는 Microsoft의 SSL 인증서를 사용 하도록 설정 하는 구성을 만드는 방법을 보여 줍니다.

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * 사용 하는 경우 *구입한 인증서*를 사용 합니다 *ssl_cert_pem_file*, *ssl_key_pem_file*, 및 *ssl_cname* 매개 변수입니다. 다음 예제에서는 사용 하는 방법을 보여 줍니다 *.pem* 구입한 SSL 인증서를 사용 하는 구성을 만드는 데 파일:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

에 대 한 자세한 내용은 *enable_ssl*를 참조 하세요 [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) 하 고 [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)합니다.

### <a name="deploy-on-azure-container-instances"></a>Azure Container Instances에 배포

Azure Container Instances에 배포할 때 제공한 값 SSL 관련 매개 변수 다음 코드 조각과 같이:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

자세한 내용은 [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-)합니다.

## <a name="update-your-dns"></a>DNS 업데이트

다음으로는 웹 서비스를 가리키도록 DNS를 업데이트해야 합니다.

+ **컨테이너 인스턴스:**

  도메인 이름 등록자에서 도구를 사용 하 여 도메인 이름의 DNS 레코드를 업데이트 합니다. 레코드는 서비스의 IP 주소를 가리켜야 합니다.

  클라이언트가 도메인 이름 등록자 및 "time to live"에 따라 (TTL) 도메인 이름에 대해 구성 된 확인할 수 시간 또는 분의 지연이 있을 수 있습니다.

+ **AKS에 대 한:**

  > [!WARNING]
  > 사용 하는 경우 *leaf_domain_label* Microsoft에서 발급 한 인증서를 사용 하 여 서비스를 만들려면 클러스터에 대 한 DNS 값을 업데이트 수동으로 하지 않습니다. 값을 자동으로 설정 되어야 합니다.

  DNS를 업데이트 합니다 **구성** AKS 클러스터의 공용 IP 주소 탭 합니다. (다음 이미지 참조). 공용 IP 주소는 AKS 에이전트 노드 및 기타 네트워킹 리소스를 포함 하는 리소스 그룹에 따라 생성 된 리소스 형식입니다.

  ![Azure Machine Learning 서비스: SSL로 웹 서비스 보호](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>다음 단계
다음 작업을 수행하는 방법을 배워 보십시오.
+ [웹 서비스로 배포된 기계 학습 모델 사용](how-to-consume-web-service.md)
+ [실험 및 Azure virtual network 내에서 유추를 안전 하 게 실행](how-to-enable-virtual-network.md)
