---
title: Azure Network Watcher 리소스 이동 | Microsoft Docs
description: 지역 간에 Azure Network Watcher 리소스 이동
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 273dcea805e3b6ec78dfc4bd14be616f6109c097
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112032072"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>지역 간에 Azure Network Watcher 리소스 이동

## <a name="moving-the-network-watcher-resource"></a>Network Watcher 리소스 이동
Network Watcher 리소스는 Network Watcher에 대한 백 엔드 서비스를 나타내며, Azure를 통해 완전히 관리됩니다. 고객은 관리할 필요가 없습니다. 이동 작업은 이 리소스에서 지원되지 않습니다.

## <a name="moving-child-resources-of-network-watcher"></a>Network Watcher의 자식 리소스 이동
리소스를 지역 간에 이동하는 것은 현재 `*networkWatcher*` 리소스 종류의 모든 자식 리소스에 대해서도 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
* [Network Watcher 개요](./network-watcher-monitoring-overview.md)를 참조하세요.
* [Network Watcher FAQ](./frequently-asked-questions.yml)를 참조하세요.