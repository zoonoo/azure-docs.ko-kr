---
title: 애플리케이션 프록시 애플리케이션에 필요한 방화벽 포트를 여는 방법 | Microsoft Docs
description: Azure AD 애플리케이션 프록시가 올바르게 작동하기 위해 열어야 하는 포트 찾기
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e69f2e5049ca290a17c058c9d18dc7c6ec91f49
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783555"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>애플리케이션 프록시 애플리케이션에 필요한 방화벽 포트를 여는 방법

각 포트의 기능 및 필요한 포트의 전체 목록을 보려면 [애플리케이션 프록시 설명서](application-proxy-add-on-premises-application.md)의 필수 조건 섹션을 참조하세요. 애플리케이션 프록시는 아웃바운드 포트만 사용합니다.

모든 필요한 포트를 열어서 있는지 확인할 수도 있습니다는 [커넥터 포트 테스트 도구](https://aadap-portcheck.connectorporttest.msappproxy.net/) 온-프레미스 네트워크에서 합니다. 녹색 확인 표시가 많을수록 복원력이 더 뛰어난 것입니다. 

## <a name="app-proxy-regions"></a>앱 프록시 영역

다음 중 반드시 녹색이어야 하는 영역을 알려주는 방법에 대해 연구하고 있습니다. 지금은 모두 녹색인지 확인합니다. 미국 중부 지역은 현재 위치한 지역에 상관없이 필요합니다.

이 도구로 올바른 결과를 얻을 수 있는지 확인하려면 다음을 수행합니다.

-   커넥터를 설치한 서버의 브라우저에서 도구를 엽니다.

-   커넥터에 적용할 수 있는 프록시 또는 방화벽이 이 페이지에도 적용되었는지 확인합니다. 이로 이동 하 여 Internet Explorer에서 수행할 수 있습니다 **설정을**  - &gt; **인터넷 옵션**  - &gt; **연결**  - &gt; **LAN 설정**합니다. 이 페이지에 "사용자 LAN에 프록시 서버 사용" 필드가 표시됩니다. 이 확인란을 선택하고 "주소" 필드에 프록시 주소를 입력합니다.

## <a name="next-steps"></a>다음 단계
[Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)
