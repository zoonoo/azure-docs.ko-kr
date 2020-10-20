---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204468"
---
Azure를 사용 하면 리소스에 대 한 _잠금을_ 설정 하 여 삭제 하거나 읽기 전용으로 설정할 수 있습니다. __리소스를 잠그면 예기치 않은 결과가 발생할 수 있습니다.__ 리소스를 수정 하지 않는 일부 작업에는 실제로 잠금에 의해 차단 되는 작업이 필요 합니다. 

예를 들어 작업 영역에 대 한 리소스 그룹에 삭제 잠금을 적용 하면 Azure ML 계산 클러스터에 대 한 크기 조정 작업이 차단 됩니다.

리소스 잠금에 대 한 자세한 내용은 [예기치 않은 변경을 방지 하기 위해 리소스 잠그기](../articles/azure-resource-manager/management/lock-resources.md)를 참조 하세요.