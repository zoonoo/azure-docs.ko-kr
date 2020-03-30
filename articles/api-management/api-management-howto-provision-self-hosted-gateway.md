---
title: Azure API 관리에서 자체 호스팅 게이트웨이 프로비전 | 마이크로 소프트 문서
description: Azure API 관리에서 자체 호스팅 게이트웨이를 프로비전하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075280"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Azure API 관리에서 자체 호스팅 게이트웨이 프로비전

Azure API Management 인스턴스에서 게이트웨이 리소스프로비전은 자체 호스팅 게이트웨이를 배포하기 위한 전제 조건입니다. 이 문서에서는 API 관리에서 게이트웨이 리소스를 프로비전하는 단계를 안내합니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능이 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 추가 비용 없이 개발자 및 프리미엄 계층에서만 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한됩니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>자체 호스팅 게이트웨이 프로비전

1. **설정**에서 **게이트웨이를 선택합니다.**
2. **+ 추가**를 클릭합니다.
3. 게이트웨이의 **이름** 및 **지역을** 입력합니다.
> [!TIP]
> **영역은** 이 게이트웨이 리소스와 연결될 게이트웨이 노드의 의도된 위치를 지정합니다. 이는 모든 Azure 리소스와 연결된 유사한 속성과 동일하지만 임의의 문자열 값을 할당할 수 있습니다.

4. 선택적으로 게이트웨이 리소스에 대한 **설명을** 입력합니다.
5. 선택적으로 **+** **API** 아래에서 선택하여 하나 이상의 API를 이 게이트웨이 리소스와 연결합니다.
> [!TIP]
> API의 **설정** 탭의 게이트웨이에서 API를 연결하고 제거할 수 있습니다.

> [!IMPORTANT]
> 기본적으로 기존 API는 새 게이트웨이 리소스와 연결되지 않습니다. 따라서 새 게이트웨이를 통해 호출하려고 하면 `404 Resource Not Found` 응답이 발생합니다.

6. **추가**를 클릭합니다.

이제 게이트웨이 리소스가 API 관리 인스턴스에 프로비전되었습니다. 게이트웨이 배포를 진행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대한 자세한 내용은 [Azure API Management 자체 호스팅 게이트웨이 개요를](self-hosted-gateway-overview.md) 참조하십시오.
* [Kubernetes에 자체 호스팅 게이트웨이를 배포하는](api-management-howto-deploy-self-hosted-gateway-to-k8s.md) 방법에 대해 자세히 알아보기
* [Docker에 자체 호스팅 게이트웨이를 배포하는](api-management-howto-deploy-self-hosted-gateway-to-docker.md) 방법에 대해 자세히 알아보기
