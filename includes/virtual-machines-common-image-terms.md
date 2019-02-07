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
ms.openlocfilehash: 19a78b772d2813c263017515f18da06fdb20aa70
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082268"
---
## <a name="terminology"></a>용어

Azure의 Marketplace 이미지에는 다음과 같은 특성이 있습니다.

* **게시자**: 이미지를 만든 조직입니다. 예제: Canonical, MicrosoftWindowsServer
* **제품**: 게시자가 만든 관련 이미지 그룹의 이름입니다. 예제: Ubuntu Server, WindowsServer
* **SKU**: 제품의 인스턴스(예: 배포의 주 릴리스)입니다. 예제: 18.04-LTS, 2019-Datacenter
* **버전**: 이미지 SKU의 버전 번호입니다. 

프로그래밍 방식으로 VM을 배포할 때 Marketplace 이미지를 식별하기 위해 이러한 값을 개별적인 매개변수로 제공합니다. 일부 도구는 콜론(:) 문자로 구분된 값을 결합하는 이미지 *URN*을 허용합니다(예: *게시자*:*제안*:*SKU*:*버전*). URN에서 버전 번호를 "latest"로 바꿀 수 있으며, 그러면 이미지의 최신 버전이 선택됩니다. 

이미지 게시자가 추가 라이선스 및 구매 약관을 제공하는 경우에는, 해당 약관에 동의하고 프로그래밍 방식 배포를 사용하도록 설정해야 합니다. VM을 프로그래밍 방식으로 배포하는 경우에는 *구매 계획* 매개 변수도 제공해야 합니다. [Marketplace 약관으로 이미지 배포](#deploy-an-image-with-marketplace-terms)를 참조하세요.