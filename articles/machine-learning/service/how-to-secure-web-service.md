---
title: SSL로 웹 서비스 보호
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 통해 배포되는 웹 서비스를 보호하는 방법을 알아봅니다. SSL(Secure Socket Layer) 및 키 기반 인증을 사용하여 클라이언트가 제출하는 데이터를 보호하고 웹 서비스 액세스를 제한할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.custom: seodec18
ms.openlocfilehash: 14350a04326ba22dcc5c8608b6ac6b9180666832
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101206"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>SSL을 사용하여 Azure Machine Learning 서비스로 웹 서비스 보호

이 문서에서는 Azure Machine Learning 서비스를 통해 배포되는 웹 서비스를 보호하는 방법을 알아봅니다. SSL(Secure Socket Layer) 및 키 기반 인증을 사용하여 클라이언트가 제출하는 데이터를 보호하고 웹 서비스 액세스를 제한할 수 있습니다.

> [!Warning]
> SSL을 사용하지 않으면 인터넷상의 모든 사용자가 웹 서비스를 호출할 수 있습니다.

SSL은 클라이언트와 웹 서비스 간에 전송되는 데이터를 암호화합니다. 클라이언트가 서버의 ID를 확인하는 데도 사용됩니다. 인증은 SSL 인증서와 키를 제공한 서비스에서만 사용할 수 있습니다.  SSL을 사용하면 웹 서비스에 액세스할 때 인증 키가 필요합니다.

SSL을 사용하도록 설정된 웹 서비스를 배포하는 경우와 기존에 배포한 웹 서비스에 대해 SSL을 사용하도록 설정하는 경우에 모두 아래의 동일한 단계를 수행합니다.

1. 도메인 이름을 가져옵니다.

2. SSL 인증서를 가져옵니다.

3. SSL 설정이 사용되는 웹 서비스를 배포하거나, 웹 서비스가 SSL 설정을 사용하도록 업데이트합니다.

4. 웹 서비스를 가리키도록 DNS를 업데이트합니다.

여러 [배포 대상](how-to-deploy-and-where.md)에서 웹 서비스를 보호하는 방식은 약간 다릅니다. 

## <a name="get-a-domain-name"></a>도메인 이름 가져오기

도메인 이름을 소유하지 않은 경우 __도메인 이름 등록 기관__에서 구입할 수 있습니다. 프로세스는 등록 기관마다 다르며 비용도 그렇습니다. 등록 기관은 도메인 이름을 관리하는 도구도 제공합니다. 이러한 도구는 웹 서비스를 호스팅하는 IP 주소에 정규화된 도메인 이름(예: www.contoso.com)을 매핑하는 데 사용됩니다.

## <a name="get-an-ssl-certificate"></a>SSL 인증서 가져오기

SSL 인증서를 확보하는 방법은 여러 가지입니다. 가장 일반적인 방법은 CA(__인증 기관__)에서 구입하는 것입니다. 인증서를 어디에서 확보하든 다음과 같은 파일이 필요합니다.

* __인증서__. 인증서는 전체 인증서 체인을 포함해야 하며 PEM으로 인코딩되어야 합니다.
* __키__. 키는 PEM으로 인코딩되어야 합니다.

인증서를 요청할 때는 웹 서비스에 사용할 주소의 FQDN(정규화된 도메인 이름)을 제공해야 합니다. 예: www.contoso.com. 인증서에 스탬프가 지정된 주소와 클라이언트에서 사용되는 주소는 웹 서비스의 ID를 확인할 때 비교됩니다. 주소가 일치하지 않으면 클라이언트로 오류가 전송됩니다. 

> [!TIP]
> 인증 기관이 인증서와 키를 PEM으로 인코딩된 파일로 제공할 수 없는 경우 [OpenSSL](https://www.openssl.org/)과 같은 유틸리티를 사용하여 형식을 변경할 수 있습니다.

> [!WARNING]
> 자체 서명된 인증서는 개발에만 사용해야 합니다. 프로덕션에 사용해서는 안됩니다. 자체 서명된 인증서로 인해 클라이언트 애플리케이션에 문제가 발생할 수 있습니다. 자세한 내용은 클라이언트 애플리케이션에 사용된 네트워크 라이브러리에 대한 설명서를 참조하세요.

## <a name="enable-ssl-and-deploy"></a>SSL를 사용하도록 설정하고 배포

SSL를 사용하도록 설정하여 서비스를 배포하거나 다시 배포하려면 해당하는 모든 위치에서 `ssl_enabled` 매개 변수를 `True`로 설정합니다. `ssl_certificate` 매개 변수를 __인증서__ 파일의 값으로 설정하고 `ssl_key`를 __키__ 파일의 값으로 설정합니다. 

+ **AKS(Azure Kubernetes Service)에서 배포**
  
  AKS 클러스터를 프로비전할 때는 아래 코드 조각에 나와 있는 것처럼 SSL 관련 매개 변수의 값을 제공합니다.

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **ACI(Azure Container Instances)에서 배포**
 
  ACI로 배포할 때는 아래 코드 조각에 나와 있는 것처럼 SSL 관련 매개 변수의 값을 제공합니다.

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **FPGA(Field-programmable Gate Arrays)에서 배포**

  `create_service` 작업의 응답에는 서비스의 IP 주소가 포함됩니다. IP 주소는 DNS 이름을 서비스의 IP 주소에 매핑할 때 사용됩니다. 응답에는 서비스를 소비하는 데 사용되는 __기본 키__와 __보조 키__도 포함됩니다. 아래 코드 조각에 나와 있는 것처럼 SSL 관련 매개 변수의 값을 제공합니다.

    ```python
    from amlrealtimeai import DeploymentClient
    
    subscription_id = "<Your Azure Subscription ID>"
    resource_group = "<Your Azure Resource Group Name>"
    model_management_account = "<Your AzureML Model Management Account Name>"
    location = "eastus2"
    
    model_name = "resnet50-model"
    service_name = "quickstart-service"
    
    deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)
    
    with open('cert.pem','r') as cert_file:
        with open('key.pem','r') as key_file:
            cert = cert_file.read()
            key = key_file.read()
            service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
    ```

## <a name="update-your-dns"></a>DNS 업데이트

다음으로는 웹 서비스를 가리키도록 DNS를 업데이트해야 합니다.

+ **ACI 및 FPGA**:  

  도메인 이름 등록 기관이 제공하는 도구를 사용하여 도메인 이름의 DNS 레코드를 업데이트합니다. 레코드는 서비스의 IP 주소를 가리켜야 합니다.  

  등록 기관 및 도메인 이름에 대해 구성된 TTL(Time To Live)에 따라 클라이언트가 도메인 이름을 확인하는 데 몇 분에서 몇 시간이 걸릴 수 있습니다.

+ **AKS**: 

  아래 그림과 같이 AKS 클러스터 "공용 IP 주소"의 "구성" 탭에서 DNS를 업데이트합니다. 공용 IP 주소는 AKS 에이전트 노드 및 기타 네트워킹 리소스를 포함하는 리소스 그룹 아래에 생성된 리소스 종류 중 하나로 표시됩니다.

  ![Azure Machine Learning 서비스: SSL로 웹 서비스 보호](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>다음 단계

[웹 서비스로 배포된 ML 모델을 사용](how-to-consume-web-service.md)하는 방법에 대해 알아봅니다.