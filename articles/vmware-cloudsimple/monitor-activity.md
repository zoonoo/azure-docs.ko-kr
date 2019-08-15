---
title: CloudSimple-사설 클라우드 모니터링 작업을 통한 Azure VMware 솔루션
description: 경고, 이벤트, 작업 및 감사를 포함 하 여 CloudSimple 환경의 Azure VMware 솔루션 작업에서 사용할 수 있는 정보에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c17bd203b7843de64734a74e7e41a22e42e3501
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991002"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>CloudSimple 활동 별로 VMware 솔루션 모니터링
 
CloudSimple 활동 로그는 CloudSimple 포털에서 수행 된 작업에 대 한 통찰력을 제공 합니다.  이 목록에는 경고, 이벤트, 태스크 및 감사가 포함 되어 있습니다.  활동 로그를 사용 하 여 수행 된 작업과 작업을 결정 합니다.  활동 로그에는 사용자가 수행 하는 읽기 작업이 포함 되지 않습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

[Cloudsimple 포털](access-cloudsimple-portal.md)에 액세스 합니다.

## <a name="activity-information"></a>활동 정보

작업 페이지에 액세스 하려면 측면 메뉴에서 **작업** 을 선택 합니다.

![작업 페이지 개요](media/activity-page-overview.png)

작업 페이지에서 작업에 대 한 세부 정보를 보려면 작업을 선택 합니다. 세부 정보 패널이 오른쪽에 열립니다. 패널의 작업은 활동의 유형에 따라 달라 집니다. **X** 를 클릭 하 여 패널을 닫습니다.

표시를 정렬 하려면 열 머리글을 클릭 합니다.  보려는 특정 값의 열을 필터링 할 수 있습니다.  **CSV로 다운로드** 아이콘을 클릭 하 여 작업 보고서를 다운로드 합니다.

## <a name="alerts"></a>,
 
경고는 CloudSimple 환경에서 중요 한 작업에 대 한 알림입니다.  경고에는 청구 또는 사용자 액세스에 영향을 주는 이벤트가 포함 됩니다.

경고를 승인 하 고 목록에서 제거 하려면 목록에서 하나 이상을 선택 하 고 **승인**을 클릭 합니다. 

경고에는 다음 정보 열을 사용할 수 있습니다. **열 편집** 을 클릭 하 고 보려는 열을 선택 합니다.

| Column | Description | 
------------ | ------------- | 
| 경고 유형 | 경고의 범주입니다.|
| 시간 | 경고가 발생 한 시간입니다. |
| Severity | 경고의 의미입니다.|
| 리소스 이름 | 리소스에 할당 된 이름입니다 (예: 사설 클라우드 이름). | 
| 리소스 종류 | 리소스 범주: 사설 클라우드, 클라우드 랙. |
| 리소스 ID | 리소스의 식별자입니다. |
| Description | 경고를 트리거한 항목에 대 한 설명입니다. |
| 확인됨 | 경고가 승인 되었는지 여부를 나타냅니다. |

## <a name="events"></a>이벤트

이벤트는 CloudSimple 포털에서 사용자 및 시스템 작업을 표시 합니다. 이벤트 페이지에는 특정 리소스와 연결 된 작업과 영향의 심각도가 나열 됩니다. 

경고에는 다음 정보 열을 사용할 수 있습니다. **열 편집** 을 클릭 하 고 보려는 열을 선택 합니다.

| Column | 설명 | 
------------ | ------------- | 
| 시간 | 이벤트가 발생 한 날짜 및 시간입니다. |
| 이벤트 유형 | 이벤트를 식별 하는 숫자 코드입니다. |
| Severity | 이벤트 심각도.|
| 리소스 이름 | 리소스에 할당 된 이름입니다 (예: 사설 클라우드 이름). | 
| 리소스 종류 | 리소스 범주: 사설 클라우드, 클라우드 랙. |
| Description | 경고를 트리거한 항목에 대 한 설명입니다. |

## <a name="tasks"></a>태스크

작업은 완료 하는 데 30 초 이상이 소요 되는 사설 클라우드 작업입니다. 30 초 이내에 소요 되는 활동은 이벤트만 보고 됩니다. 작업 페이지를 열어 사설 클라우드의 작업 진행률을 추적 합니다. 

경고에는 다음 정보 열을 사용할 수 있습니다. **열 편집** 을 클릭 하 고 보려는 열을 선택 합니다.

| Column | Description | 
------------ | ------------- | 
| 작업 ID | 태스크에 대 한 고유 식별자입니다. |
| 연산 | 태스크에서 수행 하는 작업입니다. |
| 사용자 | 작업을 완료 하기 위해 할당 된 사용자입니다. |
| 리소스 이름 | 리소스에 할당 된 이름입니다. |
| 리소스 종류 | 리소스 범주: 사설 클라우드, 클라우드 랙. |
| 리소스 ID | 리소스의 식별자입니다. |
| 시작 | 작업의 시작 시간입니다. |
| 종료 | 작업의 종료 시간입니다. |
| Status | 현재 작업 상태입니다. |
| 경과 시간 | 작업을 완료 하는 데 걸린 시간 (완료 된 경우) 또는 현재 진행 중인 작업 (진행 중인 경우)입니다. |
| Description | 작업 설명입니다. |

## <a name="audit"></a>감사

감사 로그는 사용자 작업을 추적 합니다. 감사 로그를 사용 하 여 모든 사용자에 대 한 사용자 활동을 모니터링할 수 있습니다. 

경고에는 다음 정보 열을 사용할 수 있습니다. **열 편집** 을 클릭 하 고 보려는 열을 선택 합니다.

| Column | Description | 
------------ | ------------- | 
| 시간 | 감사 항목의 시간입니다. |
| 연산 | 태스크에서 수행 하는 작업입니다. |
| 사용자 | 작업에 할당 된 사용자입니다. |
| 리소스 이름 | 리소스에 할당 된 이름입니다. |
| 리소스 종류 | 리소스 범주: 사설 클라우드, 클라우드 랙. |
| 리소스 ID | 리소스의 식별자입니다. |
| 결과 | **성공**과 같은 작업의 결과입니다. |
| 소요된 시간 | 작업을 완료 하는 데 걸리는 시간입니다. |
| Description | 동작에 대 한 설명입니다. |

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* [사설 클라우드에](cloudsimple-private-cloud.md) 대 한 자세한 정보
