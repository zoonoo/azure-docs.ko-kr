---
title: 서비스로 Azure Stack 유효성 검사를 사용 하 여 테스트를 모니터링 합니다. | Microsoft Docs
description: 서비스로 Azure Stack 유효성 검사를 사용 하 여 테스트를 모니터링 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 553d2a0e4bf6b23f5d8ab200f533d9245bf72d36
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286587"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>모니터 서비스로 Azure Stack 유효성 검사를 사용 하 여 테스트

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

확인 하 여 테스트 실행을 모니터링할 수 있습니다 합니다 **Operations** 진행에서 중이거나 완료 하는 테스트 도구 모음에 대 한 페이지입니다. 이 페이지에는 테스트 및 해당 작업의 상태를 자세히 설명합니다.

## <a name="monitor-a-test"></a>테스트를 모니터링 합니다.

1. 솔루션을 선택 합니다.

2. 선택 **관리** 워크플로 타일입니다.

3. 워크플로 해당 테스트 요약 페이지를 열려면 클릭 합니다.

4. 상황에 맞는 메뉴를 확장 **[...]**  에 대 한 suite 인스턴스를 테스트 합니다.

5. 선택 **작업 보기**

![대체 텍스트](media\image4.png)

실행 완료 된 테스트에 대 한 로그에서에서 다운로드할 수 있습니다 테스트 요약 페이지를 클릭 하 여 **로그를 다운로드할** 테스트 상황에 맞는 메뉴에서 **[...]** . Azure Stack 파트너 이러한 로그를 사용 하 여 실패 한 테스트에 대 한 문제를 디버깅할 수 있습니다.

## <a name="open-the-test-pass-summary"></a>테스트 통과 요약 열

1. 포털을 엽니다. 
2. 이전에 실행 또는 예약 된 테스트가 포함 된 기존 솔루션의 이름을 선택 합니다.

    ![테스트 통과 관리 합니다.](media/managetestpasses.png)

3. 선택 **관리** 에 **테스트 통과** 패널입니다.
4. 테스트 통과 테스트 통과 요약 열를 선택 합니다. 테스트 이름을 검토할 수 있습니다 만든 날짜, 시간 테스트 하는 결과 (성공 또는 실패)를 실행 합니다.
5. 선택 [ **...  .** ].

### <a name="test-pass-summary"></a>테스트 단계 요약

| 열 | 설명 |
| --- | --- |
| 테스트 이름 | 테스트의 이름입니다. 유효성 검사 수를 참조합니다. |
| 생성일 | 테스트 통과 만들어진 시간입니다. |
| Started | 이전 테스트 실행 시간입니다. |
| 기간 | 테스트 통과 실행 하는 데 걸린 시간 시간의 길이입니다. |
| 상태 | 결과 (성공 또는 실패) rest pass에 대 한 합니다. |
| 에이전트 이름 | 에이전트의 정규화 된 도메인 이름입니다. |
| 총작업 수 | 테스트 통과에서 시도 하는 작업의 총 수입니다. |
| 전달 된 작업 | 테스트 통과에 전달 하는 작업의 수입니다. |
|  실패 한 작업 | 실패 한 작업의 수입니다. |

### <a name="group-columns-in-the-test-pass-summary"></a>테스트에 그룹 열 요약 전달

선택한 열 머리글 열 값에서 그룹 만들기에 끌어다 놓습니다.

## <a name="reschedule-a-test"></a>테스트를 다시 예약

1. [테스트 통과 요약 열](#open-the-test-pass-summary)합니다.
2. 선택 **일정 변경** 테스트 통과 다시 예약 하 합니다.
3. Azure Stack 인스턴스에 대 한 클라우드 관리자 암호를 입력 합니다.
4. 사용자 계정을 설정할 때 정의한 진단 저장소 연결 문자열을 입력 합니다.
5. 테스트를 다시 예약 합니다.

## <a name="cancel-a-test"></a>테스트 취소

1. [테스트 통과 요약 열](#open-the-test-pass-summary)합니다.
2. **취소**를 선택합니다.

## <a name="get-test-information"></a>테스트 정보 가져오기

1. [테스트 통과 요약 열](#open-the-test-pass-summary)합니다.
2. 선택 **정보를 보려면** 테스트 통과 다시 예약 하 합니다.

**테스트 정보**

| 이름 | 설명 |
| -- | -- |
| 테스트 이름 | Azure Stack 1806 RC 유효성 검사에 대 한 예를 들어, OEM 업데이트 하는 테스트의 이름입니다. |
| 테스트 버전 | 예를 들어 5.1.4.0 테스트의 버전입니다. |
| 게시자 | Microsoft와 같은 테스트 게시자입니다. |
| Category | 테스트의 범주와 같은 **기능** 하거나 **안정성**합니다. |
| 대상 서비스 | VirtualMachines 같은 테스트 중인 서비스 |
| 설명 | 테스트의 설명입니다. |
| 예상된 기간 (분) | 길이 (분) 테스트를 실행 하는 데 걸린 시간입니다. |
| 링크 | GitHub 문제 추적기에 대 한 링크입니다. |

## <a name="get-test-parameters"></a>테스트 매개 변수 가져오기

1. [테스트 통과 요약 열](#open-the-test-pass-summary)합니다.
2. 선택 **매개 변수를 보려면** 테스트 통과 다시 예약 하 합니다.

**매개 변수**

| 이름 | 설명 |
| -- | -- |
| 테스트 이름 | 예를 들어 oemupdate1806test 테스트의 이름입니다. |
| 테스트 버전 | 예를 들어 5.1.4.0 나머지 버전입니다. |
| 테스트 인스턴스 ID | 예를 들어, 테스트의 특정 인스턴스를 식별 하는 GUID 20b20645-b400-4f0d-bf6f-1264d866ada9 합니다. |
| cloudAdminUser | 예를 들어, 클라우드 관리자를 사용 하는 계정의 이름을 **cloudadmin**합니다. |
| DiagnosticsContainerName | 예를 들어 진단 컨테이너의 ID 04dd3815-5f35-4158-92ea-698027693080 합니다. |

## <a name="get-test-operations"></a>테스트 작업을 가져옵니다.

1. [테스트 통과 요약 열](#open-the-test-pass-summary)합니다.
2. 선택 **작업 보기** 테스트 통과 다시 예약 하 합니다. 작업 요약 창이 열립니다.

## <a name="get-test-logs"></a>테스트 로그 가져오기

1. [테스트 통과 요약 열](#open-the-test-pass-summary)합니다.
2. 선택 **로그를 다운로드할** 테스트 통과 다시 예약 하 합니다.  
    Zip 파일 이라는 ReleaseYYYY-MM-DD.zip 로그 다운로드를 포함 합니다.

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
