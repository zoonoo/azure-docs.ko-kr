---
title: 인스턴스 및 인증 설정(포털)
titleSuffix: Azure Digital Twins
description: Azure Portal를 사용 하 여 Azure Digital Twins 서비스 인스턴스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0dfc93987fb2ca25b9e397e4aa91adcaaafd8a38
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478861"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure Digital Twins 인스턴스 및 인증 설정 (포털)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정 하는 등 **새 Azure Digital Twins 인스턴스를 설정**하는 단계에 대해 설명 합니다. 이 문서를 완료 한 후에는에 대 한 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스를 사용할 수 있습니다.

이 문서의 버전은 Azure Portal를 사용 하 여 이러한 단계를 하나씩 수동으로 진행 합니다. Azure Portal은 명령줄 도구의 대안을 제공하는 웹 기반의 통합 콘솔입니다.
* CLI를 사용 하 여 이러한 단계를 수동으로 진행 하려면이 문서의 CLI 버전 [*(방법: 인스턴스 및 인증 설정 (cli))*](how-to-set-up-instance-cli.md)을 참조 하세요.
* 배포 스크립트 샘플을 사용 하 여 자동화 된 설치를 실행 하려면이 문서의 스크립팅된 버전 [*(방법: 인스턴스 및 인증 설정 (스크립팅된))*](how-to-set-up-instance-scripted.md)을 참조 하세요.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

이 섹션에서는 [Azure Portal](https://ms.portal.azure.com/)를 사용 하 여 **Azure Digital twins의 새 인스턴스를 만듭니다** . 포털로 이동 하 여 자격 증명으로 로그인 합니다.

포털에서 Azure 서비스 홈 페이지 메뉴에서 _리소스 만들기_ 를 선택 하 여 시작 합니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::

검색 상자에서 *Azure Digital Twins* 를 검색 하 고 결과에서 **Azure digital Twins (미리 보기)** 서비스를 선택 합니다. _만들기_ 단추를 선택 하 여 서비스의 새 인스턴스를 만듭니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::

다음 *리소스 만들기* 페이지에서 아래 지정 된 값을 입력 합니다.
* **구독**: 사용 중인 Azure 구독
  - **리소스 그룹**: 인스턴스를 배포할 리소스 그룹입니다. 기존 리소스 그룹이 아직 없는 경우 *새로* 만들기 링크를 선택 하 고 새 리소스 그룹의 이름을 입력 하 여 여기에서 하나를 만들 수 있습니다.
* **Location**: 배포를 위한 Azure Digital twins 지원 지역입니다. 지역별 지원에 대 한 자세한 내용은 [*지역별 사용 가능한 azure 제품 (Azure Digital Twins)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 참조 하세요.
* **리소스 이름**: Azure Digital twins 인스턴스의 이름입니다. 새 인스턴스의 이름은 구독에 대 한 지역 내에서 고유 해야 합니다. 즉, 사용자가 선택한 이름을 이미 사용 하 고 있는 지역에 다른 Azure 디지털 Twins 인스턴스가 있는 경우 다른 이름을 선택 하 라는 메시지가 표시 됩니다.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::

완료 되 면 _검토 + 만들기_를 선택 합니다. 그러면 입력 한 인스턴스 세부 정보를 검토 하 고 _만들기_를 누르면 요약 페이지로 이동 합니다. 

### <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요 한 값 수집

*만든*후에는 포털 아이콘 표시줄을 따라 Azure 알림에서 인스턴스의 배포 상태를 볼 수 있습니다. 배포에 성공 하면 알림이 표시 되 고, _리소스로 이동_ 단추를 선택 하 여 생성 된 인스턴스를 볼 수 있습니다.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::

또는 배포에 실패 하는 경우 알림이 표시 됩니다. 오류 메시지에서 조언을 관찰 하 고 인스턴스 만들기를 다시 시도 합니다.

>[!TIP]
>인스턴스를 만든 후에는 Azure Portal 검색 표시줄에서 인스턴스 이름을 검색 하 여 언제 든 지 페이지로 돌아갈 수 있습니다.

인스턴스의 *개요* 페이지에서 *이름*, *리소스 그룹*및 *호스트 이름을*확인 합니다. 이러한 값은 Azure Digital Twins 인스턴스 작업을 계속할 때 필요할 수 있는 모든 중요 한 값입니다. 다른 사용자가 인스턴스에 대 한 프로그래밍을 수행 하는 경우 이러한 값을 공유 해야 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 그런 다음 적절 한 Azure 사용자에 게 관리 권한을 부여 합니다.

## <a name="set-up-user-access-permissions"></a>사용자 액세스 권한 설정

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

먼저 Azure Portal에서 Azure Digital Twins 인스턴스의 페이지를 엽니다. 인스턴스의 메뉴에서 *Access control (IAM)* 을 선택 합니다. *역할 할당 추가*에서 *추가* 단추를 선택 합니다.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::

다음 *역할 할당 추가* 페이지에서 값을 입력 합니다 (Azure 구독에서 [충분 한 권한이](#prerequisites-permission-requirements) 있는 사용자가 완료 해야 함).
* **역할**: 드롭다운 메뉴에서 *Azure Digital Twins 데이터 소유자* 를 선택 합니다.
* **액세스 권한 할당**: 드롭다운 메뉴에서 *Azure AD 사용자, 그룹 또는 서비스 사용자를* 선택 합니다.
* **선택**: 할당할 사용자의 이름 또는 전자 메일 주소를 검색 합니다. 결과를 선택 하면 사용자가 *선택한 구성원* 섹션에 표시 됩니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

세부 정보 입력을 마치면 *저장* 단추를 누릅니다.

### <a name="verify-success"></a>성공 확인

*액세스 제어 (IAM) > 역할 할당*에 설정 된 역할 할당을 볼 수 있습니다. 사용자는 *Azure Digital Twins 데이터 소유자*역할을 사용 하 여 목록에 표시 되어야 합니다. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure Portal의 홈 페이지에서 ' 리소스 만들기 ' 선택":::

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되 고 관리 권한이 할당 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins CLI 명령을 사용 하 여 인스턴스에서 개별 REST API 호출을 테스트 합니다. 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)

또는 인증 코드를 사용 하 여 인스턴스에 클라이언트 응용 프로그램을 연결 하는 방법을 참조 하세요.
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)