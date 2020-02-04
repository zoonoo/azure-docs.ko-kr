---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694270"
---
## <a name="set-up-authentication"></a>인증 설정

서비스에 액세스하려면 계정 키, 액세스 토큰 또는 Azure Active Directory 인증 토큰을 제공해야 합니다. [인증 개념 페이지](/azure/spatial-anchors/concepts/authentication)에서 이에 대한 자세한 내용을 읽을 수도 있습니다.

### <a name="account-keys"></a>계정 키

계정 키는 애플리케이션에서 Azure Spatial Anchors 서비스를 사용하여 인증할 수 있도록 하는 자격 증명입니다. 계정 키의 용도는 빨리 시작할 수 있도록 지원합니다. 특히 애플리케이션이 Azure Spatial Anchors와 통합하는 단계에서 더 필요합니다. 따라서 개발 중에 클라이언트 애플리케이션에 계정 키를 포함하여 계정 키를 사용할 수 있습니다. 개발을 넘어 진행할 때는 액세스 토큰에서 지원하는 프로덕션 수준의 인증 메커니즘이나 Azure Active Directory 사용자 인증으로 이동하는 것이 좋습니다. 개발을 위한 계정 키를 가져오려면 Azure Spatial Anchors 계정을 방문하여 "키" 탭으로 이동합니다.
