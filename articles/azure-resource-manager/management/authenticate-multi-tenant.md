---
title: 테넌트에서 인증
description: Azure Resource Manager가 테넌트에서 인증 요청을 처리하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75478826"
---
# <a name="authenticate-requests-across-tenants"></a>테넌트에서 요청 인증

다중 테넌트 애플리케이션을 만들 때 다른 테넌트에 있는 리소스에 대한 인증 요청을 처리해야 합니다. 일반적인 시나리오는 하나의 테넌트에 있는 가상 머신에서 다른 테넌트의 가상 네트워크에 조인해야 하는 경우입니다. Azure Resource Manager는 다른 테넌트에 대한 요청을 인증하기 위한 보조 토큰을 저장하기 위해 헤더 값을 제공합니다.

## <a name="header-values-for-authentication"></a>인증에 대한 헤더 값

요청에는 다음 인증 헤더 값이 있습니다.

| 헤더 이름 | 설명 | 예제 값 |
| ----------- | ----------- | ------------ |
| 권한 부여 | 기본 토큰 | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | 보조 토큰 | 전달자 &lt; 보조-token1 &gt; , encryptedbearer &lt; 보조-token2 &gt; , 전달자 &lt; 보조-token3&gt; |

보조 헤더는 최대 세 개의 보조 토큰을 유지할 수 있습니다. 

다중 테넌트 앱의 코드에서 다른 테넌트에 대한 인증 토큰을 가져오고 보조 헤더에 저장합니다. 모든 토큰은 동일한 사용자 또는 애플리케이션에서 가져와야 합니다. 사용자 또는 애플리케이션은 다른 테넌트에 대한 게스트로 초대되어야 합니다.

## <a name="processing-the-request"></a>요청 처리

앱에서 Resource Manager로 요청을 보낼 때 요청은 기본 토큰의 ID에서 실행됩니다. 기본 토큰은 유효하고 만료되지 않아야 합니다. 이 토큰은 구독을 관리할 수 있는 테넌트에서 가져와야 합니다.

요청의 다른 테넌트의 리소스를 참조하는 경우 Resource Manager는 보조 토큰을 확인하여 요청을 처리할 수 있는지 여부를 확인합니다. 헤더의 모든 보조 토큰은 유효하고 만료되지 않아야 합니다. 토큰이 만료된 경우 Resource Manager는 401 응답 코드를 반환합니다. 응답은 유효하지 않은 토큰에서 클라이언트 ID 및 테넌트 ID를 포함합니다. 보조 헤더가 테넌트에 대한 유효한 토큰을 포함하는 경우 교차 테넌트 요청이 처리됩니다.

## <a name="next-steps"></a>다음 단계

* 인증 요청에 대 한 자세한 내용은 [인증 흐름 및 응용 프로그램 시나리오](../../active-directory/develop/authentication-flows-app-scenarios.md)를 참조 하세요.
* 토큰에 대한 자세한 내용은 [Azure Active Directory 액세스 토큰](../../active-directory/develop/access-tokens.md)을 참조하세요.
