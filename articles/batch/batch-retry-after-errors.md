---
title: 오류가 발생 한 후 작업 다시 시도
description: 오류를 확인 하 고 다시 시도 하세요.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: eda567fda13d6caca679d0ce4947e042eca9530d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652010"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Batch 서비스 오류 검색 및 처리

REST 서비스 API를 사용 하는 경우 오류를 확인 해야 합니다. 일괄 작업을 실행할 때 오류가 발생 하는 것은 일반적이 지 않습니다.

## <a name="common-errors"></a>일반 오류 

- 네트워킹 오류-일괄 처리에 도달 하지 않았거나 일괄 응답이 클라이언트에 도달 하지 못한 요청입니다.
- 내부 서버 오류-표준 5xx 상태 코드 HTTP 응답입니다.
- 제한을 사용 하면 429 또는 503 상태 코드 HTTP 응답과 같은 오류가 다시 시도 헤더와 함께 발생할 수 있습니다.
- 그룹이 이미 있습니다 및 InvalidOperation와 같은 오류를 포함 하는 4xx 오류 이는 리소스가 상태 전환에 대 한 올바른 상태가 아님을 의미 합니다.

## <a name="when-to-retry"></a>다시 시도 하는 경우

일괄 처리 Api는 오류가 발생 하는 경우 사용자에 게 알립니다. 모두 다시 시도할 수 있으며, 모두 해당 목적을 위한 전역 다시 시도 처리기를 포함 합니다. 이 기본 제공 메커니즘을 사용 하는 것이 가장 좋습니다.

오류가 발생 한 후 다시 시도 하기 전에 약간의 시간 (재시도 사이 몇 초)을 기다려야 합니다. 너무 자주 또는 너무 빨리 다시 시도 하는 경우 다시 시도 처리기가 제한 됩니다.


각 API 및 해당 기본 재시도 정책에 대 한 자세한 내용은 [일괄 처리 상태 및 오류 코드](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)를 참조 하세요.
