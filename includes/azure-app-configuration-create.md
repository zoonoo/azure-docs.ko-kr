---
title: 포함 파일
description: 포함 파일
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885126"
---
1. 새 앱 구성 저장소를 만들려면 먼저 [Azure Portal](https://aka.ms/azconfig/portal)에 로그인합니다. 페이지의 왼쪽 상단에서 **+ 리소스 만들기**를 클릭합니다. **Marketplace 검색** 텍스트 상자에 **App Configuration**을 입력하고 **Enter** 키를 누릅니다.

    ![App Configuration 검색](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. 검색 결과에서 **App Configuration**을 클릭하고 **만들기**를 차례로 클릭합니다.

3. **App Configuration** > **만들기** 페이지에서 다음 설정을 입력합니다.

    | 설정 | 제안 값 | 설명 |
    |---|---|---|
    | **리소스 이름** | 전역적으로 고유한 이름 | 앱 구성 저장소 리소스에 사용할 고유한 리소스 이름을 입력합니다. 이름은 1~63자의 문자열로, 숫자, 영문자 및 `-` 문자만 포함할 수 있습니다. 이름은 `-` 문자로 시작하거나 끝날 수 없고 연속되는 `-` 문자는 유효하지 않습니다.  |
    | **구독** | 사용자의 구독 | App Configuration을 테스트하는 데 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나뿐인 경우 해당 구독이 자동으로 선택되고 **구독** 드롭다운은 표시되지 않습니다. |
    | **리소스 그룹** | *AppConfigTestResources* | 앱 구성 저장소 리소스에 대한 리소스 그룹을 선택하거나 만듭니다. 이 그룹은 리소스 그룹을 삭제하여 동시에 삭제할 수 있는 여러 리소스를 구성하는 데 유용합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](/azure/azure-resource-manager/resource-group-overview)(영문)를 참조하세요. |
    | **위치**: | *미국 중부* | **위치**를 사용하여 앱 구성 저장소를 호스트할 지리적 위치를 지정합니다. 최상의 성능을 위해 애플리케이션의 다른 구성 요소와 동일한 지역에 리소스를 만드는 것이 좋습니다. |

    ![앱 구성 저장소 리소스 만들기](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. **만들기**를 클릭합니다. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

5. 배포가 완료되면 **설정** > **액세스 키**를 클릭합니다. 기본 읽기 전용 또는 기본 읽기-쓰기 키 연결 문자열 중 하나를 적어둡니다. 나중에 이 문자열을 사용하여, 사용자가 방금 만든 앱 구성 저장소와 통신하도록 애플리케이션을 구성하게 됩니다.

6. **키/값 탐색기**와 **+ 만들기**를 클릭하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:BackgroundColor | white |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | black |
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 둡니다.
