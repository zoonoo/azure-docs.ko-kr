---
title: "Azure Cloud Shell의 Bash(미리 보기) 기능 | Microsoft Docs"
description: "Azure Cloud Shell의 Bash 기능의 개요"
services: Azure
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
ms.date: 09/25/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 158b0b65ba53630050275c910f2a7acfc39b8759
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Azure Cloud Shell의 Bash 기능 및 도구

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> 또한 [PowerShell](features-powershell.md)에 대한 기능 및 도구를 사용할 수 있습니다.

Azure Cloud Shell의 Bash는 `Ubuntu 16.04 LTS`에서 실행됩니다.

## <a name="features"></a>기능

### <a name="secure-automatic-authentication"></a>안전한 자동 인증

Azure Cloud Shell의 Bash는 Azure CLI 2.0에 대한 계정 액세스를 안전하고 자동으로 인증합니다.

### <a name="ssh-into-azure-linux-virtual-machines"></a>Azure Linux 가상 컴퓨터에 SSH

Azure CLI 2.0에서 Linux VM을 만들면 기본 SSH 키를 만들고 `$Home` 디렉터리에 배치할 수 있습니다. SSH 키를 `$Home`에 배치하면 Azure Cloud Shell에서 Azure Linux VM에 직접 SSH 연결을 할 수 있습니다. 키는 파일 공유에서 acc_<user>.img에 보관되어, 파일 공유나 키에 대한 액세스를 사용하거나 공유할 때 모범 사례를 활용합니다.

### <a name="home-persistence-across-sessions"></a>여러 세션에 걸친 $Home 지속성

세션 간에 파일을 유지하기 위해 Azure Cloud Shell에서는 처음 시작할 때 Azure 파일 공유 연결을 안내합니다.
작업이 완료되면 Azure Cloud Shell은 이후의 모든 세션에 대해 저장소(`$Home\clouddrive`로 장착됨)를 자동으로 연결합니다.
또한 Azure Cloud Shell의 Bash에서 `$Home` 디렉터리는 Azure 파일 공유에서 .img로 유지됩니다.
`$Home` 외부에 있는 파일 및 컴퓨터 상태는 세션 간에 지속 되지 않습니다.

[Azure Cloud Shell에서 Bash의 보관 파일에 대해 자세히 알아보기](persisting-shell-storage.md)

## <a name="tools"></a>도구

|Category   |이름   |
|---|---|
|Linux 셸 인터프리터|Bash<br> sh               |
|Azure 도구            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) 및 [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [배치 Shipyard](https://github.com/Azure/batch-shipyard) <br>    |
|텍스트 편집기           |vim<br> nano<br> emacs       |
|소스 제어         |git                    |
|빌드 도구            |make<br> maven<br> npm<br> pip         |
|컨테이너             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|데이터베이스              |MySQL 클라이언트<br> PostgreSql 클라이언트<br> [sqlcmd 유틸리티](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|기타                  |iPython 클라이언트<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> |

## <a name="language-support"></a>언어 지원

|언어   |버전   |
|---|---|
|.NET       |1.01       |
|이동         |1.7        |
|자바       |1.8        |
|Node.js    |6.9.4      |
|PowerShell  |[6.0(베타)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 및 3.5(기본값)|

## <a name="next-steps"></a>다음 단계
[Azure Cloud Shell의 Bash 빠른 시작](quickstart.md) <br>
[Azure CLI 2.0에 대한 자세한 정보](https://docs.microsoft.com/cli/azure/)

