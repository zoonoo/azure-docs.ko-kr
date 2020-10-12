---
title: Azure Network Watcher에 대 한 데이터 상주 | Microsoft Docs
description: 이 문서는 Azure Network Watcher 서비스에 대 한 데이터 상주를 이해 하는 데 도움이 됩니다.
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
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706833"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure Network Watcher에 대 한 데이터 상주
연결 모니터 (미리 보기) 서비스를 제외 하 고 Azure Network Watcher는 고객 데이터를 저장 하지 않습니다.


## <a name="connection-monitor-preview-data-residency"></a>연결 모니터 (미리 보기) 데이터 상주
연결 모니터 (미리 보기) 서비스는 고객 데이터를 저장 합니다. 이 데이터는 단일 지역에 Network Watcher 의해 자동으로 저장 됩니다. 따라서 연결 모니터 (미리 보기)는 [보안 센터](https://azuredatacentermap.azurewebsites.net/)에 지정 된 요구 사항을 포함 하 여 지역 데이터 상주 요구 사항을 자동으로 충족 합니다.

## <a name="singapore-data-residency"></a>싱가포르 데이터 상주

Azure에서 단일 지역에 고객 데이터를 저장할 수 있도록 하는 기능은 현재 아시아 태평양 지역의 동남 아시아 지역 (싱가포르) 에서만 사용할 수 있습니다. 다른 모든 지역의 경우 고객 데이터는 지역에 저장 됩니다. 자세한 내용은 [보안 센터](https://azuredatacentermap.azurewebsites.net/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)개요를 참조 하세요.
