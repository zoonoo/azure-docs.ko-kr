---
title: Docker에 자체 호스팅 Azure API 관리 게이트웨이 배포 | 마이크로 소프트 문서
description: Docker에 자체 호스팅 Azure API 관리 게이트웨이를 배포하는 방법 알아보기
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768506"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Azure API 관리 자체 호스팅 게이트웨이를 Docker에 배포

이 문서에서는 자체 호스팅 Azure API Management 게이트웨이를 Docker 환경에 배포하는 단계를 제공합니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능이 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 추가 비용 없이 개발자 및 프리미엄 계층에서만 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
- Docker 환경을 만듭니다. [데스크톱용 Docker는](https://www.docker.com/products/docker-desktop) 개발 및 평가 목적으로 사용할 수 있는 좋은 옵션입니다. 모든 Docker 버전, 해당 기능 및 Docker 자체에 대한 포괄적인 설명서에 대한 자세한 내용은 [Docker](https://docs.docker.com) 설명서를 참조하십시오.
- [API 관리 인스턴스에서 게이트웨이 리소스 프로비전](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> 자체 호스팅 게이트웨이는 x86-64 Linux 기반 Docker 컨테이너로 패키지됩니다.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Docker에 자체 호스팅 게이트웨이 배포

1. **설정**에서 **게이트웨이를 선택합니다.**
2. 배포할 게이트웨이 리소스를 선택합니다.
3. **배포를**선택합니다.
4. **토큰** 텍스트 상자의 새 토큰은 기본 **만료** 및 **비밀 키** 값을 사용하여 자동으로 생성되었습니다. 원하는 경우 둘 중 하나 또는 둘 다 조정하고 **생성을** 선택하여 새 토큰을 만듭니다.
4. 배포 스크립트에서 **Docker를** 선택했는지 **확인합니다.**
5. 환경을 클릭하여 파일을 다운로드하려면 **환경** 옆에 **있는 env.conf** 파일 링크를 선택합니다.
6. **실행** 텍스트 상자의 오른쪽 끝에 있는 **복사** 아이콘을 선택하여 Docker 명령을 클립보드에 저장합니다.
7. 명령을 터미널(또는 명령) 창에 붙여넣습니다. 필요에 따라 포트 매핑 및 컨테이너 이름을 조정합니다. 명령은 다운로드한 환경 파일이 현재 디렉터리에 있을 것으로 예상합니다.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. 명령을 실행합니다. 이 명령은 Microsoft 컨테이너 레지스트리에서 다운로드한 자체 호스팅 게이트웨이 이미지를 사용하여 컨테이너를 실행하고 컨테이너의 HTTP(8080) 및 HTTPS(8081) 포트를 호스트의 포트 80 및 443에 매핑하도록 Docker 환경을 지시합니다.
9. 아래 명령을 실행하여 게이트웨이 포드가 실행되고 있는지 확인합니다.
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Azure 포털로 돌아가방금 배포한 게이트웨이 노드가 정상 상태를 보고하고 있는지 확인합니다.

![게이트웨이 상태](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> 명령을 <code>console docker container logs <gateway-name></code> 사용하여 자체 호스팅 게이트웨이 로그의 스냅숏을 봅니다.
>
> 명령을 <code>docker container logs --help</code> 사용하여 모든 로그 보기 옵션을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대한 자세한 내용은 [Azure API Management 자체 호스팅 게이트웨이 개요를](self-hosted-gateway-overview.md)참조하십시오.
* [자체 호스팅 게이트웨이에 대한 사용자 지정 도메인 이름을 구성합니다.](api-management-howto-configure-custom-domain-gateway.md)
