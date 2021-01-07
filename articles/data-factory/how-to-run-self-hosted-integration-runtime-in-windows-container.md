---
title: Windows 컨테이너에서 Self-Hosted Integration Runtime를 실행 하는 방법
description: Windows 컨테이너에서 Self-Hosted Integration Runtime를 실행 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 7035da173102e59aec9c643381bad701d6facf38
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634541"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Windows 컨테이너에서 Self-Hosted Integration Runtime를 실행 하는 방법

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Windows 컨테이너에서 Self-Hosted Integration Runtime를 실행 하는 방법을 설명 합니다.
Azure Data Factory은 Self-Hosted Integration Runtime의 공식 windows 컨테이너 지원을 제공 합니다. Docker 빌드 소스 코드를 다운로드 하 고 빌드 및 실행 중인 프로세스를 고유한 연속 배달 파이프라인에서 결합할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항 
- [Windows의 컨테이너 요구 사항](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker 버전 2.3 이상 
- Self-Hosted Integration Runtime 버전 4.11.7512.1 이상 
## <a name="get-started"></a>시작하기 
1.  Docker 설치 및 Windows 컨테이너 사용 
2.  https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container에서 소스 코드 다운로드
3.  ' SHIR ' 폴더에서 최신 버전 SHIR 다운로드 
4.  셸에서 폴더를 엽니다. 
```console
cd "yourFolderPath"
```

5.  Windows docker 이미지를 빌드합니다. 
```console
docker build . -t "yourDockerImageName" 
```
6.  Docker 컨테이너를 실행 합니다. 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY는이 명령에 대해 필수입니다. NODE_NAME, ENABLE_HA 및 HA_PORT는 선택 사항입니다. 이 값을 설정 하지 않으면 명령은 기본값을 사용 합니다. ENABLE_HA의 기본값은 false이 고 HA_PORT는 8060입니다.

## <a name="container-health-check"></a>컨테이너 상태 검사 
120 초 시작 기간이 지나면 상태 검사기는 30 초 마다 정기적으로 실행 됩니다. 이는 컨테이너 엔진에 IR 상태를 제공 합니다. 

## <a name="limitations"></a>제한 사항
현재 Windows 컨테이너에서 Self-Hosted Integration Runtime를 실행 하는 경우 아래 기능을 지원 하지 않습니다.
- HTTP 프록시 
- TLS/SSL 인증서를 사용 하 여 암호화 된 노드-노드 통신 
- 백업 생성 및 가져오기 
- 디먼 서비스 
- 자동 업데이트 

### <a name="next-steps"></a>다음 단계
- [Azure Data Factory의 통합 런타임 개념](./concepts-integration-runtime.md)을 검토합니다.
- [Azure Portal에서 자체 호스팅 통합 런타임을 만드는](./create-self-hosted-integration-runtime.md) 방법을 알아봅니다.