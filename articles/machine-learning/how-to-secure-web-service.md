---
title: TLS를 사용한 안전한 웹 서비스
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 통해 배포되는 웹 서비스를 보호하기 위해 HTTPS를 사용하도록 설정하는 방법을 알아봅니다. Azure 기계 학습은 TLS 버전 1.2를 사용하여 웹 서비스로 배포된 모델을 보호합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287342"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>TLS를 사용하여 Azure 기계 학습을 통해 웹 서비스 보호
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습을 통해 배포되는 웹 서비스를 보호하는 방법을 보여 주며, 이 문서에서는

[HTTPS를](https://en.wikipedia.org/wiki/HTTPS) 사용하여 웹 서비스에 대한 액세스를 제한하고 클라이언트가 제출하는 데이터를 보호합니다. HTTPS는 둘 사이의 통신을 암호화하여 클라이언트와 웹 서비스 간의 통신을 보호하는 데 도움이 됩니다. 암호화는 [TLS(전송 계층 보안)를](https://en.wikipedia.org/wiki/Transport_Layer_Security)사용합니다. TLS는 TLS의 전신인 *SSL(보안 소켓* 계층)이라고도 합니다.

> [!TIP]
> Azure 기계 학습 SDK는 보안 통신과 관련된 속성에 대해 "SSL"이라는 용어를 사용합니다. 그렇다고 해서 웹 서비스가 *TLS를*사용하지 않는다는 의미는 아닙니다. SSL은 더 일반적으로 인식되는 용어입니다.
>
> 특히 Azure 기계 학습을 통해 배포된 웹 서비스는 TLS 버전 1.2만 지원합니다.

TLS와 SSL은 모두 암호화 및 신원 확인에 도움이 되는 *디지털 인증서에*의존합니다. 디지털 인증서의 작동 방식에 대한 자세한 내용은 위키백과 주제 [공개 키 인프라를](https://en.wikipedia.org/wiki/Public_key_infrastructure)참조하십시오.

> [!WARNING]
> 웹 서비스에 HTTPS를 사용하지 않는 경우 서비스에서 전송되는 데이터가 인터넷의 다른 사람에게 표시될 수 있습니다.
>
> 또한 HTTPS를 사용하면 클라이언트가 연결하는 서버의 신뢰성을 확인할 수 있습니다. 이 기능은 [중간자](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) 공격으로부터 클라이언트를 보호합니다.

다음은 웹 서비스를 보호하는 일반적인 프로세스입니다.

1. 도메인 이름을 가져옵니다.

2. 디지털 인증서를 받으십시오.

3. TLS를 사용하도록 설정하여 웹 서비스를 배포하거나 업데이트합니다.

4. 웹 서비스를 가리키도록 DNS를 업데이트합니다.

> [!IMPORTANT]
> AZURE Kubernetes 서비스(AKS)에 배포하는 경우 자체 인증서를 구입하거나 Microsoft에서 제공하는 인증서를 사용할 수 있습니다. Microsoft의 인증서를 사용하는 경우 도메인 이름 또는 TLS/SSL 인증서를 받을 필요가 없습니다. 자세한 내용은 이 문서의 [TLS 사용 및 배포](#enable) 섹션을 참조하십시오.

[배포 대상](how-to-deploy-and-where.md)간에 s를 보호하는 경우 약간의 차이가 있습니다.

## <a name="get-a-domain-name"></a>도메인 이름 가져오기

아직 도메인 이름을 소유하고 있지 않은 경우 *도메인 이름 등록기관에서*도메인 이름을 구입합니다. 프로세스와 가격은 레지스트라마다 다릅니다. 레지스트라는 도메인 이름을 관리하는 도구를 제공합니다. 이러한 도구를 사용하여 웹 서비스를 호스팅하는 IP 주소에 정규화된\.도메인 이름(예: www contoso.com)을 매핑합니다.

## <a name="get-a-tlsssl-certificate"></a>TLS/SSL 인증서 받기

TLS/SSL 인증서(디지털 인증서)를 받는 방법에는 여러 가지가 있습니다. 가장 일반적인 것은 *CA(인증 기관)에서* 구입하는 것입니다. 인증서를 받는 위치에 관계없이 다음 파일이 필요합니다.

* **인증서**. 인증서에는 전체 인증서 체인이 포함되어야 하며 "PEM 인코딩"이어야 합니다.
* **키**. 키도 PEM 인코딩되어야 합니다.

인증서를 요청할 때 웹 서비스에 사용할 주소(예: www contoso.com)의\.FQDN을 제공해야 합니다. 인증서에 스탬프가 찍힌 주소와 클라이언트가 사용하는 주소를 비교하여 웹 서비스의 ID를 확인합니다. 해당 주소가 일치하지 않으면 클라이언트에 오류 메시지가 표시됩니다.

> [!TIP]
> 인증 기관에서 인증서와 키를 PEM 인코딩된 파일로 제공할 수 없는 경우 [OpenSSL과](https://www.openssl.org/) 같은 유틸리티를 사용하여 형식을 변경할 수 있습니다.

> [!WARNING]
> *자체 서명된* 인증서는 개발에만 사용하십시오. 프로덕션 환경에서 사용하지 마십시오. 자체 서명된 인증서로 인해 클라이언트 애플리케이션에 문제가 발생할 수 있습니다. 자세한 내용은 클라이언트 응용 프로그램에서 사용하는 네트워크 라이브러리에 대한 설명서를 참조하세요.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>TLS 및 배포 를 활성화

TLS를 사용하도록 설정한 서비스를 배포(또는 재배포)하려면 *ssl_enabled* 매개 변수를 해당되는 경우 "True"로 설정합니다. *ssl_certificate* 매개 변수를 *인증서* 파일 값으로 설정합니다. *ssl_key* *키* 파일 값으로 설정합니다.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>AKS 및 현장 프로그래밍 가능한 게이트 어레이(FPGA)에 배포

  > [!NOTE]
  > 이 섹션의 정보는 디자이너에 대한 보안 웹 서비스를 배포할 때도 적용됩니다. 파이썬 SDK 사용에 익숙하지 않은 경우 [파이썬용 Azure 기계 학습 SDK는 무엇입니까?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

AKS에 배포할 때 새 AKS 클러스터를 만들거나 기존 클러스터를 연결할 수 있습니다. 클러스터 를 만들거나 연결하는 방법에 대한 자세한 내용은 [Azure Kubernetes 서비스 클러스터에 모델 배포를](how-to-deploy-azure-kubernetes-service.md)참조하십시오.
  
-  새 클러스터를 만드는 경우 **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** 를 사용합니다.
- 기존 클러스터를 연결하는 경우 **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 를 사용합니다. 둘 다 **enable_ssl** 메서드가 있는 구성 개체를 반환합니다.

**enable_ssl** 메서드는 Microsoft에서 제공하는 인증서 또는 구입한 인증서를 사용할 수 있습니다.

  * Microsoft의 인증서를 사용하는 경우 *leaf_domain_label* 매개 변수를 사용해야 합니다. 이 매개 변수는 서비스에 대한 DNS 이름을 생성합니다. 예를 들어 "contoso" 값은 "contoso\<6랜덤 문자> 도메인 이름을 만듭니다. \<azureregion>.cloudapp.azure.com", \<azureregion> 서비스를 포함 하는 지역입니다. 선택적으로 *overwrite_existing_domain* 매개 변수를 사용하여 기존 *leaf_domain_label*덮어쓸 수 있습니다.

    TLS를 사용하도록 설정한 서비스를 배포(또는 재배포)하려면 *ssl_enabled* 매개 변수를 해당되는 경우 "True"로 설정합니다. *ssl_certificate* 매개 변수를 *인증서* 파일 값으로 설정합니다. *ssl_key* *키* 파일 값으로 설정합니다.

    > [!IMPORTANT]
    > Microsoft의 인증서를 사용하는 경우 자신의 인증서 나 도메인 이름을 구입할 필요가 없습니다.

    다음 예제에서는 Microsoft에서 TLS/SSL 인증서를 사용할 수 있는 구성을 만드는 방법을 보여 줍니다.

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * *구입한 인증서를*사용하는 경우 *ssl_cert_pem_file*, *ssl_key_pem_file*및 *ssl_cname* 매개 변수를 사용합니다. 다음 예제에서는 *.pem* 파일을 사용하여 구입한 TLS/SSL 인증서를 사용하는 구성을 만드는 방법을 보여 줍니다.

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

*enable_ssl*대한 자세한 내용은 [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) 및 [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)을 참조하십시오.

### <a name="deploy-on-azure-container-instances"></a>Azure 컨테이너 인스턴스에 배포

Azure 컨테이너 인스턴스에 배포할 때 다음 코드 코드 조각에서 와 같이 TLS 관련 매개 변수에 대한 값을 제공합니다.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

자세한 내용은 [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)을 참조하십시오.

## <a name="update-your-dns"></a>DNS 업데이트

다음으로는 웹 서비스를 가리키도록 DNS를 업데이트해야 합니다.

+ **컨테이너 인스턴스의 경우:**

  도메인 이름 등록 기관의 도구를 사용하여 도메인 이름에 대한 DNS 레코드를 업데이트합니다. 레코드는 서비스의 IP 주소를 가리키야 합니다.

  레지스트라와 도메인 이름에 대해 구성된 "TTL"(생활 시간)에 따라 클라이언트가 도메인 이름을 확인하려면 몇 분 또는 몇 시간이 지연될 수 있습니다.

+ **AKS의 경우:**

  > [!WARNING]
  > *microsoft의* 인증서를 사용하여 서비스를 만드는 데 leaf_domain_label 사용한 경우 클러스터의 DNS 값을 수동으로 업데이트하지 마십시오. 값을 자동으로 설정해야 합니다.

  왼쪽 창의 **설정** **아래의 구성** 탭에서 AKS 클러스터의 공용 IP 주소의 DNS를 업데이트합니다. (다음 이미지를 참조하십시오.) 공용 IP 주소는 AKS 에이전트 노드 및 기타 네트워킹 리소스를 포함하는 리소스 그룹 아래에 만들어진 리소스 유형입니다.

  [![Azure 기계 학습: TLS를 통해 웹 서비스 보안](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>TLS/SSL 인증서 업데이트

TLS/SSL 인증서가 만료되어 갱신해야 합니다. 일반적으로 이것은 매년 발생합니다. 다음 섹션의 정보를 사용하여 Azure Kubernetes 서비스에 배포된 모델에 대한 인증서를 업데이트하고 갱신합니다.

### <a name="update-a-microsoft-generated-certificate"></a>Microsoft에서 생성된 인증서 업데이트

인증서가 원래 Microsoft에서 생성된 *경우(leaf_domain_label* 사용하여 서비스를 만들 때) 다음 예제 중 하나를 사용하여 인증서를 업데이트합니다.

**SDK 사용**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**CLI 사용**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

자세한 내용은 다음 참조 문서를 참조하십시오.

* [Ssl 구성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdate구성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>사용자 지정 인증서 업데이트

인증서가 원래 인증서 기관에 의해 생성된 경우 다음 단계를 사용합니다.

1. 인증서 기관에서 제공한 설명서를 사용하여 인증서를 갱신합니다. 이 프로세스는 새 인증서 파일을 만듭니다.

1. SDK 또는 CLI를 사용하여 새 인증서로 서비스를 업데이트합니다.

    **SDK 사용**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **CLI 사용**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

자세한 내용은 다음 참조 문서를 참조하십시오.

* [Ssl 구성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdate구성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>TLS 사용 안 함

Azure Kubernetes 서비스에 배포된 모델에 대한 TLS를 사용하지 않으려면 `SslConfiguration` 다음을 `status="Disabled"`사용하여 다음을 만듭니다.

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>다음 단계
방법 배우기:
+ [웹 서비스로 배포된 기계 학습 모델 사용](how-to-consume-web-service.md)
+ [Azure 가상 네트워크 내에서 실험 및 추론을 안전하게 실행](how-to-enable-virtual-network.md)
