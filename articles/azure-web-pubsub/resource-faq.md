---
title: Azure Web PubSub 서비스 FAQ
description: Azure Web PubSub 서비스에 대한 질문과 대답을 가져옵니다.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: e53823539194fe1082621f458e8b5b3d493c191d
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166949"
---
# <a name="azure-web-pubsub-service-faq"></a>Azure Web PubSub 서비스 FAQ

Azure Web PubSub 서비스의 FAQ입니다. 

## <a name="is-azure-web-pubsub-service-ready-for-production-use"></a>Azure Web PubSub 서비스가 프로덕션에 사용할 준비가 되었나요?

Azure Web PubSub 서비스는 공개 미리 보기 상태이며 커밋된 SLA가 아닙니다. 

##  <a name="where-does-my-data-reside"></a>데이터가 어디에 상주하나요?

Azure Web PubSub 서비스는 데이터 프로세서 서비스로 작동합니다. 고객 콘텐츠를 저장하지 않으며 데이터 상주는 디자인에 의해 포함됩니다. 진단을 위해 Azure Storage와 같은 다른 Azure 서비스와 Azure Web PubSub 서비스를 함께 사용하는 경우 Azure 지역에서 데이터 상주를 유지하는 방법에 대한 지침은 [이 백서](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)를 참조하세요.