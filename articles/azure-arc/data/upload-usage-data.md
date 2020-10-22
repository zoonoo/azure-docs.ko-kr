---
title: Azure Monitor에 사용 현황 데이터 업로드
description: 사용 중인 Azure Arc 사용 데이터 서비스 데이터를 Azure Monitor 업로드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 578f0d1ca742fe4445b8aeed6876d1a73fd3f79e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378121"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Azure Monitor에 사용 현황 데이터 업로드

정기적으로 사용 정보를 내보낼 수 있습니다. 이 정보의 내보내기 및 업로드는 Azure에서 데이터 컨트롤러, SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 서버 그룹 리소스를 만들고 업데이트 합니다.

> [!NOTE] 
> 미리 보기 기간 중에는 Azure Arc 사용 데이터 서비스를 사용 하는 비용이 없습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> 사용 데이터를 업로드 하기 전에 Azure Arc 데이터 컨트롤러를 만든 후 24 시간 이상 기다립니다.

## <a name="create-service-principal-and-assign-roles"></a>서비스 사용자 만들기 및 역할 할당

계속 하기 전에 필요한 서비스 주체를 만들고 적절 한 역할에 할당 했는지 확인 합니다. 자세한 내용은 다음을 참조하세요.
* [서비스 주체를 만듭니다](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [서비스 사용자에 게 역할 할당](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>사용 현황 데이터 업로드

인벤토리 및 리소스 사용량과 같은 사용 정보는 다음과 같은 두 단계를 통해 Azure에 업로드할 수 있습니다.

1. 데이터 컨트롤러에 로그인 합니다. 프롬프트에서 값을 입력 합니다. 

   ```console
   azdata login
   ```

1. 다음과 같이 명령을 사용 하 여 사용 현황 데이터를 내보냅니다 `azdata arc dc export` .

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   이 명령은 `usage.json` 데이터 컨트롤러에 생성 된 모든 Azure Arc 사용 가능한 데이터 리소스 (예: SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 인스턴스 등)를 사용 하 여 파일을 만듭니다.

2. 명령을 사용 하 여 사용 현황 데이터 업로드 ```azdata upload```

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>업로드 자동화 (선택 사항)

일정에 따라 메트릭과 로그를 업로드 하려는 경우 몇 분 마다 스크립트를 만들어 타이머에서 실행할 수 있습니다. 다음은 Linux shell 스크립트를 사용 하 여 업로드를 자동화 하는 예제입니다.

자주 사용 하는 텍스트/코드 편집기에서 다음 스크립트를 파일에 추가 하 고 스크립트 실행 파일 (예: sh (Linux/Mac) 또는 .cmd, .bat, ps1)로 저장 합니다.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

스크립트 파일 실행 파일 만들기

```console
chmod +x myuploadscript.sh
```

20 분 마다 스크립트를 실행 합니다.

```console
watch -n 1200 ./myuploadscript.sh
```

Cron 또는 Windows 작업 스케줄러와 같은 작업 스케줄러 또는 Ansible, 퍼핏 또는 Chef와 같은 orchestrator를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[메트릭 및 로그 업로드 Azure Monitor](upload-metrics.md)

[Azure Monitor에 로그 업로드](upload-logs.md)

[청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)
