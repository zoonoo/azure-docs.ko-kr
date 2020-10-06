---
title: '릴리스 정보: Azure Synapse Analytics(작업 영역 미리 보기)'
description: Azure Synapse Analytics(작업 영역 미리 보기)에 대한 릴리스 정보
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031673"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Azure Synapse Analytics(작업 영역 미리 보기) 릴리스 정보

이 문서에서는 Azure Synapse Analytics(작업 영역)의 제한 사항 및 문제에 대해 설명합니다. 관련 정보는 [Azure Synapse Analytics(작업 영역)란?](overview-what-is.md)을 참조하세요.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- 문제 및 고객에게 미치는 영향: SDK에서 만든 작업 영역은 Synapse Studio를 시작할 수 없습니다.

- 해결 방법: 다음 단계를 완료합니다. 
  1.    `az synapse workspace create`를 실행하여 작업 영역을 만듭니다.
  2.    `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`를 실행하여 관리 ID를 추출합니다.
  3.    ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`를 실행하여 스토리지 계정에 역할로 작업 영역을 추가합니다.
  4.    ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `를 실행하여 방화벽 규칙을 추가합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse란?](overview-what-is.md)
* [시작](get-started.md)
* [FAQ](overview-faq.md)
