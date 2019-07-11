---
title: 자습서 - Azure에서 Linux 가상 머신에 LEMP 배포 | Microsoft Docs
description: 이 자습서에서는 Azure에서 Linux 가상 머신에 LEMP 스택을 설치하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 15d88e082f9ab0838f4a560d89801edd9d46d682
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703535"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>자습서: Azure에서 Linux 가상 머신에 LEMP 웹 서버 설치

이 문서에서는 Azure의 Ubuntu VM에 NGINX 웹 서버, MySQL 및 PHP(LEMP 스택)를 배포하는 방법을 설명합니다. LEMP 스택은 인기 있는 [LAMP 스택](tutorial-lamp-stack.md) 대신 사용할 수 있으며 Azure에도 설치할 수 있습니다. 작동 중인 LEMP 서버를 보려면 필요에 따라 WordPress 사이트를 설치하고 구성할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Ubuntu VM(LEMP 스택에서 'L') 만들기
> * 웹 트래픽에 대해 포트 80 열기
> * NGINX, MySQL 및 PHP 설치
> * 설치 및 구성 확인
> * LEMP 서버에 WordPress 설치

이 설치는 빠른 테스트 또는 개념 증명을 위한 것입니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>NGINX, MySQL 및 PHP 설치

다음 명령을 실행하여 Ubuntu 패키지 원본을 업데이트하고 NGINX, MySQL 및 PHP를 설치합니다. 

```bash
sudo apt update && sudo apt install nginx && sudo apt install mysql-server php-mysql php-fpm
```

패키지 및 기타 종속성을 설치하라는 메시지가 표시됩니다. 이 프로세스에서는 PHP와 MySQL을 함께 사용하는 데 필요한 최소한의 PHP 확장을 설치합니다.  

## <a name="verify-installation-and-configuration"></a>설치 및 구성 확인


### <a name="verify-nginx"></a>NGINX 확인

다음 명령으로 PHP의 버전을 확인합니다.
```bash
nginx -v
```

NGINX를 설치하고 VM에 포트 80을 열어서 인터넷에서 웹 서버에 액세스할 수 있습니다. NGINX 시작 페이지를 보려면 웹 브라우저를 열고 VM의 공용 IP 주소를 입력합니다. VM에 SSH하는 데 사용한 공용 IP 주소를 사용합니다.

![NGINX 기본 페이지][3]


### <a name="verify-and-secure-mysql"></a>MySQL 확인 및 보호

다음 명령을 사용하여 MySQL의 버전을 확인합니다(대문자 `V` 매개 변수 주의).

```bash
mysql -V
```

루트 암호 설정을 포함하여 MySQL 설치를 보호하려면 `mysql_secure_installation` 스크립트를 실행합니다. 

```bash
sudo mysql_secure_installation
```

필요에 따라 암호 유효성 검사 플러그인을 설치할 수 있습니다(권장). 그런 다음, MySQL 루트 사용자의 암호를 설정하고 사용자 환경에 대한 나머지 보안 설정을 구성합니다. 모든 질문에 "Y"(예)라고 대답하는 것이 좋습니다.

MySQL 기능(MySQL 데이터베이스를 만들거나, 사용자를 추가하거나 구성 설정을 변경함)을 시도해 보려면 MySQL에 로그인합니다. 이 단계는 이 자습서를 완료하는 데 필수는 아닙니다. 


```bash
sudo mysql -u root -p
```

완료한 후 `\q`를 입력하여 mysql 프롬프트를 종료합니다.

### <a name="verify-php"></a>PHP 확인

다음 명령으로 PHP의 버전을 확인합니다.

```bash
php -v
```

PHP-FPM(PHP FastCGI Process Manager)를 사용하도록 NGINX를 구성합니다. 다음 명령을 실행하여 원래 NGINX 서버 블록 구성 파일을 백업하고 원하는 편집기에 원본 파일을 편집합니다.

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

편집기에서 `/etc/nginx/sites-available/default`의 내용을 다음으로 바꿉니다. 설정에 대한 설명을 보려면 주석을 참조하세요. VM의 공용 IP 주소를 *yourPublicIPAddress*로 바꾸고 `fastcgi_pass`에서 PHP 버전을 확인하고 나머지 설정은 그대로 유지합니다. 그런 다음 파일을 저장합니다.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
}
```

NGINX 구성의 구문 오류를 확인합니다.

```bash
sudo nginx -t
```

구문이 올바른 경우 다음 명령을 사용하여 NGINX를 다시 시작합니다.

```bash
sudo service nginx restart
```

추가로 테스트하려는 경우 빠른 PHP 정보 페이지를 만들어 브라우저에서 봅니다. 다음 명령은 PHP 정보 페이지를 만듭니다.

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



이제 만든 PHP 정보 페이지를 확인할 수 있습니다. 웹 브라우저를 열고 `http://yourPublicIPAddress/info.php`로 이동합니다. VM의 공용 IP 주소를 대체합니다. 이 이미지와 유사하게 표시됩니다.

![PHP 정보 페이지][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure에서 LEMP 서버를 배포했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Ubuntu VM 만들기
> * 웹 트래픽에 대해 포트 80 열기
> * NGINX, MySQL 및 PHP 설치
> * 설치 및 구성 확인
> * LEMP 스택에 WordPress 설치

SSL 인증서로 웹 서버를 보호하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [SSL로 웹 서버 보안](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
