---
title: 알려진 문제-Azure Digital Twins
description: Azure Digital Twins의 알려진 문제를 인식 하 고 완화 하는 데 도움을 받으세요.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044139"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure Digital Twins의 알려진 문제

이 문서에서는 Azure Digital Twins와 관련 된 알려진 문제에 대 한 정보를 제공 합니다.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell의 "400 클라이언트 오류: 잘못 된 요청"

"400 클라이언트 오류: url에 대 한 잘못 된 요청: http://localhost:50342/oauth2/token " 및 전체 스택 추적이 발생 하 여 Cloud Shell의 명령이 간헐적으로 실패할 수 있습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

명령을 다시 실행 `az login` 하 고 후속 로그인 단계를 완료 하 여이를 해결할 수 있습니다.

그런 다음 명령을 다시 실행할 수 있습니다.

### <a name="possible-causes"></a>가능한 원인

이는 Cloud Shell의 알려진 문제에 대 한 결과입니다. [*Cloud Shell에서 토큰을 일시적으로 가져오지 못했습니다. 400 클라이언트 오류: 잘못 된 요청*](https://github.com/Azure/azure-cli/issues/11749)입니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 솔루션에 대 한 보안*](concepts-security.md)