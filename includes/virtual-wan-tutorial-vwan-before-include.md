---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023529"
---
구성을 시작하기 전에 다음 조건을 충족하는지 확인합니다.

* 연결하려는 가상 네트워크가 이미 있는 경우 온-프레미스 네트워크의 서브넷과 중첩되지 않는지 확인합니다. 가상 네트워크에는 게이트웨이 서브넷이 필요하지 않으며 가상 네트워크 게이트웨이를 사용할 수 없습니다. 가상 네트워크가 없는 경우 이 문서에 있는 단계를 사용하여 만들 수 있습니다.
* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 가상 네트워크이며, 허브 영역에 지정하는 주소 범위는 사용자가 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위를 잘 모른다면 세부 정보를 알고 있는 다른 사람의 도움을 받으세요.
* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.