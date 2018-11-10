---
title: '빠른 시작: Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기'
description: 이 빠른 시작에서 Azure 데이터 탐색기 클러스터 및 데이터베이스를 만들고 데이터를 수집(로드)하는 방법을 알아봅니다.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 4b4230397da7d97b613b546f33a0cbb764675720
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214820"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>빠른 시작: Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure 데이터 탐색기를 사용하려면 먼저 *클러스터*를 만들고 이 클러스터에 *데이터베이스*를 하나 이상 만듭니다. 그런 다음, 데이터베이스에 대해 쿼리를 실행할 수 있도록 데이터베이스에 데이터를 *수집*(로드)합니다. 이 빠른 시작에서는 클러스터와 데이터베이스를 만듭니다. 후속 문서에서는 데이터를 수집하는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-cluster"></a>클러스터 만들기

Azure 리소스 그룹에 계산 및 저장소 리소스 집합이 정의된 Azure 데이터 탐색기 클러스터를 만듭니다.

1. 포털의 왼쪽 상단 모서리에서 **리소스 만들기** 단추(+)를 선택합니다.

1. *Azure 데이터 탐색기*를 검색합니다.

   ![리소스 검색](media/create-cluster-database-portal/search-resources.png)

1. 화면 하단의 **Azure 데이터 탐색기**에서 **만들기**를 선택합니다.

1. 클러스터의 고유 이름을 입력하고 구독을 선택하고 *test-resource-group*라는 이름의 리소스 그룹을 만듭니다.

    ![리소스 그룹 만들기](media/create-cluster-database-portal/create-resource-group.png)

1. 다음 정보로 양식을 작성합니다.

   ![클러스터 생성 양식](media/create-cluster-database-portal/create-cluster-form.png)

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 클러스터 이름 | 고유한 클러스터 이름 | 클러스터를 식별하는 고유한 이름을 선택합니다. 예: *mytestcluster*. 입력한 클러스터 이름에 도메인 이름 *[region].kusto.windows.net*이 추가됩니다. 이름에는 소문자와 숫자만 포함할 수 있습니다. 3~22자를 포함해야 합니다.
    | 구독 | 사용자의 구독 | 클러스터에 사용할 Azure 구독을 선택합니다.|
    | 리소스 그룹 | *test-resource-group* | 새 리소스 그룹을 만듭니다. |
    | 위치 | *미국 서부* | 이 빠른 시작에서는 *미국 서부*를 선택합니다. 프로덕션 시스템의 경우 요구에 가장 적합한 지역을 선택합니다.
    | 컴퓨팅 사양 | *D13_v2* | 이 빠른 시작에 가장 낮은 가격 사양을 선택합니다. 프로덕션 시스템의 경우 요구에 가장 적합한 사양을 선택합니다.
    | | |

1. **만들기**를 선택하여 클러스터를 프로비전합니다. 프로비전에는 일반적으로 약 10분이 소요됩니다. 프로비전 프로세스를 모니터링하려면 도구 모음에서 **알림**을 선택합니다.

    ![공지](media/create-cluster-database-portal/notifications.png)

1. 프로세스가 완료되면 **알림**, **리소스로 이동**을 차례로 선택합니다.

    ![리소스로 이동](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>데이터베이스 만들기

이제 프로세스의 두 번째 단계: 데이터 만들기에 대한 준비가 되었습니다.

1. **개요** 탭에서 **데이터베이스 만들기**를 선택합니다.

    ![2단계: 데이터베이스 만들기](media/create-cluster-database-portal/database-creation.png)

1. 다음 정보로 양식을 작성합니다.

    ![데이터베이스 생성 양식](media/create-cluster-database-portal/create-database.png)

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 데이터베이스 이름 | *TestDatabase* | 데이터베이스 이름은 클러스터 내에서 고유해야 합니다.
    | 보존 기간 | *3650* | 데이터를 쿼리에 사용할 수 있도록 보장된 기간입니다. 시간 범위는 데이터가 수집된 시간부터 측정됩니다.
    | 캐시 기간 | *31* | 자주 쿼리되는 데이터를 장기 저장소가 아닌 SSD 저장소 또는 RAM에 보관할 수 있는 시간 범위입니다.
    | | | |

1. **저장**을 선택하여 데이터베이스를 만듭니다. 만들기에는 일반적으로 채 1분이 소요되지 않습니다. 프로세스가 완료되면 클러스터 **개요** 탭으로 되돌아갑니다.

## <a name="run-basic-commands-in-the-database"></a>데이터베이스에 기본 명령 실행

클러스터와 데이터베이스가 있으면 쿼리와 명령을 실행할 수 있습니다. 데이터베이스에 아직 데이터가 없지만 도구가 어떻게 작동하는지는 볼 수 있습니다.

1. 클러스터 아래에서 **쿼리**를 선택합니다.

    ![데이터베이스 쿼리](media/create-cluster-database-portal/query-database.png)

1. `.show databases` 명령을 쿼리 창에 붙여넣은 다음, **실행**을 선택합니다.

    ![데이터베이스 표시 명령](media/create-cluster-database-portal/show-databases.png)

    결과 집합에 클러스터의 유일한 데이터베이스인 **TestDatabase**가 표시됩니다.

1. `.show tables` 명령을 쿼리 창에 붙여넣은 다음, 창에서 해당 명령을 선택합니다. **실행**을 선택합니다.

    이 명령은 빈 결과 집합을 반환합니다. 아직 테이블이 없기 때문입니다. 테이블은 이 시리즈의 다음 문서에서 추가합니다.

## <a name="stop-and-restart-the-cluster"></a>클러스터 중지 및 다시 시작

비즈니스 요구에 따라 클러스터를 중지했다가 다시 시작할 수 있습니다.

1. 클러스터를 중지하려면 **개요** 탭의 맨 위에서 **중지**를 선택합니다.

    클러스터가 중지되면 쿼리에 데이터를 사용할 수 없고 새 데이터를 수집할 수 없습니다.

1. 클러스터를 다시 시작하려면 **개요** 탭의 맨 위에서 **시작**을 선택합니다.

    클러스터가 다시 시작되면 사용할 수 있게 되기까지(원래 프로비전된 시점과 같음) 약 10분이 걸립니다. 핫 캐시에 데이터를 로드하는 데 추가 시간이 필요합니다.  

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작 및 자습서를 진행하려는 경우 만든 리소스를 그대로 둡니다. 그렇지 않은 경우 비용이 발생하지 않도록 **test-resource-group**을 정리하십시오.

1. Azure Portal에서 맨 왼쪽에 있는 **리소스 그룹**을 선택한 다음, 만든 리소스 그룹을 선택합니다.  

    왼쪽 메뉴가 접혀 있으면 ![[확장] 단추를](media/create-cluster-database-portal/expand.png) 클릭하여 펼칩니다.

   ![삭제할 리소스 그룹 선택](media/create-cluster-database-portal/delete-resources-select.png)

1. **test-resource-group** 아래에서 **리소스 그룹 삭제**를 선택합니다.

1. 새 창에서 삭제할 리소스 그룹의 이름(*test-resource-group*)을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 이벤트 허브에서 Azure 데이터 탐색기로 데이터 수집](ingest-data-event-hub.md)


