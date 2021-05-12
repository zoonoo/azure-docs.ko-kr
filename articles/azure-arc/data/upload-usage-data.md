---
title: Azure Monitor에 사용량 현황 데이터 업로드
description: Azure Monitor에 Azure Arc 지원 데이터 서비스 사용량 현황 데이터 업로드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 0c72eda59f375c70274b17796ca53614ef95505b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669511"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Azure Monitor에 사용량 현황 데이터 업로드

사용량 정보는 주기적으로 내보낼 수 있습니다. 사용량 정보를 내보내고 업로드하면 Azure의 데이터 컨트롤러, SQL 관리형 인스턴스 및 PostgreSQL Hyperscale 서버 그룹 리소스가 생성되고 업데이트됩니다.

> [!NOTE] 
> 미리 보기 기간에는 Azure Arc 지원 데이터 서비스를 무료로 사용할 수 있습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Azure Arc 데이터 컨트롤러를 만든 후 24시간 이상 기다린 다음 사용량 현황 데이터를 업로드해야 합니다.

## <a name="create-service-principal-and-assign-roles"></a>서비스 주체 만들기 및 역할 할당

계속 진행하기 전에 필요한 서비스 주체를 만들고 적절한 역할에 할당했는지 확인합니다. 자세한 내용은 다음을 참조하세요.
* [서비스 주체 만들기](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [서비스 주체에게 역할 할당](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>사용량 데이터 업로드

인벤토리 및 리소스 사용량과 같은 사용량 정보는 다음 두 단계를 따라 Azure에 업로드할 수 있습니다.

1. 데이터 컨트롤러에 로그인합니다. 프롬프트에서 값을 입력합니다. 

   ```console
   azdata login
   ```

1. 다음과 같이 `azdata arc dc export` 명령을 사용하여 사용량 현황 데이터를 내보냅니다.

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   이 명령은 데이터 컨트롤러에 생성된 모든 Azure Arc 지원 데이터 리소스(예: SQL 관리형 인스턴스 및 PostgreSQL 하이퍼스케일 인스턴스 등)를 사용하여 `usage.json` 파일을 만듭니다.

2. ```azdata upload``` 명령을 사용하여 사용량 현황 데이터를 업로드합니다.

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>업로드 자동화(선택 사항)

일정에 따라 메트릭과 로그를 업로드하려는 경우 스크립트를 만들어 몇 분마다 타이머에서 실행되도록 할 수 있습니다. 다음은 Linux 셸 스크립트를 사용하여 업로드를 자동화하는 예제입니다.

자주 사용하는 텍스트/코드 편집기에서 다음 스크립트를 파일에 추가하고 `.sh`(Linux/Mac) 또는 `.cmd`, `.bat`, `.ps1`과 같은 스크립트 실행 파일로 저장합니다.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

스크립트 파일을 실행 가능하도록 설정합니다.

```console
chmod +x myuploadscript.sh
```

20분마다 스크립트를 실행합니다.

```console
watch -n 1200 ./myuploadscript.sh
```

Cron 또는 Windows 작업 스케줄러와 같은 작업 스케줄러를 사용하거나 Ansible, Puppet 또는 Chef와 같은 오케스트레이터를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Monitor에 메트릭 및 로그 업로드](upload-metrics.md)

[Azure Monitor에 로그 업로드](upload-logs.md)

[청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)
