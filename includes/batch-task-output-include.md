---
title: 포함 파일
description: 포함 파일
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025600"
---
Azure Batch에서 실행 중인 태스크는 실행 시 출력 데이터를 생성할 수 있습니다. 작업의 다른 태스크, 작업을 실행한 클라이언트 애플리케이션 또는 둘 다에서 검색할 수 있도록 태스크 출력 데이터는 종종 저장할 필요가 있습니다. 태스크는 Batch 컴퓨팅 노드의 파일 시스템에 출력 데이터를 쓰지만, 노드 이미지를 다시 만들거나 노드에서 풀을 종료할 때 해당 노드의 모든 데이터가 손실됩니다. 또한 태스크에는 파일 보존 기간이 있을 수 있으며, 그 후에는 해당 태스크에서 만든 파일이 삭제됩니다. 이러한 이유로 나중에 [Azure Storage](../articles/storage/index.yml)와 같은 데이터 저장소에 필요한 태스크 출력을 유지하는 것이 중요합니다.

Batch의 스토리지 계정 옵션은 [Batch 계정 및 Azure Storage 계정](../articles/batch/accounts.md#azure-storage-accounts)을 참조하세요.