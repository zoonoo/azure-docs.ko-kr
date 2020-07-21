---
title: 알려진 문제-Azure Digital Twins
description: Azure Digital Twins의 알려진 문제를 인식 하 고 완화 하는 데 도움을 받으세요.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531736"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure Digital Twins의 알려진 문제

이 문서에서는 Azure Digital Twins와 관련 된 알려진 문제에 대 한 정보를 제공 합니다.

## <a name="managing-event-routes-in-the-azure-portal"></a>Azure Portal에서 이벤트 경로 관리

계정 등의 [**MSA (개인 Microsoft 계정)**](https://account.microsoft.com/account/Account)를 사용 하 여 포털에 로그인 한 경우 *@outlook.com* 사용 권한 수준에 관계 없이 포털에서 이벤트 경로를 관리 하려고 할 때 *이벤트 경로를 볼 수 있는 권한이 필요* 하다는 화면이 표시 됩니다.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Azure Digital Twins 인스턴스에서 이벤트 경로를 만들려고 할 때 사용 권한 오류 Azure Portal의 스크린샷":::

### <a name="troubleshooting-steps"></a>문제 해결 단계

현재 포털에서 이벤트 경로를 관리할 수 없는 사용자는 Azure Digital Twins Api 또는 CLI를 사용 하 여 이벤트 경로를 관리할 수 있습니다. 이러한 문제를 완화 하기 위해 이러한 도구 중 하나로 이벤트 경로 관리를 전환 하는 것이 좋습니다.

이에 대 한 지침은 [*방법: 끝점 및 경로 관리*](how-to-manage-routes.md)에서 찾을 수 있습니다.

### <a name="possible-causes"></a>가능한 원인

계정 같은 개인 [Microsoft 계정 (MSA)](https://account.microsoft.com/account/Account)를 사용 하 여 포털에 로그인 *@outlook.com* 합니다. Azure Portal에서 이벤트 경로를 관리 하는 것은 현재 회사 도메인 계정의 Azure 사용자만 사용할 수 있습니다.

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