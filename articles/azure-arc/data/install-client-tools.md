---
title: 클라이언트 도구 설치
description: Azure Data Studio에 대한 azdata, kubectl, Azure CLI, psql, Azure Data Studio(참가자) 및 Arc 확장을 설치합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9dcfb037c946394a157a9f81d5c09303e31cbefe
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108323612"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스 배포 및 관리를 위한 클라이언트 도구 설치

> [!IMPORTANT]
> 새로운 월간 릴리스로 업데이트하는 경우 최신 버전의 Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 도구 및 Azure Data Studio용 Azure Arc 확장으로도 업데이트해야 합니다.

이 문서에서는 클라이언트 컴퓨터에 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)], Azure Data Studio, Azure CLI(az) 및 Kubernetes CLI 도구(kubectl)를 설치하는 단계를 안내합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스를 만들고 관리하기 위한 도구 

다음 표에서는 Azure Arc 지원 데이터 서비스를 만들고 관리하는 데 필요한 일반적인 도구와 이러한 도구를 설치하는 방법을 보여 줍니다.

| 도구 | 필수 | Description | 설치 |
|---|---|---|---|
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] | 예 | 빅 데이터 클러스터를 설치하고 관리하기 위한 명령줄 도구입니다. [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]에는 `azdata sql query`(명령줄에서 단일 쿼리 실행), `azdata sql shell`(대화형 셸), `azdata postgres query` 및 `azdata postgres shell` 명령을 사용하여 Azure SQL 및 SQL Server 인스턴스와 Postgres 서버에 연결하고 쿼리하는 명령줄 유틸리티가 포함되어 있습니다. | [설치](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | 예 | Azure SQL, SQL Server, PostrgreSQL 및 MySQL을 비롯한 다양한 데이터베이스에 연결하고 쿼리하는 데 사용할 수 있는 풍부한 환경 도구입니다. Azure Data Studio에 대한 확장은 Azure Arc 지원 데이터 서비스에 대한 관리 환경을 제공합니다. | [설치](/sql/azure-data-studio/download-azure-data-studio) |
| Azure Data Studio용 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 확장 | Yes | 아직 설치하지 않은 경우 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]를 설치할 Azure Data Studio용 확장입니다.| Azure Data Studio의 확장 갤러리에서 설치합니다.|
| Azure Data Studio용 Azure Arc 확장 | Yes | Azure Data Studio에 대한 확장은 Azure Arc 지원 데이터 서비스에 대한 관리 환경을 제공합니다. Azure Data Studio용 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 확장에 대한 종속성이 있습니다. | Azure Data Studio의 확장 갤러리에서 설치합니다.|
| Azure Data Studio의 PostgreSQL 확장 | 아니요 | PostgreSQL에 대한 관리 기능을 제공하는 Azure Data Studio용 PostgreSQL 확장입니다. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Azure Data Studio의 확장 갤러리에서 설치합니다.|
| Azure CLI(az)<sup>1</sup> | Yes | Azure 서비스를 관리하기 위한 최신 명령줄 인터페이스입니다. AKS 배포와 함께 사용되며 Azure Arc 지원 데이터 서비스 인벤토리 및 청구 데이터를 Azure에 업로드합니다. ([추가 정보](/cli/azure/)) | [설치](/cli/azure/install-azure-cli) |
| Kubernetes CLI(kubectl)<sup>2</sup> | Yes | Kubernetes 클러스터 관리를 위한 명령줄 도구([추가 정보](https://kubernetes.io/docs/tasks/tools/install-kubectl/)) | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | 일부 샘플 스크립트에 필요합니다. | URL을 사용하여 데이터를 전송하기 위한 명령줄 도구입니다. | [Windows](https://curl.haxx.se/windows/) \| Linux: curl 패키지 설치 |
| oc | Red Hat OpenShift 및 Azure Redhat OpenShift 배포에 필요합니다. |`oc`는 OpenShift CLI(명령줄 인터페이스)입니다. | [CLI 설치](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> Azure CLI 버전 2.0.4 이상을 사용해야 합니다. 필요한 경우 `az --version`을 실행하여 버전을 찾습니다.

<sup>2</sup>`kubectl` 버전 1.13 이상을 사용해야 합니다. 또한 `kubectl` 버전은 Kubernetes 클러스터의 바로 이전 또는 이후 부 버전이어야 합니다. `kubectl` 클라이언트에서 특정 버전을 설치하려는 경우 [Install `kubectl` binary via curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl)(curl을 통해 kubectl 이진 설치)를 참조하세요(Windows 10에서는 Windows PowerShell이 아닌 cmd.exe를 사용하여 curl 실행).

<sup>3</sup> PowerShell을 사용하는 경우 curl은 Invoke-WebRequest cmdlet의 별칭입니다.

## <a name="next-steps"></a>다음 단계

[Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)
