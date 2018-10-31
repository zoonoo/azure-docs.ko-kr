---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437028"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Marketplace 약관으로 이미지 배포

Azure Marketplace의 일부 VM 이미지를 프로그래밍 방식으로 배포하려면 동의가 필요한 추가 라이선스 및 구매 약관이 있습니다.  

이러한 이미지에서 VM을 배포하려면 이미지의 약관에 동의하고 프로그래밍 방식 배포를 사용하도록 설정해야 합니다. 이 작업은 구독에 대해 한 번만 수행해야 합니다. 그 후에 VM을 이미지에서 프로그래밍 방식으로 배포할 때마다 *구매 계획* 매개 변수를 지정해야 합니다.

다음 섹션에서는 수행 방법을 보여 줍니다.

* Marketplace 이미지에 추가 사용 조건이 있는지 여부 확인 
* 프로그래밍 방식으로 약관에 동의
* 프로그래밍 방식으로 VM을 배포할 때 구매 계획 매개 변수 제공

