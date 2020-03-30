---
title: 사이드카 컨테이너로 SSL 사용
description: 사이드카 컨테이너에서 Nginx를 실행하여 Azure 컨테이너 인스턴스에서 실행 중인 컨테이너 그룹에 대한 SSL 또는 TLS 끝점 만들기
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 43b39c7c13d6d5e52aae2ce1706e4880ab27d225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294954"
---
# <a name="enable-an-ssl-endpoint-in-a-sidecar-container"></a>사이드카 컨테이너에서 SSL 엔드포인트 사용

이 문서에서는 응용 프로그램 [컨테이너와](container-instances-container-groups.md) SSL 공급자를 실행 하는 사이드카 컨테이너를 사용 하 여 컨테이너 그룹을 만드는 방법을 보여 주며 있습니다. 별도의 SSL 끝점으로 컨테이너 그룹을 설정하면 응용 프로그램 코드를 변경하지 않고 응용 프로그램에 대한 SSL 연결을 사용하도록 설정합니다.

두 개의 컨테이너로 구성된 예제 컨테이너 그룹을 설정합니다.
* 공용 Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 이미지를 사용하여 간단한 웹 앱을 실행하는 응용 프로그램 컨테이너입니다. 
* 공용 [Nginx](https://hub.docker.com/_/nginx) 이미지를 실행하는 사이드카 컨테이너로 SSL을 사용하도록 구성됩니다. 

이 예제에서 컨테이너 그룹은 공용 IP 주소가 있는 Nginx에 대한 포트 443만 노출합니다. Nginx는 포트 80에서 내부적으로 수신하는 도우미 웹 앱으로 HTTPS 요청을 라우팅합니다. 다른 포트에서 수신 하는 컨테이너 앱에 대 한 예제를 조정할 수 있습니다. 

컨테이너 그룹에서 SSL을 사용하도록 설정하는 다른 방법에 대한 [다음 단계를](#next-steps) 참조하십시오.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 문서를 완료할 수 있습니다. 로컬로 사용하려면 2.0.55 버전 이상이 권장됩니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하십시오.

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

Nginx를 SSL 공급자로 설정하려면 SSL 인증서가 필요합니다. 이 문서에서는 자체 서명된 SSL 인증서를 만들고 설정하는 방법을 보여 주며 있습니다. 프로덕션 시나리오의 경우 인증서 기관에서 인증서를 가져와야 합니다.

자체 서명된 SSL 인증서를 만들려면 Azure Cloud Shell 및 여러 Linux 배포판에서 사용할 수 있는 [OpenSSL](https://www.openssl.org/) 도구를 사용하거나 운영 체제에서 유사한 클라이언트 도구를 사용합니다.

먼저 로컬 작업 디렉토리에 인증서 요청(.csr 파일)을 만듭니다.

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

프롬프트에 따라 식별 정보를 추가합니다. 일반 이름의 경우 인증서와 연결된 호스트 이름을 입력합니다. 암호를 입력하라는 메시지가 표시되면 입력하지 않고 Enter를 눌러 암호 추가를 건너뜁니다.

다음 명령을 실행하여 인증서 요청에서 자체 서명된 인증서(.crt file)를 만듭니다. 예를 들어:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

이제 디렉터리에서 인증서 요청(), 개인 키()`ssl.csr``ssl.key`및 자체 서명된 인증서()의`ssl.crt`세 개의 파일이 표시됩니다. 이후 `ssl.key` `ssl.crt` 단계에서 사용합니다.

## <a name="configure-nginx-to-use-ssl"></a>SSL을 사용하도록 Nginx 구성

### <a name="create-nginx-configuration-file"></a>Nginx 구성 파일 만들기

이 섹션에서는 Nginx가 SSL을 사용할 수 있도록 구성 파일을 만듭니다. 먼저 다음 텍스트를 라는 `nginx.conf`새 파일에 복사합니다. Azure Cloud Shell에서 Visual Studio 코드를 사용하여 작업 디렉터리에서 파일을 만들 수 있습니다.

```console
code nginx.conf
```

에서 `location`앱에 대한 `proxy_pass` 올바른 포트로 설정해야 합니다. 이 예제에서는 컨테이너에 대 한 `aci-helloworld` 포트 80을 설정 합니다.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-인코딩 비밀 및 구성 파일

Base64 Nginx 구성 파일, SSL 인증서 및 SSL 키를 인코딩합니다. 다음 섹션에서는 컨테이너 그룹을 배포하는 데 사용되는 YAML 파일에 인코딩된 내용을 입력합니다.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>컨테이너 그룹 배포

이제 [YAML 파일에서](container-instances-multi-container-yaml.md)컨테이너 구성을 지정하여 컨테이너 그룹을 배포합니다.

### <a name="create-yaml-file"></a>YAML 파일 만들기

다음 YAML을 라는 `deploy-aci.yaml`새 파일에 복사합니다. Azure Cloud Shell에서 Visual Studio 코드를 사용하여 작업 디렉터리에서 파일을 만들 수 있습니다.

```console
code deploy-aci.yaml
```

아래에 `secret`표시된 base64 인코딩 된 파일의 내용을 입력합니다. 예를 `cat` 들어, 각 base64 인코딩된 파일은 해당 내용을 볼 수 있습니다. 배포 하는 동안 이러한 파일 컨테이너 그룹의 [비밀 볼륨에](container-instances-volume-secret.md) 추가 됩니다. 이 예제에서는 비밀 볼륨이 Nginx 컨테이너에 탑재됩니다.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>컨테이너 그룹 배포

az 그룹이 명령을 만드는 리소스 그룹을 [만듭니다.](/cli/azure/group#az-group-create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

[Az 컨테이너 create](/cli/azure/container#az-container-create) 명령을 사용하여 컨테이너 그룹을 배포하여 YAML 파일을 인수로 전달합니다.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>배포 상태 확인

배포 상태를 확인하려면 [az container show](/cli/azure/container#az-container-show) 명령을 사용합니다.

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

성공적인 배포의 경우 출력은 다음과 유사합니다.

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>SSL 연결 확인

브라우저를 사용하여 컨테이너 그룹의 공용 IP 주소로 이동합니다. 이 예제에 표시된 IP `52.157.22.76`주소는 은 **https://52.157.22.76**. Nginx 서버 구성으로 인해 실행 중인 응용 프로그램을 보려면 HTTPS를 사용해야 합니다. HTTP를 통해 연결하려고 시도실패합니다.

![Azure 컨테이너 인스턴스에서 실행되는 애플리케이션을 보여주는 브라우저 스크린샷](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> 이 예제에서는 인증 기관의 인증서가 아닌 자체 서명된 인증서를 사용하기 때문에 브라우저는 HTTPS를 통해 사이트에 연결할 때 보안 경고를 표시합니다. 페이지를 진행하려면 경고를 수락하거나 브라우저 또는 인증서 설정을 조정해야 할 수 있습니다. 이는 정상적인 동작입니다.

>

## <a name="next-steps"></a>다음 단계

이 문서에서는 컨테이너 그룹에서 실행 중인 웹 앱에 대한 SSL 연결을 사용하도록 Nginx 컨테이너를 설정하는 방법을 보여 주어 있습니다. 포트 80이 아닌 다른 포트에서 수신 하는 앱에 대해 이 예제를 조정할 수 있습니다. 또한 Nginx 구성 파일을 업데이트하여 포트 80(HTTP)에서 서버 연결을 자동으로 리디렉션하여 HTTPS를 사용할 수도 있습니다.

이 문서에서는 사이드카에서 Nginx를 사용하지만 [Caddy와](https://caddyserver.com/)같은 다른 SSL 공급자를 사용할 수 있습니다.

[Azure 가상 네트워크에](container-instances-vnet.md)컨테이너 그룹을 배포하는 경우 다음을 포함하여 백 엔드 컨테이너 인스턴스에 대한 SSL 끝점을 사용하도록 설정하는 다른 옵션을 고려할 수 있습니다.

* [Azure 함수 프록시](../azure-functions/functions-proxies.md)
* [Azure API 관리](../api-management/api-management-key-concepts.md)
* [Azure 응용 프로그램 게이트웨이](../application-gateway/overview.md) - 샘플 [배포 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)참조하십시오.
