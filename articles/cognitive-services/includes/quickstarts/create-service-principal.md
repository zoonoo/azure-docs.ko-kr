---
title: Azure 서비스 주체 만들기
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95096959"
---
## <a name="create-an-azure-service-principal"></a>Azure 서비스 주체 만들기

애플리케이션이 Azure 계정과 상호 작용하도록 하려면 사용 권한을 관리하는 Azure 서비스 주체가 필요합니다. [Azure 서비스 주체 만들기](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)의 지침을 따르세요.

서비스 주체를 만들면 비밀 값, ID 및 애플리케이션 ID가 있는 것을 볼 수 있습니다. 이후 단계를 위해 애플리케이션 ID 및 비밀을 임시 위치에 저장합니다.