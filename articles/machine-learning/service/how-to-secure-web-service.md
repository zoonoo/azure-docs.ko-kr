---
title: SSL을 사용하여 Azure Machine Learning 웹 서비스 보호
description: Azure Machine Learning 서비스를 통해 배포되는 웹 서비스를 보호하는 방법을 알아봅니다. SSL(Secure Socket Layer) 및 키 기반 인증을 사용하여 클라이언트가 제출하는 데이터를 보호하고 웹 서비스 액세스를 제한할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801015"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>SSL을 사용하여 Azure Machine Learning 웹 서비스 보호

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

> [!IMPORTANT]
> 자체 서명된 인증서는 개발에만 사용해야 합니다. 프로덕션에 사용해서는 안됩니다. 자체 서명된 인증서를 사용하는 경우 [자체 서명된 인증서를 통해 웹 서비스 사용](#self-signed) 섹션에서 구체적인 지침을 참조하세요.


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

  ![Azure Machine Learning 서비스: SSL을 사용하여 웹 서비스 보호](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>인증된 서비스 사용

### <a name="how-to-consume"></a>사용 방법 
+ **ACI 및 AKS**: 

  ACI 및 AKS 웹 서비스의 경우 다음 문서에서 웹 서비스 사용 방법을 확인할 수 있습니다.
  + [ACI에 배포하는 방법](how-to-deploy-to-aci.md)

  + [AKS에 배포하는 방법](how-to-deploy-to-aks.md)

+ **FPGA**:  

  다음 예제는 Python과 C#을 사용하여 인증된 FPGA 서비스를 사용하는 방법을 보여 줍니다.
  `authkey`는 서비스를 배포할 때 반환된 기본 키 또는 보조 키로 바꿉니다.

  Python 예제:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  C# 예제:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>인증 헤더 설정
다른 gRPC 클라이언트는 인증 헤더를 설정하여 요청을 인증할 수 있습니다. 일반적인 방법은 `SslCredentials`와 `CallCredentials`를 결합하는 `ChannelCredentials` 개체를 생성하는 것입니다. 이것은 요청의 인증 헤더에 추가됩니다. 특정 헤더에 대한 지원 구현에 대한 자세한 내용은 [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html)을 참조하세요.

다음 예제에서는 C# 및 Go에서 헤더를 설정하는 방법을 보여줍니다.

+ C# 사용 시에는 다음과 같이 헤더를 설정합니다.
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Go 사용 시에는 다음과 같이 헤더를 설정합니다.
    ```go
    conn, err := grpc.Dial(serverAddr, 
        grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
        grpc.WithPerRPCCredentials(&authCreds{
        Key: "authKey"}))
    
    type authCreds struct {
        Key string
    }
    
    func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
        return map[string]string{
            "authorization": c.Key,
        }, nil
    }
    
    func (c *authCreds) RequireTransportSecurity() bool {
        return true
    }
    ```

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>자체 서명된 인증서로 서비스 사용

클라이언트가 자체 서명된 인증서로 보안이 설정된 서버를 인증할 수 있게 설정하는 방법은 두 가지입니다.

* 클라이언트 시스템에서, 클라이언트 시스템의 `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 환경 변수가 인증서 파일을 가리키도록 설정합니다.

* `SslCredentials` 개체를 생성할 때 인증서 파일의 콘텐츠를 생성자에게 전달합니다.

두 가지 방법 중 하나를 사용하면 gRPC가 인증서를 루트 인증서로 사용하게 됩니다.

> [!IMPORTANT]
> gRPC는 신뢰할 수 없는 인증서를 허용하지 않습니다. 신뢰할 수 없는 인증서를 사용하면 `Unavailable` 상태 코드가 발생하면서 실패합니다. 실패의 세부 정보에는 `Connection Failed`가 포함됩니다.
