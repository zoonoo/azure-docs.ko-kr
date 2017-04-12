---
title: "Azure CLI 샘플 - App Service | Microsoft Docs"
description: "Azure CLI 샘플 - App Service"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5d74f8216edd46fcd36d2b7f8763ec8a69ccad42
ms.lasthandoff: 03/31/2017


---
# <a name="azure-cli-samples"></a>Azure CLI 샘플

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

| | |
|-|-|
|**앱 만들기**||
| [웹앱 만들기 및 GitHub에서 코드 배포](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Azure 웹앱을 만들고 공용 GitHub 리포지토리에서 코드를 배포합니다. |
| [GitHub의 연속 배포를 사용하여 웹앱 만들기](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| 사용자가 소유한 GitHub 리포지토리에서 연속 게시를 통해 Azure 웹앱을 만듭니다. |
| [웹앱 만들기 및 로컬 Git 리포지토리의 코드 배포](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Azure 웹앱을 만들고 로컬 Git 리포지토리의 코드 푸시를 구성합니다. |
| [웹앱 만들기 및 스테이징 환경에 코드 배포](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | 코드 변경 내용을 준비하기 위해 배포 슬롯을 사용하여 Azure 웹앱을 만듭니다. |
| [Docker 컨테이너에서 ASP.NET Core 웹앱 만들기](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Linux에서 Azure 웹앱을 만들고 Docker Hub의 Docker 이미지를 로드합니다. |
|**앱 구성**||
| [웹앱에 사용자 지정 도메인 매핑](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Azure 웹앱을 만들고 여기에 사용자 지정 도메인 이름을 매핑합니다. |
| [웹앱에 사용자 지정 SSL 인증서 바인딩](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Azure 웹앱을 만들고 여기에 사용자 지정 도메인 이름의 SSL 인증서를 바인딩합니다. |
|**앱 크기 조정**||
| [수동 웹앱 크기 조정](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Azure 웹앱을 만들고 2개의 인스턴스로 확장합니다. |
| [고가용성 아키텍처로 전 세계 웹앱 크기 조정](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 두 개의 지역에 두 개의 Azure 웹앱을 만들고 Azure Traffic Manager를 사용하여 단일 끝점을 통해 사용할 수 있습니다. |
|**리소스에 앱 연결**||
| [SQL Database에 웹앱 연결](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Azure 웹앱 및 SQL Database를 만들고 앱 설정에 데이터베이스 연결 문자열을 추가합니다. |
| [저장소 계정에 웹앱 연결](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Azure 웹앱 및 저장소 계정을 만들고 앱 설정에 저장소 연결 문자열을 추가합니다. |
| [Redis Cache에 웹앱 연결](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Azure 웹앱 및 Redis Cache를 만들고 앱 설정에 Redis 연결 세부 정보를 추가합니다. |
| [documentdb에 웹앱 연결](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Azure 웹앱 및 documentdb를 만든 다음 앱 설정에 documentdb 연결 세부 정보를 추가합니다. |
|**앱 모니터링**||
| [웹 서버 로그로 웹앱 모니터링](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Azure 웹앱을 만들고 로깅을 사용하도록 설정하고 로그를 로컬 컴퓨터에 다운로드합니다. |
| | |
