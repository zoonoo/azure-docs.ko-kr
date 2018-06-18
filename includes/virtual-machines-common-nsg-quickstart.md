---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34684985"
---
서브넷 또는 VM 네트워크 인터페이스에서 네트워크 필터를 만들어, Azure에서 VM(가상 컴퓨터)에 대한 포트를 열거나 끝점을 만듭니다. 인바운드 및 아웃바운드 트래픽을 모두 제어하는 이러한 필터를 트래픽을 수신하는 리소스에 연결된 네트워크 보안 그룹에 배치합니다.

포트 80에서 웹 트래픽의 일반적인 예제를 사용해 보겠습니다. 표준 TCP 포트 80에 대한 웹 요청을 처리하도록 구성된 VM이 있는 경우(적절한 서비스를 시작하고 VM에서 OS 방화벽 규칙을 열어야 함) 다음을 수행합니다.

1. 네트워크 보안 그룹을 만듭니다.
2. 다음을 사용하여 트래픽을 허용하는 인바운드 규칙을 만듭니다.
   * 대상 포트 범위 "80"
   * 원본 포트 범위 " * "(모든 원본 포트 가능).
   * 우선 순위 값 65,500 이하(기본 범용 거부 인바운드 규칙보다 우선 순위 높음)
3. 네트워크 보안 그룹을 VM 네트워크 인터페이스 또는 서브넷에 연결합니다.

네트워크 보안 그룹 및 규칙을 사용하여 환경을 보호하는 복잡한 네트워크 구성을 만들 수 있습니다. 이 예제에서는 HTTP 트래픽 또는 원격 관리를 허용하는 하나 이상의 규칙을 사용합니다. 자세한 내용은 다음 ['자세한 내용'](#more-information-on-network-security-groups) 섹션 또는 [네트워크 보안 그룹이란?](../articles/virtual-network/security-overview.md)을 참조하세요.

