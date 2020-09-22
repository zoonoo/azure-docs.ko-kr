---
title: 오프 라인 배포
description: 오프 라인 배포를 사용 하면 Microsoft Container Registry에서 끌어오는 대신 개인 컨테이너 레지스트리에서 컨테이너 이미지를 끌어올 수 있습니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 575903654a165bef0d09ac6abf0793af3f6784e8
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940612"
---
# <a name="offline-deployment-overview"></a>오프 라인 배포 개요

일반적으로 Azure Arc 데이터 컨트롤러, SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 서버 그룹을 만드는 데 사용 되는 컨테이너 이미지는 MCR (Microsoft Container Registry)에서 직접 가져옵니다. 어떤 경우에는 배포 하는 환경이 Microsoft Container Registry에 연결 되지 않습니다.  이와 같은 상황에서는 Microsoft Container Registry에 액세스할 _수 있는 컴퓨터_ 를 사용 하 여 컨테이너 이미지를 가져온 다음 Azure Arc 사용 가능 데이터 서비스를 배포할 환경에서 연결할 _수 있는 개인_ 컨테이너 레지스트리에 태그를 지정할 수 있습니다.

Azure Arc 사용 데이터 서비스에 대 한 월별 업데이트를 제공 하 고 많은 수의 컨테이너 이미지가 있으므로 스크립트를 사용 하 여 컨테이너 이미지를 풀링 하 고 태그를 지정 하 고 개인 컨테이너 레지스트리에 푸시하는이 프로세스를 수행 하는 것이 가장 좋습니다.  스크립트를 자동화 하거나 수동으로 실행할 수 있습니다.

[샘플 스크립트](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) 는 Azure Arc GitHub 리포지토리에서 찾을 수 있습니다.

> [!NOTE]
> 이 스크립트를 사용 하려면 python 및 [DOCKER CLI](https://docs.docker.com/install/)를 설치 해야 합니다.

이 스크립트는 다음 정보를 대화형으로 표시 합니다.  또는 대화형 프롬프트 없이 스크립트를 실행 하려는 경우 스크립트를 실행 하기 전에 해당 환경 변수를 설정할 수 있습니다.

|prompt|환경 변수|참고|
|---|---|---|
|소스 컨테이너 레지스트리 제공-를 사용 하려면 ENTER 키를 누릅니다. `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|일반적으로 Microsoft Container Registry에서 이미지를 끌어올 수 있지만 다른 레지스트리를 사용 하 여 비공개 미리 보기에 참여 하는 경우에는 미리 보기 프로그램의 일부로 제공 된 정보를 사용할 수 있습니다.|
|소스 컨테이너 레지스트리 리포지토리 제공-를 사용 하려면 ENTER 키를 누릅니다 `arcdata` .|SOURCE_DOCKER_REPOSITORY|Microsoft Container Registry에서 끌어오는 경우 리포지토리는이 됩니다 `arcdata` .|
|소스 컨테이너 레지스트리에 대 한 사용자 이름 제공-없음을 사용 하려면 ENTER 키를 누릅니다.|SOURCE_DOCKER_USERNAME|로그인이 필요한 원본에서 컨테이너 이미지를 끌어오는 경우에만 값을 제공 합니다.  Microsoft Container Registry에는 로그인이 필요 하지 않습니다.|
|소스 컨테이너 레지스트리에 대 한 암호 제공-없음을 사용 하려면 ENTER 키를 누릅니다.|SOURCE_DOCKER_PASSWORD|로그인이 필요한 원본에서 컨테이너 이미지를 끌어오는 경우에만 값을 제공 합니다.  Microsoft Container Registry에는 로그인이 필요 하지 않습니다. 마스킹된 암호 프롬프트입니다.  입력 하거나 붙여 넣는 경우에는 암호를 볼 수 없습니다.|
|원본에서 이미지에 대 한 컨테이너 이미지 태그를 제공 합니다. ' '를 사용 하려면 enter 키를 누릅니다 `<current monthly release tag>` .|SOURCE_DOCKER_TAG|기본 태그 이름은 Microsoft Container Registry에서 현재 릴리스의 월과 연도를 반영 하도록 매월 업데이트 됩니다.|
|대상 컨테이너 레지스트리 DNS 이름 또는 IP 주소를 제공 합니다.|TARGET_DOCKER_REGISTRY|대상 레지스트리 DNS 이름 또는 IP 주소입니다.  이미지가 _푸시되는 레지스트리입니다._|
|대상 컨테이너 레지스트리 리포지토리를 제공 합니다.|TARGET_DOCKER_REPOSITORY|이미지를 푸시할 대상 레지스트리의 리포지토리입니다.|
|대상 컨테이너 레지스트리에 대 한 사용자 이름 제공-없음을 사용 하려면 enter 키를 누릅니다.|TARGET_DOCKER_USERNAME|대상 컨테이너 레지스트리에 로그인 하는 데 사용 되는 사용자 이름 (있는 경우)입니다.|
|대상 컨테이너 레지스트리에 대 한 암호 제공-없음을 사용 하려면 enter 키를 누릅니다.|TARGET_DOCKER_PASSWORD|대상 컨테이너 레지스트리에 로그인 하는 데 사용 되는 암호 (있는 경우)입니다. 마스킹된 암호 프롬프트입니다.  입력 하거나 붙여 넣는 경우에는 암호를 볼 수 없습니다.|
|대상의 이미지에 대 한 컨테이너 이미지 태그를 제공 합니다.|TARGET_DOCKER_TAG|일반적으로 혼동을 피하기 위해 원본과 동일한 태그를 사용 합니다.|