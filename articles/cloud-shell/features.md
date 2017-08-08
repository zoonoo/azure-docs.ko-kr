---
title: "Azure Cloud Shell(미리 보기) 기능 | Microsoft Docs"
description: "Azure Cloud Shell의 기능 개요"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3e8eb5b2fe7d3d072132044e2445eab6fcd82ce1
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="features-and-tools-for-azure-cloud-shell"></a>Azure Cloud Shell에 대한 기능 및 도구
Azure Cloud Shell은 Azure 리소스를 관리 및 개발하기 위한 브라우저 기반 셸 환경입니다.

Cloud Shell은 컴퓨터의 직접 설치, 버전 관리 및 유지 관리에 대한 오버헤드 없이 Azure 리소스 관리를 위한 브라우저에서 액세스할 수 있는 미리 구성된 셸 경험을 제공합니다.

Cloud Shell은 요청별로 컴퓨터를 프로비전하므로 결과적으로 컴퓨터 상태는 세션 간에 유지되지 않습니다. Cloud Shell은 대화형 세션용으로 빌드되었기 때문에 셸은 셸 비활성 시간 20분 후 자동으로 종료됩니다.

## <a name="bash-in-cloud-shell"></a>Cloud Shell의 Bash
### <a name="tools"></a>도구
|Category   |이름   |
|---|---|
|Linux 셸 인터프리터|Bash<br> sh               |
|Azure 도구            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) 및 [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [배치 Shipyard](https://github.com/Azure/batch-shipyard)     |
|텍스트 편집기           |vim<br> nano<br> emacs       |
|소스 제어         |git                    |
|빌드 도구            |make<br> maven<br> npm<br> pip         |
|컨테이너             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [초안](https://github.com/Azure/draft)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|데이터베이스              |MySQL 클라이언트<br> PostgreSql 클라이언트<br> [sqlcmd 유틸리티](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|기타                  |iPython 클라이언트<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> |

### <a name="language-support"></a>언어 지원
|language   |버전   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|자바       |1.8        |
|Node.js    |6.9.4      |
|파이썬     |2.7 및 3.5(기본값)|

## <a name="secure-automatic-authentication"></a>안전한 자동 인증
Cloud Shell은 Azure CLI 2.0에 대한 계정 액세스를 안전하고 자동으로 인증합니다.

## <a name="azure-files-persistence"></a>Azure 파일 지속성
Cloud Shell은 임시 컴퓨터를 사용하여 요청별로 할당되므로 $Home 외부에 있는 파일 및 컴퓨터 상태는 세션 간에 유지되지 않습니다.
세션 간에 파일을 유지하기 위해 Cloud Shell에서는 처음 시작할 때 Azure 파일 공유 연결을 안내합니다.
작업이 완료되면 Cloud Shell은 이후의 모든 세션에 대해 저장소를 자동으로 연결합니다.

[Azure 파일 공유를 Cloud Shell에 연결하는 방법에 대해 자세히 알아봅니다.](persisting-shell-storage.md)

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작](quickstart.md) <br>
[Azure CLI 2.0에 대한 자세한 정보](https://docs.microsoft.com/cli/azure/) <br>
