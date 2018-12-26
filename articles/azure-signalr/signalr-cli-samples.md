---
title: Azure CLI 샘플 - Azure SignalR Service
description: Azure CLI 샘플 - Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a39f0abf4f3a8cc30d6f8f83dcad7e9069f348e4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258783"
---
# <a name="azure-cli-samples"></a>Azure CLI 샘플

다음 표에는 Azure CLI를 사용하는 Azure SignalR Service용 bash 스크립트에 대한 링크가 포함되어 있습니다.

| | |
|-|-|
|**만들기**||
| [새 SignalR Service 및 리소스 그룹 만들기](scripts/signalr-cli-create-service.md) | 임의의 이름이 있는 새 리소스 그룹에 새 Azure SignalR Service 리소스를 만듭니다.  |
|**통합**||
| [SignalR을 사용하도록 구성된 새 SignalR Service 및 Web App 만들기](scripts/signalr-cli-create-with-app-service.md) | 임의의 이름이 있는 새 리소스 그룹에 새 Azure SignalR Service 리소스를 만듭니다. 또한 SignalR Service를 사용하는 ASP.NET Core Web App을 호스팅하기 위한 새 Web App 및 App Service 계획을 추가합니다. 웹앱은 새 SignalR Service 리소스에 연결하는 앱 설정으로 구성됩니다. |
| [SignalR 및 GitHub OAuth를 사용하도록 구성된 새 SignalR Service 및 Web App 만들기](scripts/signalr-cli-create-with-app-service-github-oauth.md) | 임의의 이름이 있는 새 리소스 그룹에 새 Azure SignalR Service 리소스를 만듭니다. 또한 SignalR Service를 사용하는 ASP.NET Core Web App을 호스팅하기 위한 새 Azure Web App 및 호스팅 계획을 추가합니다. 웹앱은 새 SignalR Service 리소스의 연결 문자열에 대한 앱 설정과 [인증 자습서](signalr-authenticate-oauth.md)에서 설명한 대로 [GitHub 인증](https://developer.github.com/v3/guides/basics-of-authentication/)을 지원하는 클라이언트 비밀로 구성됩니다. 웹앱은 로컬 git 리포지토리 배포 원본도 사용하도록 구성됩니다. |
| | |