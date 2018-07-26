---
title: Azure Cloud Shell 기능 | Microsoft Docs
description: Azure Cloud Shell의 Bash 기능의 개요
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: juluk
ms.openlocfilehash: 09c3ca23aafc8519b9e3ad57d030f066bb153e26
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056194"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell에 대한 기능 및 도구

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell은 `Ubuntu 16.04 LTS`에서 실행됩니다.

## <a name="features"></a>기능

### <a name="secure-automatic-authentication"></a>안전한 자동 인증

Cloud Shell은 Azure CLI 2.0 및 Azure PowerShell에 대한 계정 액세스를 안전하게 자동으로 인증합니다.

### <a name="home-persistence-across-sessions"></a>여러 세션에 걸친 $Home 지속성

세션 간에 파일을 유지하기 위해 Cloud Shell에서는 처음 시작할 때 Azure 파일 공유 연결을 안내합니다.
작업이 완료되면 Azure Cloud Shell은 이후의 모든 세션에 대해 저장소(`$Home\clouddrive`로 장착됨)를 자동으로 연결합니다.
또한 `$Home` 디렉터리는 Azure 파일 공유에서 .img로 유지됩니다.
`$Home` 외부에 있는 파일 및 컴퓨터 상태는 세션 간에 지속 되지 않습니다. SSH 키와 같은 비밀을 저장하는 경우 모범 사례를 사용합니다. [Azure Key Vault와 같은 서비스에는 설치에 대한 자습서가 있습니다](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Cloud Shell에서 파일을 유지하는 방법에 대해 자세히 알아보기](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure 드라이브(Azure:)

Azure Cloud Shell의 PowerShell(미리 보기)은 Azure 드라이브(`Azure:`)에서 시작됩니다.
Azure 드라이브는 파일 시스템 탐색과 비슷하게 Compute, 네트워크, Storage 등과 같은 Azure 리소스를 쉽게 검색 및 탐색할 수 있습니다.
사용하는 드라이브에 관계 없이 이러한 리소스를 관리하기 위해 친숙한 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure)을 계속 사용할 수 있습니다.
Azure Portal에서 또는 Azure PowerShell cmdlet을 통해 직접 만들어진 Azure 리소스에 대한 변경 내용은 Azure 드라이브에 반영됩니다.  `dir -Force`를 실행하여 리소스를 새로 고칠 수 있습니다.

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>오픈 소스 도구와 완벽한 통합

Cloud Shell은 Terraform, Ansible, Chef InSpec 같은 오픈 소스 도구를 위해 사전 구성된 인증을 포함하고 있습니다. 예제 연습에서 사용해 보십시오.

## <a name="tools"></a>도구

|Category   |Name   |
|---|---|
|Linux 도구            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Azure 도구            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) 및 [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) |
|텍스트 편집기           |vim<br> nano<br> emacs       |
|소스 제어         |git                    |
|빌드 도구            |make<br> maven<br> npm<br> pip         |
|컨테이너             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|데이터베이스              |MySQL 클라이언트<br> PostgreSql 클라이언트<br> [sqlcmd 유틸리티](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|기타                  |iPython 클라이언트<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)| 

## <a name="language-support"></a>언어 지원

|언어   |버전   |
|---|---|
|.NET Core  |2.0.0       |
|Go         |1.9        |
|자바       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.0.2](https://github.com/PowerShell/powershell/releases)       |
|파이썬     |2.7 및 3.5(기본값)|

## <a name="next-steps"></a>다음 단계
[Azure Cloud Shell의 Bash 빠른 시작](quickstart.md) <br>
[Cloud Shell의 PowerShell(미리 보기) 빠른 시작](quickstart-powershell.md) <br>
[Azure CLI 2.0에 대한 자세한 정보](https://docs.microsoft.com/cli/azure/) <br>
[Microsoft Azure PowerShell에 관해 알아보기](https://docs.microsoft.com/powershell/azure/) <br>
