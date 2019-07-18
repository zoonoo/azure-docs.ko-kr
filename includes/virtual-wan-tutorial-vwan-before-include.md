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
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182107"
---
구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 연결하려는 가상 네트워크가 이미 있는 경우에는 온-프레미스 네트워크의 서브넷 중에 연결하려는 가상 네트워크와 겹치는 서브넷이 없는지 확인하십시오. 가상 네트워크에는 게이트웨이 서브넷이 필요하지 않으며 가상 네트워크 게이트웨이를 사용할 수 없습니다. 가상 네트워크가 없는 경우 이 문서에 있는 단계를 사용하여 만들 수 있습니다.
* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 가상 네트워크이며 허브 영역에 지정하는 주소 범위는 사용자가 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.