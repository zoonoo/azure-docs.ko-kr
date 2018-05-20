---
title: Linux의 Azure App Service에 대한 SSH 지원 | Microsoft Docs
description: Linux의 Azure App Service에서 SSH 사용에 대해 자세히 알아봅니다.
keywords: azure app service, 웹앱, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: cf27e852f5ec9b7e12b0c678e9940596bc57b385
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Linux의 Azure App Service에 대한 SSH 지원

[SSH(보안 셸)](https://en.wikipedia.org/wiki/Secure_Shell)는 네트워크 서비스를 안전하게 사용하기 위한 암호화 네트워크 프로토콜입니다. 이 방법은 명령줄에서 원격으로 안전하게 컴퓨터에 로그인하고 원격으로 관리 명령을 실행하는 데 가장 일반적으로 사용됩니다.

Linux의 App Service는 새 웹앱의 런타임 스택에 사용되는 기본 제공 Docker 이미지가 있는 앱 컨테이너에 SSH 지원을 제공합니다.

![런타임 스택](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

또한 SSH 서버를 이미지의 일부로 포함하고 이 문서에 설명된 대로 구성하여 사용자 지정 Docker 이미지에서 SSH를 사용할 수도 있습니다.

> [!NOTE] 
> SSH, SFTP 또는 Visual Studio Code를 사용하여 로컬 개발 컴퓨터에서 직접 앱 컨테이너에 연결할 수도 있습니다(Node.js 앱 라이브 디버깅을 위해). 자세한 내용은 [Linux App Service의 원격 디버깅 및 SSH](https://aka.ms/linux-debug)를 참조하세요.
>

## <a name="making-a-client-connection"></a>클라이언트 연결 만들기

SSH 클라이언트 연결을 만들려면 주 사이트를 시작해야 합니다.

다음과 같은 형식을 사용하여 웹앱에 대한 SCM(소스 제어 관리) 끝점을 브라우저에 붙여 넣습니다.

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

아직 인증을 받지 못한 경우 연결하기 위해서는 Azure 구독에서 인증을 받아야 합니다.

![SSH 연결](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>사용자 지정 Docker 이미지를 사용한 SSH 지원

사용자 지정 Docker 이미지로 Azure Portal에서 컨테이너 및 클라이언트 간 SSH 통신을 지원하려면 Docker 이미지에 대해 다음 단계를 수행합니다.

이 단계는 Azure App Service 리포지토리에 [예제](https://github.com/Azure-App-Service/node/blob/master/6.9.3/)로 나와 있습니다.

1. 이미지에 대한 Dockerfile의 [`RUN` 명령](https://docs.docker.com/engine/reference/builder/#run)에 `openssh-server` 설치를 포함하고 루트 계정에 대한 암호를 `"Docker!"`로 설정합니다.

    > [!NOTE]
    > 이 구성을 사용하면 컨테이너에 대한 외부 연결이 허용되지 않습니다. SSH는 게시 자격 증명을 사용하여 인증되는 Kudu/SCM 사이트를 통해서만 액세스할 수 있습니다.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. [`COPY` 명령](https://docs.docker.com/engine/reference/builder/#copy)을 Dockerfile에 추가하여 [sshd_config](http://man.openbsd.org/sshd_config) 파일을 */etc/ssh/* 디렉터리에 복사합니다. 구성 파일은 Azure-App-Service GitHub 리포지토리([여기](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config) 참조)의 sshd_config 파일을 기준으로 해야 합니다.

    > [!NOTE]
    > *sshd_config* 파일에 다음이 포함되어야 하며 이러한 항목이 없으면 연결이 실패합니다. 
    > * `Ciphers`에는 다음 중 하나 이상이 포함되어야 합니다. `aes128-cbc,3des-cbc,aes256-cbc`
    > * `MACs`에는 다음 중 하나 이상이 포함되어야 합니다. `hmac-sha1,hmac-sha1-96`

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Dockerfile에 대한 [`EXPOSE` 명령](https://docs.docker.com/engine/reference/builder/#expose)에 포트 2222를 포함합니다. 루트 암호를 알고 있더라도 인터넷에서 포트 2222에 액세스할 수 없습니다. 개인 가상 네트워크의 브리지 네트워크 내에 있는 컨테이너에서만 액세스할 수 있는 내부 전용 포트입니다.

    ```docker
    EXPOSE 2222 80
    ```

1. 셸 스크립트를 사용하여 SSH 서비스를 시작해야 합니다([init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)의 예제 참조).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile은 [`ENTRYPOINT` 명령](https://docs.docker.com/engine/reference/builder/#entrypoint)을 사용하여 스크립트를 실행합니다.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="next-steps"></a>다음 단계

[Azure 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)에 질문 및 문제를 게시할 수 있습니다.

컨테이너용 웹앱에 대한 자세한 내용은 다음을 참조하세요.

* [Web App for Containers에 사용자 지정 Docker 이미지를 사용하는 방법](quickstart-docker-go.md)
* [Linux의 Azure App Service에서 .NET Core 사용](quickstart-dotnetcore.md)
* [Linux의 Azure App Service에서 Ruby 사용](quickstart-ruby.md)
* [Containers용 Azure App Service Web App 관련 FAQ](app-service-linux-faq.md)