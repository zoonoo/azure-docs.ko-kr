---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993016"
---
### <a name="access-tokens"></a>액세스 토큰

액세스 토큰은 Azure Spatial Anchors로 인증하는 보다 강력한 방법입니다. 특히 프로덕션 배포를 위해 애플리케이션을 준비하는 경우입니다. 이 방법의 요약은 클라이언트 애플리케이션에서 안전하게 인증할 수 있는 백 엔드 서비스를 설정하는 것입니다. 백 엔드 서비스는 런타임 시 AAD 및 Azure Spatial Anchors Secure Token Service와 인터페이스하여 액세스 토큰을 요청합니다. 그런 다음, 이 토큰은 클라이언트 애플리케이션으로 전달되고 SDK에서 Azure Spatial Anchors로 인증하는 데 사용됩니다.
