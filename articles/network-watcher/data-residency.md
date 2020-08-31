---
title: Azure Network Watcher에 대 한 데이터 상주 | Microsoft Docs
description: Network Watcher 서비스에 대 한 데이터 상주 이해
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
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117838"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure Network Watcher에 대 한 데이터 상주
Azure Network Watcher는 연결 모니터 (미리 보기) 서비스를 제외 하 고 고객 데이터를 저장 하지 않습니다.


## <a name="connection-monitor-preview-data-residency"></a>연결 모니터 (미리 보기) 데이터 상주
*연결 모니터 (미리 보기)* 서비스는 고객 데이터를 저장 합니다. 이 데이터는 단일 지역에 Network Watcher 의해 자동으로 저장 됩니다. 따라서 *연결 모니터 (미리 보기)* 는 [보안 센터](https://azuredatacentermap.azurewebsites.net/)에 지정 된 경우를 포함 하 여 지역 데이터 상주 요구 사항을 자동으로 충족 합니다.

## <a name="singapore-data-residency"></a>싱가포르 데이터 상주

Azure에서 단일 지역에 고객 데이터를 저장 하도록 설정 하는 기능은 현재 아시아 태평양 지역의 동남 아시아 지역 (싱가포르) 에서만 사용할 수 있습니다. 다른 모든 지역의 경우 고객 데이터는 지역에 저장 됩니다. 자세한 내용은 [보안 센터](https://azuredatacentermap.azurewebsites.net/)를 참조 하세요.

> [!Note]
> **이전 복제** 고객은 최종 사용자 IP 주소와 관련 된 연결, 대기 시간 및 네트워크 토폴로지 변경 내용을 모니터링할 수 있도록 Network Watcher 인스턴스에 최종 사용자 IP 주소를 저장 Network Watcher 하는 옵션을 사용할 수 있습니다. 이러한 최종 사용자 IP 주소는 고객 데이터로 분류 될 수 있습니다. 2020 7 월 15 일 Network Watcher이 데이터는 단일 지역에 저장 됩니다. (고객 데이터는 더 이상 HK에 복제 되지 않습니다.) 이 데이터는 더 이상 HK에 복제 되지 않습니다. 이 고객 데이터는 미사용 데이터 이며 암호화 되었습니다.
> 
> 타사에서이 고객 데이터에 액세스할 수 있는 경우 타사에서 IP 주소를 확인할 수 있지만 해당 IP 주소와 연결 된 컴퓨터 또는 장치에 대 한 타사 액세스 권한은 부여 하지 않습니다. Network Watcher는 제 3 자가 HK에서이 고객 데이터에 액세스 한 것으로 간주 합니다.

## <a name="next-steps"></a>다음 단계

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)개요를 참조 하세요.
