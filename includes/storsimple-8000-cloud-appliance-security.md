---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 87e6c096c0ff0e58e8acebc13a62e44ea7d65c61
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182262"
---
StorSimple Cloud Appliance를 사용하는 경우 다음 보안 고려 사항을 염두에 두세요.

* 클라우드 어플라이언스는 Microsoft Azure 구독을 통해 보안이 설정됩니다. 따라서 클라우드 어플라이언스를 사용하고 Azure 구독이 손상되는 경우, 클라우드 어플라이언스에 저장된 데이터도 노출되기 쉽습니다.
* StorSimple에 저장된 데이터를 암호화하는 데 사용되는 인증서의 공개 키는 Azure Portal에서 안전하게 사용할 수 있으며 프라이빗 키는 StorSimple Cloud Appliance에 유지됩니다. StorSimple Cloud Appliance에서 공개 및 프라이빗 키는 모두 Azure에 저장됩니다.
* 클라우드 어플라이언스는 Microsoft Azure 데이터 센터에서 호스팅됩니다.

