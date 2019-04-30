---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097709"
---
가용성 그룹 수신기를 만든 후에 수신기 리소스에 대한 RegisterAllProvidersIP 및 HostRecordTTL 클러스터 매개 변수를 조정해야 할 수도 있습니다. 이러한 매개 변수는 장애 조치 후 다시 연결 시간을 줄여 연결 시간 제한을 방지할 수 있습니다. 이러한 매개 변수와 샘플 코드에 대한 자세한 내용은 [가용성 그룹 수신기 만들기 또는 구성](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)을 참조하세요.

<!-- Update_Description: update meta properties -->