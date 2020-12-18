---
title: 알려진 문제-Azure Digital Twins
description: Azure Digital Twins의 알려진 문제를 인식 하 고 완화 하는 데 도움을 받으세요.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q3
ms.openlocfilehash: a9735e355244d51464c66c10e02f97f03d2e67cd
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673474"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure Digital Twins의 알려진 문제

이 문서에서는 Azure Digital Twins와 관련 된 알려진 문제에 대 한 정보를 제공 합니다.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell의 "400 클라이언트 오류: 잘못 된 요청"

**문제 설명:** 에서 실행 중인 Cloud Shell의 명령은 *https://shell.azure.com* "400 클라이언트 오류: url에 대 한 잘못 된 요청 http://localhost:50342/oauth2/token " 및 전체 스택 추적으로 인해 간헐적으로 실패할 수 있습니다.

| 이는 영향을 미칩니까? | 원인 | 해결 방법 |
| --- | --- | --- |
| &nbsp;Azure &nbsp; Digital &nbsp; twins에서이는 다음 명령 그룹에 영향을 줍니다.<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | 이는 Cloud Shell의 알려진 문제에 대 한 결과입니다. [*Cloud Shell에서 토큰을 일시적으로 가져오지 못했습니다. 400 클라이언트 오류: 잘못 된 요청*](https://github.com/Azure/azure-cli/issues/11749)입니다.<br><br>이렇게 하면 Azure Digital Twins 인스턴스 인증 토큰과 Cloud Shell의 기본 [관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) 기반 인증에 문제가 발생 합니다. <br><br>이는 또는 명령 그룹의 Azure Digital Twins 명령에는 영향을 주지 않습니다 `az dt` `az dt endpoint` . 다른 유형의 인증 토큰 (Azure Resource Manager 기반)을 사용 하므로 Cloud Shell의 관리 되는 id 인증에 문제가 없는 것입니다. | 이 문제를 해결 하는 한 가지 방법은 `az login` Cloud Shell에서 명령을 다시 실행 하 고 후속 로그인 단계를 완료 하는 것입니다. 그러면 세션이 관리 되는 id 인증 외부로 전환 되어 근본 문제가 방지 됩니다. 그런 다음 명령을 다시 실행할 수 있습니다.<br><br>또는 Azure Portal에서 Cloud Shell 창을 열고 여기에서 Cloud Shell 작업을 완료할 수 있습니다.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Azure Portal 아이콘 표시줄의 Cloud Shell 아이콘 이미지" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>마지막으로, Azure CLI 명령을 로컬로 실행할 수 있도록 컴퓨터에 [Azure CLI을 설치](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 하는 것이 또 다른 해결 방법입니다. 로컬 CLI에는이 문제가 발생 하지 않습니다. |


## <a name="missing-role-assignment-after-scripted-setup"></a>스크립팅된 설치 후 역할 할당이 누락 되었습니다.

**문제 설명:** 일부 사용자에 게 [*는 방법: 인스턴스 및 인증 (스크립팅된) 설정*](how-to-set-up-instance-scripted.md)의 역할 할당 부분과 관련 된 문제가 발생할 수 있습니다. 이 스크립트는 실패를 나타내지는 않지만 *Azure Digital Twins 데이터 소유자* 역할은 사용자에 게 할당 되지 않으며,이 문제는 다른 리소스를 다른 리소스를 만들 수 있는 기능에 영향을 줍니다.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| 이는 영향을 미칩니까? | 원인 | 해결 방법 |
| --- | --- | --- |
| 스크립트를 실행 한 후 역할 할당이 성공적으로 설정 되었는지 확인 하려면 설치 문서의 [*사용자 역할 할당 확인*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) 섹션에 있는 지침을 따르세요. 사용자가이 역할에 표시 되지 않는 경우이 문제가 영향을 받습니다. | 사용자가 [MSA (개인 Microsoft 계정)](https://account.microsoft.com/account)로 로그인 한 사용자의 경우 사용자의 로그인 메일과 다를 수 있습니다. 사용자의 로그인 메일과 같이 스크립트를 검색 하 여 역할을 적절 하 게 할당 하는 데 사용 하기 어려울 수 있습니다. | 해결 하려면 [CLI 명령](how-to-set-up-instance-cli.md#set-up-user-access-permissions) 또는 [Azure Portal 지침](how-to-set-up-instance-portal.md#set-up-user-access-permissions)을 사용 하 여 수동으로 역할 할당을 설정할 수 있습니다. |

## <a name="issue-with-interactive-browser-authentication"></a>대화형 브라우저 인증과 관련 된 문제

**문제 설명:** Azure **1.2.0** **[](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) 라이브러리** 의 버전을 사용 하 여 azure Digital twins 응용 프로그램에서 인증 코드를 작성할 때 [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 메서드와 관련 된 문제가 발생할 수 있습니다. 이는 브라우저 창에서 인증을 시도할 때 "AuthenticationFailedException"의 오류 응답으로 표시 됩니다. 브라우저 창이 완전히 시작 되지 않거나 사용자를 성공적으로 인증 하는 것 처럼 보이지만 클라이언트 응용 프로그램은 오류와 함께 실패 합니다.

| 이는 영향을 미칩니까? | 원인 | 해결 방법 |
| --- | --- | --- |
| &nbsp;영향을 받는 &nbsp; 메서드는 &nbsp; &nbsp; &nbsp; &nbsp; 다음 문서에서 사용 됩니다 &nbsp; .<br><br>[*자습서: 클라이언트 앱 코딩*](tutorial-code.md)<br><br>[*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)<br><br>[*방법: Azure Digital Twins Api 및 Sdk 사용*](how-to-use-apis-sdks.md) | 일부 사용자에 게는 라이브러리의 버전 **1.2.0** 문제가 있습니다 `Azure.Identity` . | 이 문제를 해결 하려면 [최신 버전](https://www.nuget.org/packages/Azure.Identity) 의를 사용 하도록 응용 프로그램을 업데이트 `Azure.Identity` 합니다. 라이브러리 버전을 업데이트 한 후 브라우저에서 예상 대로 로드 하 고 인증 해야 합니다. |

## <a name="next-steps"></a>다음 단계

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 솔루션에 대 한 보안*](concepts-security.md)