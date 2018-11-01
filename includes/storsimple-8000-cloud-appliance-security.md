---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f77310ebf10812c1bf15a2555520f46c10c8c182
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166232"
---
<!--alkohli 02/21/2017 cloud appliance security-->

StorSimple Cloud Appliance를 사용하는 경우 다음 보안 고려 사항을 염두에 두세요.

* 클라우드 어플라이언스는 Microsoft Azure 구독을 통해 보안이 설정됩니다. 따라서 클라우드 어플라이언스를 사용하고 Azure 구독이 손상되는 경우, 클라우드 어플라이언스에 저장된 데이터도 노출되기 쉽습니다.
* StorSimple에 저장된 데이터를 암호화하는 데 사용되는 인증서의 공개 키는 Azure Portal에서 안전하게 사용할 수 있으며 개인 키는 StorSimple Cloud Appliance에 유지됩니다. StorSimple Cloud Appliance에서 공개 및 개인 키는 모두 Azure에 저장됩니다.
* 클라우드 어플라이언스는 Microsoft Azure 데이터 센터에서 호스팅됩니다.

