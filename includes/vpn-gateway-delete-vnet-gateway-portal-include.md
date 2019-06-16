---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157413"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>1단계: 가상 네트워크 게이트웨이로 이동

1. [Azure Portal](https://portal.azure.com)에서 **모든 리소스**로 이동합니다. 
2. 가상 네트워크 게이트웨이 페이지를 열려면 삭제하려는 가상 네트워크 게이트웨이로 이동하여 클릭합니다.

### <a name="step-2-delete-connections"></a>2단계: 연결 삭제

1. 가상 네트워크 게이트웨이에 대한 페이지에서 **연결**을 클릭하여 게이트웨이에 대한 모든 연결을 봅니다.
2. 연결 이름 행의 **'...'** 을 클릭하고 드롭다운에서 **삭제**를 선택합니다.
3. **예**를 클릭하여 연결 삭제를 확인합니다. 연결이 여러 개인 경우 각 연결을 삭제합니다.

### <a name="step-3-delete-the-virtual-network-gateway"></a>3단계: 가상 네트워크 게이트웨이 삭제

이 VNet에 S2S 구성 외에 P2S 구성이 있는 경우 가상 네트워크 게이트웨이를 삭제하면 경고 없이 모든 P2S 클라이언트의 연결이 자동으로 끊어집니다.

1. 가상 네트워크 게이트웨이 페이지에서 **개요**를 클릭합니다.
2. **개요** 페이지에서 **삭제**를 클릭하여 게이트웨이를 삭제합니다.
