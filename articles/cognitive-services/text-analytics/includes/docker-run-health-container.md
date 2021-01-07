---
title: Docker run 명령의 컨테이너 실행 예
titleSuffix: Azure Cognitive Services
description: 상태 컨테이너에 대 한 Docker run 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: b19fb3f86be46a5db60fb87f9c7f5c3e28ac6428
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965149"
---
## <a name="install-the-container"></a>컨테이너 설치

상태 컨테이너에 대해 Text Analytics를 설치 하 고 실행할 수 있는 여러 가지 방법이 있습니다. 

- [Azure Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) 를 사용 하 여 Text Analytics 리소스를 만들고 Docker를 사용 하 여 컨테이너를 가져옵니다.
- 다음 PowerShell 및 Azure CLI 스크립트를 사용 하 여 리소스 배포 및 컨테이너 구성을 자동화할 수 있습니다.

### <a name="run-the-container-locally"></a>컨테이너를 로컬로 실행

컨테이너 이미지를 다운로드 한 후 사용자 환경에서 컨테이너를 실행 하려면 해당 이미지 ID를 찾습니다.
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

다음 명령을 실행 `docker run` 합니다. 아래 자리 표시자를 사용자 고유의 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | Text Analytics 리소스의 키입니다. 리소스의 **키 및 끝점** 페이지의 Azure Portal에서 찾을 수 있습니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 텍스트 분석 API에 액세스 하기 위한 끝점입니다. 리소스의 **키 및 끝점** 페이지의 Azure Portal에서 찾을 수 있습니다. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | 컨테이너의 이미지 ID입니다. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | 컨테이너의 입력 디렉터리입니다. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

이 명령은 다음을 수행합니다.

- 호스트 컴퓨터에 입력 디렉터리가 존재 한다고 가정 합니다.
- 컨테이너 이미지에서 상태 컨테이너에 대 한 Text Analytics를 실행 합니다.
- 6 CPU 코어 및 12기가바이트 (GB)의 메모리를 할당 합니다.
- 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
- 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

### <a name="demo-ui-to-visualize-output"></a>출력을 시각화 하는 데모 UI

> [!NOTE]
> 데모는 상태 컨테이너의 Text Analytics 에서만 사용할 수 있습니다.

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.  컨테이너의 끝점에 추가 하 여 액세스할 수 있는 시각화 도구를 컨테이너에 제공 했습니다 `/demo` . 예를 들면 다음과 같습니다.

```
http://<serverURL>:5000/demo
```

아래 예제 말아 예를 사용 하 여 배포 된 컨테이너에 쿼리를 제출 하 고 `serverURL` 변수를 적절 한 값으로 바꿉니다.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Azure Web App for Containers을 사용 하 여 컨테이너 설치

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) 는 클라우드에서 실행 중인 컨테이너 전용 azure 리소스입니다. 자동 크기 조정, docker 컨테이너 및 docker 작성, HTTPS 지원 등의 기본 기능을 제공 합니다.

> [!NOTE]
> Azure 웹 앱을 사용 하 여 다음 형식으로 도메인을 자동으로 가져옵니다. `<appservice_name>.azurewebsites.net`

Azure CLI를 사용 하 여이 PowerShell 스크립트를 실행 하 고 HTTPS를 통해 구독 및 컨테이너 이미지를 사용 하 여 Web App for Containers를 만듭니다. 첫 번째 요청을 제출 하기 전에 스크립트가 완료 될 때까지 기다립니다 (약 25-30 분).

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Azure Container Instance를 사용 하 여 컨테이너 설치

또한 ACI (Azure Container Instance)를 사용 하 여 배포를 보다 쉽게 만들 수 있습니다. ACI는 서버를 사용 하지 않는 관리 되는 Azure 환경에서 주문형 Docker 컨테이너를 실행할 수 있는 리소스입니다. 

Azure Portal를 사용 하 여 ACI 리소스를 배포 하는 단계는 [Azure Container Instances 사용 방법을](../how-tos/text-analytics-how-to-use-container-instances.md) 참조 하세요. 컨테이너 이미지를 사용 하 여 구독에서 ACI를 만드는 Azure CLI를 사용 하 여 아래 PowerShell 스크립트를 사용할 수도 있습니다.  첫 번째 요청을 제출 하기 전에 스크립트가 완료 될 때까지 기다립니다 (약 25-30 분).  최대 Cpu 수에 대 한 제한으로 인해 요청 당 5 개 이상의 매우 많은 문서 (각각 5000 자)를 전송 하려는 경우이 옵션을 선택 하지 마십시오.
가용성 정보는 [ACI 지역 지원](../../../container-instances/container-instances-region-availability.md) 문서를 참조 하세요. 

> [!NOTE] 
> Azure Container Instances 기본 제공 도메인에 대 한 HTTPS 지원을 포함 하지 않습니다. HTTPS가 필요한 경우 인증서를 만들고 도메인을 등록 하는 등 수동으로 구성 해야 합니다. 아래 NGINX를 사용 하 여이 작업을 수행 하는 지침을 찾을 수 있습니다.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>보안 ACI 연결

기본적으로 컨테이너 API와 함께 ACI를 사용 하는 경우에는 보안이 제공 되지 않습니다. 일반적으로 컨테이너는 네트워크 브리지가 외부에서 보호 되는 pod의 일부로 실행 되기 때문입니다. 그러나 컨테이너 끝점을 비공개로 유지 하는 프런트 엔드 구성 요소를 사용 하 여 컨테이너를 수정할 수 있습니다. 다음 예에서는 [NGINX](https://www.nginx.com) 를 수신 게이트웨이로 사용 하 여 HTTPS/SSL 및 클라이언트 인증서 인증을 지원 합니다.

> [!NOTE]
> NGINX은 오픈 소스 고성능 HTTP 서버 및 프록시입니다. NGINX 컨테이너는 단일 컨테이너에 대 한 TLS 연결을 종료 하는 데 사용할 수 있습니다. 더 복잡 한 NGINX 수신 기반 TLS 종료 솔루션도 가능 합니다.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>수신 게이트웨이로 NGINX 설정

NGINX는 [구성 파일](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) 을 사용 하 여 런타임에 기능을 사용 하도록 설정 합니다. 다른 서비스에 대 한 TLS 종료를 사용 하도록 설정 하려면 SSL 인증서를 지정 하 여 TLS 연결을 종료 하 고  `proxy_pass` 서비스에 대 한 주소를 지정 해야 합니다. 아래에 예제가 나와 있습니다.


> [!NOTE]
> `ssl_certificate` NGINX 컨테이너의 로컬 파일 시스템 내에 경로를 지정 해야 합니다. 에 지정 된 주소는 `proxy_pass` NGINX 컨테이너의 네트워크 내에서 사용할 수 있어야 합니다.

NGINX 컨테이너는 아래에 탑재 된의 모든 파일을 `_.conf_` `/etc/nginx/conf.d/` HTTP 구성 경로에 로드 합니다.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>예 Docker 작성 파일

아래 예제에서는 NGINX를 배포 하 고 상태 컨테이너에 대해 Text Analytics를 배포 하기 위해 [docker 작성](https://docs.docker.com/compose/reference/overview) 파일을 만드는 방법을 보여 줍니다.

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

이 Docker 작성 파일을 시작 하려면 콘솔에서 파일의 루트 수준에 있는 다음 명령을 실행 합니다.

```bash
docker-compose up
```

자세한 내용은 [NGINX SSL 종료](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)에 대 한 NGINX 설명서를 참조 하세요.

