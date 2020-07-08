---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465126"
---
퍼블릭 엔드포인트를 특정 가상 네트워크로 제안하려는 스토리지 계정으로 이동합니다. 스토리지 계정의 목차에서 **방화벽 및 가상 네트워크**를 선택합니다. 

페이지 위쪽에서 **선택한 네트워크** 라디오 단추를 선택합니다. 그러면 퍼블릭 엔드포인트의 제한을 제어할 수 있는 여러 설정이 표시됩니다. **+기존 가상 네트워크 추가**를 클릭하여 퍼블릭 엔드포인트를 통해 스토리지 계정에 액세스할 수 있도록 허용해야 하는 가상 네트워크를 선택합니다. 이렇게 하려면 가상 네트워크를 선택하고 해당 가상 네트워크의 서브넷을 선택해야 합니다. 

**신뢰할 수 있는 Microsoft 서비스가 이 서비스 계정에 액세스할 수 있도록 허용**을 선택하여 Azure 파일 동기화와 같은 신뢰할 수 있는 자사 Microsoft 서비스에서 스토리지 계정에 액세스할 수 있도록 허용합니다.

[![퍼블릭 엔드포인트를 통해 스토리지 계정에 액세스할 수 있도록 허용된 가상 네트워크의 방화벽 및 가상 네트워크 블레이드 스크린샷](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)