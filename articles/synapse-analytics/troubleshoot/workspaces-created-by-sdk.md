---
title: '문제 해결: SDK로 생성 된 작업 영역은 Synapse Studio를 시작할 수 없습니다.'
description: SDK에서 만든 작업 영역을 확인 하는 단계 Synapse Studio를 시작할 수 없습니다.
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466947"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>SDK를 사용 하 여 만든 Azure Synapse Analytics 작업 영역 문제 해결

이 문서에서는 SDK (software development kit)를 사용 하 여 만든 Synapse 작업 영역에서 Synapse Studio를 시작 하기 위한 문제 해결 단계를 제공 합니다.


## <a name="prerequisites"></a>필수 구성 요소

- SDK를 사용 하 여 만든 Synapse 작업 영역

## <a name="workaround"></a>해결 방법

SDK에서 만든 작업 영역에서 Synapse Studio를 시작 하려면 다음 단계를 완료 합니다. 
  1.    `az synapse workspace create`를 실행하여 작업 영역을 만듭니다.
  2.    `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`를 실행하여 관리 ID를 추출합니다.
  3.    을 실행 하 여 관리 되는 id 저장소 계정에 저장소 Blob 데이터 참가자 역할을 부여 합니다. ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `를 실행하여 방화벽 규칙을 추가합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse란?](../overview-what-is.md)
* [시작](../get-started.md)
