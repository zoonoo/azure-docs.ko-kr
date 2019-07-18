---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182150"
---
가용성 그룹 수신기를 만든 후에 수신기 리소스에 대한 RegisterAllProvidersIP 및 HostRecordTTL 클러스터 매개 변수를 조정해야 할 수도 있습니다. 이러한 매개 변수는 장애 조치 후 다시 연결 시간을 줄여 연결 시간 제한을 방지할 수 있습니다. 이러한 매개 변수와 샘플 코드에 대한 자세한 내용은 [가용성 그룹 수신기 만들기 또는 구성](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)을 참조하세요.

