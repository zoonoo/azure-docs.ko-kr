---
title: 파일 포함
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896594"
---
구성을 시작 하기 전에 다음 조건을 충족 하는지 확인 합니다.

* 연결 하려는 가상 네트워크가 이미 있는 경우 온-프레미스 네트워크의 서브넷이 겹치지 않는지 확인 합니다. 가상 네트워크에는 게이트웨이 서브넷이 필요 하지 않으며 가상 네트워크 게이트웨이를 사용할 수 없습니다. 가상 네트워크가 없는 경우이 문서의 단계를 사용 하 여 가상 네트워크를 만들 수 있습니다.
* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 가상 네트워크 이며 허브 지역에 대해 지정 하는 주소 범위는 연결 하는 기존 가상 네트워크와 겹칠 수 없습니다. 또한 온-프레미스에 연결 하는 주소 범위와 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위에 익숙하지 않은 경우 해당 세부 정보를 제공할 수 있는 사용자와 협력 하세요.
* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.