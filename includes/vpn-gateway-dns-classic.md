---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875569"
---
DNS 설정은이 구성의 필수 부분이 아니지만 Vm 간의 이름 확인을 원하는 경우 DNS가 필요 합니다. 값을 지정하더라도 새 DNS 서버를 만들지 않습니다. 지정한 DNS 서버는 연결 중인 리소스에 대한 이름을 확인할 수 있는 DNS 서버 IP 주소여야 합니다.

가상 네트워크를 만든 후에 DNS 서버의 IP 주소를 추가하여 이름 확인을 처리할 수 있습니다. 가상 네트워크에 대 한 설정을 열고 DNS 서버를 선택한 다음 이름 확인에 사용할 DNS 서버의 IP 주소를 추가 합니다.

1. 포털에서 가상 네트워크를 찾습니다.
2. 가상 네트워크에 대 한 페이지의 **설정** 섹션에서 **DNS 서버**를 선택 합니다.
3. DNS 서버를 추가합니다.
4. 설정을 저장 하려면 페이지 위쪽에서 **저장** 을 선택 합니다.