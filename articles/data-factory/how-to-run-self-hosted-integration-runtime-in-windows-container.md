---
title: Windows 컨테이너에서 자체 호스팅 통합 런타임을 실행하는 방법
description: Windows 컨테이너에서 자체 호스팅 통합 런타임을 실행하는 방법을 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 0c1452368af6e3bd3083b3b7ecf505d2d911b6b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642425"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Windows 컨테이너에서 자체 호스팅 통합 런타임을 실행하는 방법

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Windows 컨테이너에서 자체 호스팅 통합 런타임을 실행하는 방법을 설명합니다.
Azure Data Factory는 자체 호스팅 통합 런타임의 공식 Windows 컨테이너 지원을 제공합니다. Docker 빌드 소스 코드를 다운로드하고 자체 지속적인 업데이트 파이프라인에서 빌드 및 실행 프로세스를 결합할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건 
- [Windows의 컨테이너 요구 사항](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker 버전 2.3 이상 
- 자체 호스팅 통합 런타임 버전 5.2.7713.1 이상 
## <a name="get-started"></a>시작 
1.  Docker를 설치하고 Windows 컨테이너를 사용하도록 설정합니다. 
2.  https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container에서 소스 코드 다운로드
3.  'SHIR' 폴더에서 최신 버전 SHIR을 다운로드합니다. 
4.  셸에서 폴더를 엽니다. 
```console
cd "yourFolderPath"
```

5.  Windows Docker 이미지를 빌드합니다. 
```console
docker build . -t "yourDockerImageName" 
```
6.  Docker 컨테이너를 실행합니다. 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true -e HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY는 이 명령에서 필수입니다. NODE_NAME, ENABLE_HA 및 HA_PORT는 선택 사항입니다. 값을 설정하지 않으면 명령은 기본값을 사용합니다. ENABLE_HA는 기본값이 false이고 HA_PORT는 8060입니다.

## <a name="container-health-check"></a>컨테이너 상태 확인 
120초 시작 기간이 경과하면 상태 검사기가 30초마다 실행되어 컨테이너 엔진에 IR 상태를 제공합니다. 

## <a name="limitations"></a>제한 사항
현재 Windows 컨테이너에서 자체 호스팅 통합 런타임을 실행하는 경우 아래 기능을 지원하지 않습니다.
- HTTP 프록시 
- TLS/SSL 인증서를 사용하는 암호화된 노드 간 통신 
- 백업 생성 및 가져오기 
- 디먼 서비스 
- 자동 업데이트 

### <a name="next-steps"></a>다음 단계
- [Azure Data Factory의 통합 런타임 개념](./concepts-integration-runtime.md)을 검토합니다.
- [Azure Portal에서 자체 호스팅 통합 런타임을 만드는](./create-self-hosted-integration-runtime.md) 방법을 알아봅니다.