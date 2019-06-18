---
title: Azure Container Instances에서 SSL을 사용 하도록 설정
description: Azure Container Instances에서 실행 중인 컨테이너 그룹에 대 한 SSL 또는 TLS 끝점 만들기
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 12de4ef31084d8ac8586c79ffe3d0a8e891727bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411394"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>컨테이너 그룹의 SSL 끝점을 사용 하도록 설정

이 문서에서는 만드는 방법을 보여 줍니다.는 [컨테이너 그룹](container-instances-container-groups.md) 응용 프로그램 컨테이너 및 SSL 공급자를 실행 하는 사이드카 컨테이너입니다. 별도 SSL 끝점을 사용 하 여 컨테이너 그룹을 설정 하 여 응용 프로그램 코드를 변경 하지 않고 응용 프로그램에 대 한 SSL 연결을 사용 합니다.

두 개의 컨테이너로 구성 된 컨테이너 그룹을 설정할 수 있습니다.
* 공용 Microsoft를 사용 하 여 간단한 웹 앱을 실행 하는 응용 프로그램 컨테이너 [aci helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 이미지입니다. 
* 공용을 실행 하는 사이드카 컨테이너 [Nginx](https://hub.docker.com/_/nginx) 이미지, SSL을 사용 하도록 구성 합니다. 

이 예제에서는 컨테이너 그룹을 노출 포트 443 Nginx에 대 한 공용 IP 주소를 사용 하 여 합니다. Nginx는 내부적으로 포트 80에서 수신 대기 하는 도우미 웹 앱에 HTTPS 요청을 라우팅합니다. 다른 포트에서 수신 대기 하는 컨테이너 앱에 대 한 예제를 조정할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 문서를 완료할 수 있습니다. 로컬로 사용하려면 2.0.55 버전 이상이 권장됩니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

Nginx를 SSL 공급자를 설정 하려면 SSL 인증서가 필요 합니다. 이 문서에는 만들기 및 자체 서명 된 SSL 인증서를 설정 하는 방법을 보여 줍니다. 프로덕션 시나리오에 대 한 인증 기관에서 인증서를 가져와야 합니다.

자체 서명 된 SSL 인증서를 만들려면 사용 합니다 [OpenSSL](https://www.openssl.org/) Azure Cloud Shell 및 많은 Linux 배포에서 사용할 수 있는 도구 또는 운영 체제에서 비교할 수 있는 클라이언트 도구를 사용 합니다.

먼저 로컬 작업 디렉터리에 인증서 요청 (.csr 파일)을 만듭니다.

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

식별 정보를 추가 하도록 지시 합니다. 일반 이름에 대 한 인증서에 연결 된 호스트 이름을 입력 합니다. 암호를 묻는 메시지가 나타나면 암호를 추가 하지 않으려면 입력 하지 않고 Enter 키를 누릅니다.

인증서 요청에서 자체 서명 된 인증서 (.crt 파일)를 만들려면 다음 명령을 실행 합니다. 예를 들면 다음과 같습니다.

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

이제 디렉터리에 3 개의 파일 표시: 인증서 요청 (`ssl.csr`), 개인 키 (`ssl.key`), 및 자체 서명 된 인증서 (`ssl.crt`). 사용할 `ssl.key` 고 `ssl.crt` 이후 단계에서 합니다.

## <a name="configure-nginx-to-use-ssl"></a>SSL을 사용 하도록 Nginx 구성

### <a name="create-nginx-configuration-file"></a>Nginx 구성 파일 만들기

이 섹션에서는 SSL을 사용 하도록 Nginx에 대 한 구성 파일을 만들 수 있습니다. 라는 새 파일에 다음 텍스트를 복사 하 여 시작`nginx.conf`합니다. Azure Cloud Shell에서 작업 디렉터리에 파일을 만들려면 Visual Studio Code를 사용할 수 있습니다.

```console
code nginx.conf
```

`location`를 설정 해야 `proxy_pass` 앱에 대 한 올바른 포트를 사용 하 여 합니다. 이 예제에서는 포트 80에 대 한 설정의 `aci-helloworld` 컨테이너입니다.

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
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

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

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64로 인코딩한 비밀 및 구성 파일

Base64로 인코딩할 Nginx 구성 파일, SSL 인증서 및 SSL 키입니다. 다음 섹션에서는 컨테이너 그룹을 배포 하는 데 사용 하는 YAML 파일에서 인코딩된 내용을 입력할 수 있습니다.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>컨테이너 그룹 배포

이제 컨테이너 그룹의 컨테이너 구성을 지정 하 여 배포를 [YAML 파일](container-instances-multi-container-yaml.md)합니다.

### <a name="create-yaml-file"></a>YAML 파일 만들기

라는 새 파일에 다음 YAML에 복사 `deploy-aci.yaml`합니다. Azure Cloud Shell에서 작업 디렉터리에 파일을 만들려면 Visual Studio Code를 사용할 수 있습니다.

```console
code deploy-aci.yaml
```

Base64로 인코딩된 내용을 입력 파일을 아래 표시 된 위치 `secret`합니다. 예를 들어 `cat` 각 base64로 인코딩된 파일의 내용을 확인 합니다. 배포 하는 동안 이러한 파일에 추가 되는 [비밀 볼륨](container-instances-volume-secret.md) 컨테이너 그룹에서입니다. 이 예제에서는 Nginx 컨테이너로 비밀 볼륨 탑재 됩니다.

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

사용 하 여 리소스 그룹을 만들어야 합니다 [az 그룹 만들기](/cli/azure/group#az-group-create) 명령:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

컨테이너 그룹 배포를 [az 컨테이너 만들기](/cli/azure/container#az-container-create) 명령인 YAML 파일을 인수로 전달 합니다.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>배포 상태 확인

배포의 상태를 보려면 다음을 사용 하 여 [az container show](/cli/azure/container#az-container-show) 명령:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

성공적인 배포의 경우 출력은 다음과 비슷합니다.

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>SSL 연결 확인

실행 중인 응용 프로그램을 보려면 브라우저에서 해당 IP 주소로 이동 합니다. 이 예제에 표시 된 IP 주소는 예를 들어 `52.157.22.76`합니다. 사용 해야 `https://<IP-ADDRESS>` Nginx 서버 구성으로 인해 실행 중인 응용 프로그램을 확인 합니다. 연결을 시도 `http://<IP-ADDRESS>` 실패 합니다.

![Azure 컨테이너 인스턴스에서 실행되는 애플리케이션을 보여주는 브라우저 스크린샷](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> 이 예제에서는 자체 서명 된 인증서를 사용 하 고 인증 기관에서 하지 하나를 사용 하므로 HTTPS를 통해 사이트에 연결할 때 브라우저 보안 경고를 표시 합니다. 이는 정상적인 동작입니다.
>

## <a name="next-steps"></a>다음 단계

이 문서에서는 Nginx 컨테이너를 컨테이너 그룹에서 실행 되는 웹 앱에 대 한 SSL 연결을 사용 하도록 설정 하는 방법을 보여 주었습니다. 포트 80 이외의 포트에서 수신 대기 하는 앱에 대 한이 예제를 조정할 수 있습니다. 또한 자동으로 포트 80 (HTTP) HTTPS를 사용 하도록 서버 연결을 리디렉션할 Nginx 구성 파일을 업데이트할 수 있습니다.

와 같은 다른 SSL 공급자 들 사이드카에서 Nginx를 사용 하는이 문서를 사용할 수 있습니다 [Caddy](https://caddyserver.com/)합니다.

컨테이너 그룹에서 SSL을 사용 하도록 설정 하는 다른 방법에서 그룹을 배포 하는 것을 [Azure 가상 네트워크](container-instances-vnet.md) 사용 하 여는 [Azure application gateway](../application-gateway/overview.md)합니다. 게이트웨이 SSL 끝점으로 설정할 수 있습니다. 예를 보려면 [배포 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) 게이트웨이에서 SSL 종료를 사용 하도록 조정할 수 있습니다.
