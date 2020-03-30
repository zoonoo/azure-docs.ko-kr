---
title: 마이크로소프트 Azure 데이터 탐색기 흐름 커넥터(미리 보기)
description: Microsoft Flow 커넥터를 사용하여 자동으로 예약되거나 트리거된 작업의 흐름을 만드는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 7745888dcaa1324d4a9d956e93d0504c8da8c026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501778"
---
# <a name="microsoft-flow-connector-preview"></a>마이크로소프트 흐름 커넥터 (미리 보기)

Azure 데이터 탐색기 흐름 커넥터를 사용하면 Azure Data Explorer에서 [Microsoft Power Automate의 흐름 기능을](https://flow.microsoft.com/) 사용하여 예약되거나 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행할 수 있습니다.

일반적인 사용 시나리오는 다음과 같습니다.

* 테이블및차트가 포함된 일일 보고서 보내기
* 쿼리 결과에 따라 알림 설정
* 클러스터에서 제어 명령 예약
* Azure 데이터 탐색기와 다른 데이터베이스 간에 데이터 내보내기 및 가져오기 

자세한 내용은 [Microsoft Flow 커넥터 사용 예제를](flow-usage.md)참조하십시오.

##  <a name="log-in"></a>로그인 

1. [마이크로 소프트 파워 자동화에](https://flow.microsoft.com/)로그인 .

1. 처음 연결할 때 로그인하라는 메시지가 표시됩니다.

1. **로그인**을 선택하고 자격 증명을 입력합니다.

![로그인 대화 상자](./media/flow/flow-signin.png)

## <a name="authentication"></a>인증

사용자 자격 증명 또는 AAD 응용 프로그램을 사용 하 고 인증할 수 있습니다.

> [!Note]
> 응용 프로그램이 [AAD 응용 프로그램이며](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) 클러스터에서 쿼리를 실행할 권한이 있는지 확인합니다.

1. Microsoft 흐름 커넥터의 오른쪽 상단에 있는 세 개의 ![점 선택: 연결 추가](./media/flow/flow-addconnection.png)

1. **새 연결 추가를** 선택한 다음 **서비스 주체와 연결을 선택합니다.**
![로그인 대화 상자](./media/flow/flow-signin.png)

1. 필요한 정보를 입력합니다.
    * 연결 이름: 새 연결에 대한 설명적이고 의미 있는 이름
    * 클라이언트 ID: 응용 프로그램 ID
    * 클라이언트 보안: 응용 프로그램 키
    * 테넌트: 응용 프로그램을 만든 AAD 디렉터리ID입니다. 예를 들어 Microsoft 테넌트 ID는 다음과 같다: 72f988bf-86f1-41af-91ab-2d7cd011db47

![애플리케이션 인증](./media/flow/flow-appauth.png)

인증이 완료되면 흐름에서 새로 추가된 연결을 사용하는 것을 볼 수 있습니다.

![응용 프로그램 인증 완료](./media/flow/flow-appauthcomplete.png)

이제부터는 이러한 응용 프로그램 자격 증명을 사용하여 이 흐름이 실행됩니다.

## <a name="find-the-azure-kusto-connector"></a>Azure Kusto 커넥터 찾기

Microsoft 흐름 커넥터를 사용하려면 먼저 트리거를 추가해야 합니다. 트리거는 되풀이 기간 또는 이전 흐름 작업에 대한 응답으로 정의할 수 있습니다.

1. [새 흐름을 만들거나](https://flow.microsoft.com/manage/flows/new) 홈 페이지에서 내 **흐름** 작업을 선택한 다음 **+ 새**을 선택합니다.

    ![새 흐름 만들기](./media/flow/flow-newflow.png)

1. 공백에서 예약된 추가합니다.

    ![새 예약흐름](./media/flow/flow-scheduled-from-blank.png)

1. 예약된 흐름 빌드 페이지에 필요한 정보를 입력합니다.
    ![예약된 흐름 빌드](./media/flow/flow-build-scheduled-flow.png)
1. **만들기**를 선택합니다.
1. **+ 새 단계**를 선택합니다.
1. 검색 상자에 "Kusto"를 입력합니다.

    ![흐름 작업 선택](./media/flow/flow-actions.png)

1. **Azure 데이터 탐색기를**선택합니다.

## <a name="flow-actions"></a>흐름 작업

Azure 데이터 탐색기 커넥터를 열면 흐름에 추가할 수 있는 세 가지 작업이 있습니다.

이 섹션에서는 각 Microsoft 흐름 작업에 대한 기능 및 매개 변수에 대해 설명합니다.

![흐름 Azure 데이터 탐색기 작업](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>제어 명령을 실행하고 결과를 시각화합니다.

컨트롤 실행 명령을 사용하고 결과 작업을 시각화하여 [제어 명령을 실행합니다.](https://docs.microsoft.com/azure/kusto/management/index)

1. 클러스터 URL을 지정합니다. 예를 들어 https://clusterName.eastus.kusto.windows.net
1. 데이터베이스의 이름을 입력합니다.
1. 제어 명령을 지정합니다.
    * 흐름에 사용되는 앱 및 커넥터에서 동적 콘텐츠 선택
    * 값에 액세스, 변환 및 비교할 식 추가
1. 이 작업의 결과를 전자 메일로 테이블 또는 차트로 보내려면 다음과 같은 차트 유형을 지정합니다.
    * HTML 테이블
    * 원형 차트
    * 시간 표
    * 막대형 차트

![흐름 제어 명령 실행](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> 클러스터 *이름* 필드에 클러스터 URL을 입력합니다.

### <a name="run-query-and-list-results"></a>쿼리 및 목록 결과 실행

> [!Note]
> 쿼리가 점으로 시작하는 경우(제어 [명령즉),](https://docs.microsoft.com/azure/kusto/management/index)컨트롤 [실행 명령을](#run-control-command-and-visualize-results) 사용하고 결과를 시각화합니다.

이 작업은 Kusto 클러스터에 쿼리를 보냅니다. 이후에 추가되는 작업은 쿼리 결과의 각 줄을 반복합니다.

다음 예제는 1분마다 쿼리를 트리거하고 쿼리 결과를 기반으로 전자 메일을 보냅니다. 쿼리는 데이터베이스의 줄 수를 확인한 다음 줄 수가 0보다 큰 경우에만 전자 메일을 보냅니다. 

![쿼리 목록 결과 실행](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> 열에 여러 줄이 있는 경우 연결선이 열의 각 줄에 대해 실행됩니다.

### <a name="run-query-and-visualize-results"></a>쿼리 실행 및 결과 시각화
        
> [!Note]
> 쿼리가 점으로 시작하는 경우(제어 [명령즉),](https://docs.microsoft.com/azure/kusto/management/index)컨트롤 [실행 명령을](#run-control-command-and-visualize-results) 사용하고 결과를 시각화합니다.
        
실행 쿼리를 사용하고 결과 작업을 시각화하여 Kusto 쿼리 결과를 테이블 또는 차트로 시각화합니다. 예를 들어 이 흐름을 사용하여 전자 메일로 매일 ICM 보고서를 수신합니다. 
    
이 예제에서는 쿼리 결과가 HTML 테이블로 반환됩니다.
            
![쿼리 실행 및 결과 시각화](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> 클러스터 *이름* 필드에 클러스터 URL을 입력합니다.

## <a name="email-kusto-query-results"></a>이메일 쿠스토 쿼리 결과

모든 전자 메일 주소로 전자 메일로 보고서를 보내는 모든 흐름에 단계를 포함할 수 있습니다. 

1. **+ 새 단계를** 선택하여 흐름에 새 단계를 추가합니다.
1. 검색 필드에서 Office 365를 입력하고 **Office 365 Outlook을**선택합니다.
1. **이메일 보내기**를 선택합니다.
1. 이메일 보고서를 보낼 위치에 이메일 주소를 입력합니다.
1. 이메일의 제목을 입력합니다.
1. *[몸체]* 필드에서 동적 콘텐츠 필드에서 **Body**를 선택합니다.
1. **고급 옵션 표시**를 선택합니다.
1. 첨부 *파일 이름 -1* 필드에서 **첨부 파일 이름을**선택합니다.
1. 첨부 *파일 콘텐츠* 필드에서 **첨부 파일 컨텐트를**선택합니다.
1. 필요한 경우 중요도 수준을 설정합니다.
1. **저장**을 선택합니다.

![메일 보내기](./media/flow/flow-sendemail.png)

## <a name="check-if-your-flow-succeeded"></a>흐름이 성공했는지 확인

흐름이 성공했는지 확인하려면 흐름의 실행 기록을 참조하세요.
1. [마이크로소프트 플로우 홈 페이지로](https://flow.microsoft.com/)이동합니다.
1. 기본 메뉴에서 [내 흐름을](https://flow.microsoft.com/manage/flows)선택합니다.
    ![내 흐름 페이지](./media/flow/flow-myflows.png)
1. 조사하려는 흐름 행에서 더 많은 명령 아이콘을 선택한 다음 **기록 을 실행합니다.**

    ![기록 메뉴 실행](./media/flow//flow-runhistory.png)

    모든 흐름 실행은 시작 시간, 기간 및 상태로 나열됩니다.
    ![기록 결과 페이지 실행](./media/flow/flow-runhistoryresults.png)

    흐름에 대한 자세한 내용을 보려면 [My 흐름](https://flow.microsoft.com/manage/flows) 페이지에서 조사할 흐름을 선택합니다.

    ![실행 기록 전체 결과 페이지](./media/flow/flow-fulldetails.png) 

실행에 실패한 이유를 확인하려면 실행 시작 시간을 선택합니다. 흐름이 나타나고 실패한 흐름의 단계는 빨간색 느낌표로 표시됩니다. 실패한 단계를 확장하여 세부 정보를 봅니다. 오른쪽 창에는 오류에 대한 정보가 포함되어 있으므로 문제를 해결할 수 있습니다.

![흐름 오류](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>시간 시간 시간 예외

흐름이 7분 이상 실행되는 경우 "RequestTimeout" 예외가 실패하고 반환될 수 있습니다.

[마이크로소프트 흐름 제한에](#limitations)대 한 자세한 내용은 .
    
시간이 제한되지 않고 변경할 수 있는 Azure Data Explorer에서 동일한 쿼리가 성공적으로 실행될 수 있습니다.
            
"요청 시간 표시" 예외는 아래 이미지에 나와 있습니다.
    
![흐름 요청 시간 시간 시간 예외 오류](./media/flow/flow-requesttimeout.png)
    
시간 초과 문제를 해결하려면 쿼리가 더 빠르게 실행되도록 보다 효율적으로 만들거나 청크로 구분하십시오. 각 청크는 쿼리의 다른 부분에서 실행할 수 있습니다.

자세한 내용은 쿼리 [모범 사례에](https://docs.microsoft.com/azure/kusto/query/best-practices)대해 읽어보십시오.

## <a name="limitations"></a>제한 사항

* 클라이언트에 반환된 결과는 500,000개의 레코드로 제한됩니다. 이러한 레코드의 전체 메모리는 64MB 및 7분 실행 시간을 초과할 수 없습니다.
* 커넥터는 [포크](https://docs.microsoft.com/azure/kusto/query/forkoperator) 및 [면](https://docs.microsoft.com/azure/kusto/query/facetoperator) 연산자도 지원하지 않습니다.
* 흐름은 마이크로 소프트 에지와 크롬에서 가장 잘 작동합니다.

## <a name="next-steps"></a>다음 단계

예약되거나 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행하는 또 다른 [방법입니다.](https://docs.microsoft.com/azure/kusto/tools/logicapps)
