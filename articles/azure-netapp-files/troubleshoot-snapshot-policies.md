---
title: Azure NetApp Files에 대 한 스냅숏 정책 문제 해결 | Microsoft Docs
description: Azure NetApp Files에 대 한 스냅숏 정책 관리 문제를 해결 하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법에 대해 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651007"
---
# <a name="troubleshoot-snapshot-policies"></a>스냅샷 정책 문제 해결

이 문서에서는 Azure NetApp Files 스냅숏 정책을 관리 하는 경우 발생할 수 있는 오류 시나리오를 설명 합니다. 또한 문제를 해결 하는 데 도움이 될 수 있는 솔루션을 제공 합니다.

## <a name="error-conditions-and-resolutions"></a>오류 조건 및 해결 방법 

|     오류 조건    |     해결 방법    |
|-|-|
| 스냅숏 정책을 만들지 못했습니다. 스냅숏 정책 이름이 잘못 되었습니다. | 스냅숏 정책 이름이 잘못 된 경우 스냅숏 정책을 만드는 동안 오류가 발생 합니다. 스냅숏 정책 이름에는 다음 지침이 적용 됩니다.  <ul><li> 스냅숏 정책 이름에는 ASCII가 아닌 문자 또는 특수 문자를 사용할 수 없습니다. </li> <li> 스냅숏 정책 이름은 문자 또는 숫자로 시작 해야 하며 문자, 숫자, 밑줄 (' _ ') 및 하이픈 ('-')만 포함할 수 있습니다. </li> <li> 스냅숏 정책 이름은 1 ~ 007e; 64 자 사이 여야 합니다.  </li></ul> 지침에 따라 스냅숏 정책 이름을 수정 합니다.  |
| 스냅숏 정책 만들기가 잘못 된 값과 함께 실패 합니다. | 또는와 같은 필드에 대해 잘못 된 값을 입력 하면 Azure NetApp Files에서 스냅숏 정책을 만들지 못합니다 `Number of snapshots to keep` `Minute on the hour to take snapshot` . 유효한 값은 다음과 같습니다.  <ul><li>값은 유효한 숫자 여야 합니다.</li> <li>값은 0에서 59 사이 여야 합니다.</li></ul> 필드에 유효한 값이 제공 되어 있는지 확인 합니다. | 
| 오류가 발생 하 여 스냅숏 정책을 만들지 못했습니다 `Total number of snapshots to keep exceeds 255` . | 각 볼륨에는 [최대 255 개의 스냅숏이](azure-netapp-files-resource-limits.md)있을 수 있습니다. 최대값은 매시간, 매일, 매주 및 매월 스냅숏의 합계를 포함 합니다. <br> 값을 줄이고 `Snapshots to keep` 다시 시도 하세요. |
| 오류가 발생 하 여 볼륨에 정책을 할당 하지 못했습니다 `Total snapshot policy is over the max '255'` . | 각 볼륨에는 [최대 255 개의 스냅숏이](azure-netapp-files-resource-limits.md)있을 수 있습니다. 모든 요청 시, 매시간, 매일, 매주 및 매월 스냅숏의 합계가 최대값을 초과 하면 오류가 발생 합니다. <br> 값을 줄이거나 `snapshots to keep` 일부 주문형 스냅숏을 삭제 한 후 다시 시도 하십시오. | 

## <a name="next-steps"></a>다음 단계  

* [스냅숏 정책 관리](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
