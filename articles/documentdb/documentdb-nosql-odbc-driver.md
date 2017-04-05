---
title: "BI 분석 도구를 사용하여 Azure DocumentDB에 연결 | Microsoft Docs"
description: "BI 및 데이터 분석 소프트웨어에서 정규화된 데이터를 볼 수 있도록 Azure DocumentDB ODBC 드라이버를 사용하여 테이블 및 뷰를 만드는 방법을 알아봅니다."
keywords: "odbc, odbc 드라이버"
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/27/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 5f712c7fa9b6ee06f7c89de40ba4227a925a35ce
ms.lasthandoff: 03/28/2017


---

# <a name="connect-to-documentdb-using-bi-analytics-tools-with-the-odbc-driver"></a>ODBC 드라이버와 함께 BI 분석 도구를 사용하여 DocumentDB에 연결

DocumentDB ODBC 드라이버를 사용하면 SQL Server Integration Services, Power BI Desktop 및 Tableau와 같은 BI 분석 도구를 사용하여 DocumentDB를 연결할 수 있으므로 해당 솔루션에서 DocumentDB 데이터 시각화를 분석하고 만들 수 있습니다.

DocumentDB ODBC 드라이버는 ODBC 3.8을 준수하며 ANSI SQL-92 구문을 지원합니다. 이 드라이버는 DocumentDB에서 데이터를 다시 정규화하는 데 도움이 되는 다양한 기능을 제공합니다. 이 드라이버를 사용하여 DocumentDB의 데이터를 테이블 및 뷰로 나타낼 수 있습니다. 이 드라이버를 사용하면 테이블 및 뷰에 대해 쿼리별 그룹화, 삽입, 업데이트 및 삭제 등의 SQL 작업을 수행할 수 있습니다.

## <a name="why-do-i-need-to-normalize-my-data"></a>데이터를 정규화해야 하는 이유는 무엇인가요?
DocumentDB는 NoSQL 데이터베이스이므로 응용 프로그램이 즉석에서 해당 데이터 모델을 반복하고, 엄격한 스키마로 제한하지 않도록 함으로써 앱을 빠르게 개발할 수 있도록 합니다. 단일 DocumentDB 데이터베이스에는 다양한 구조의 JSON 문서가 포함될 수 있습니다. 이러한 특징은 신속한 응용 프로그램 개발에 유용하지만 데이터 분석 및 BI 도구를 사용하여 데이터 보고서를 분석하고 만들려는 경우 데이터를 평면화하고 특정 스키마를 따르도록 할 필요가 있습니다.

바로 이러한 경우 ODBC 드라이버가 사용됩니다. 이제 ODBC 드라이버를 사용하여 DocumentDB의 데이터를 데이터 분석 및 보고 요구에 맞는 테이블 및 뷰로 다시 정규화할 수 있습니다. 다시 정규화된 스키마는 기본 데이터에 아무런 영향도 미치지 않고 개발자도 이러한 스키마를 준수할 필요가 없으며, 사용자는 ODBC 호환 도구를 사용하여 데이터에 액세스할 수 있습니다. 이제 DocumentDB 데이터베이스는 개발 팀에서 선호될 뿐 아니라 데이터 분석에서도 많이 사용될 것입니다.

다음에는 ODBC 드라이버를 살펴보겠습니다.

## <a id="install"></a>1단계: DocumentDB ODBC 드라이버 설치

1. 다음 중에서 환경에 맞는 드라이버를 다운로드합니다.

    * Windows 64비트용 [Microsoft Azure DocumentDB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64)
    * Windows 32비트 또는 64비트용 [Microsoft Azure DocumentDB ODBC 32x64-bit.msi](https://aka.ms/documentdb-odbc-32x64)
    * Windows 32비트용 [Microsoft Azure DocumentDB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32)

    msi 파일을 로컬로 실행합니다. 그러면 **Microsoft Azure DocumentDB ODBC 드라이버 설치 마법사**가 시작됩니다. 
2. 기본 입력으로 ODBC 드라이버를 설치하여 설치 마법사를 완료합니다.
3. Windows 검색 상자에 **ODBC 데이터 원본**을 입력하여 컴퓨터에서 **ODBC 데이터 원본 관리자** 앱을 엽니다. 
    **드라이버** 탭을 클릭하고 **Microsoft DocumentDB ODBC 드라이버**가 표시되는지 확인하여 이 드라이버가 설치되어 있는지 확인할 수 있습니다.

    ![DocumentDB OBDC 데이터 원본 관리자](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver.png)

## <a id="connect"></a>2단계: DocumentDB 데이터베이스에 연결

1. [DocumentDB ODBC 드라이버 설치](#install) 후에 **ODBC 데이터 원본 관리자** 창에서 **추가**를 클릭합니다. 사용자 또는 시스템 DSN을 만들 수 있습니다. 이 예제에서는 사용자 DSN을 만듭니다.
2. **새 데이터 원본 만들기** 창에서 **Microsoft DocumentDB ODBC 드라이버**를 선택하고 **마침**을 클릭합니다.
3. **DocumentDB ODBC 드라이버 SDN 설정** 창에서 다음을 입력합니다. 

    ![DocumentDB ODBC 드라이버 DSN 설정 창](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-dsn-setup.png)
    - **데이터 원본 이름**: ODBC DSN에 대한 고유한 이름입니다 이 이름은 DocumentDB 계정에 고유하므로 여러 계정이 있는 경우에 적절히 이름을 지정합니다.
    - **설명**: 데이터 원본에 대한 짧은 설명입니다.
    - **호스트**: DocumentDB 계정에 대한 URI입니다. 다음 스크린샷처럼 Azure Portal의 DocumentDB 키 블레이드에서 검색할 수 있습니다. 
    - **액세스 키**: 다음 스크린샷에 표시된 대로 Azure Portal의 DocumentDB 키 블레이드에 있는 기본 또는 보조, 읽기-쓰기 또는 읽기 전용 키입니다. DSN가 읽기 전용 데이터 처리 및 보고에 사용되는 경우 읽기 전용 키를 사용하는 것이 좋습니다.
    ![DocumentDB 키 블레이드](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-keys.png)
    - **액세스 키 암호화**: 이 컴퓨터의 사용자 수에 따라 가장 적합한 옵션을 선택합니다. 
4. DocumentDB 계정에 연결할 수 있는지 확인하려면 **테스트** 단추를 클릭합니다. 
5. **고급 옵션**을 클릭하고 다음 값을 설정합니다.
    - **쿼리 일관성**: 작업에 대해 [일관성 수준](documentdb-consistency-levels.md)을 선택합니다. 기본값은 세션입니다.
    - **재시도 횟수**: 초기 요청이 서비스 조정으로 인해 완료되지 않은 경우 작업을 다시 시도할 횟수를 입력합니다.
    - **스키마 파일**: 다양한 옵션이 있습니다.
        - 기본적으로 이 항목을 있는 그대로(비어 있음) 두면 드라이버는 첫 번째 페이지 데이터에서 모든 컬렉션을 검색하여 각 컬렉션의 스키마를 확인합니다. 이 작업을 컬렉션 매핑이라고 합니다. 정의된 스키마 파일이 없이 경우 이 드라이버는 각 드라이버 세션을 검색하므로, DSN을 사용하는 응용 프로그램의 시작 시간이 더 늘어날 수 있습니다. 따라서 DSN에 대한 스키마 파일을 항상 연결하는 것이 좋습니다.
        - 스키마 파일이 이미 있는 경우([스키마 편집기](#schema-editor)를 사용하여 만들었을 수 있음) **찾아보기**를 클릭하고 해당 파일로 이동한 후 **저장**을 클릭하고 **확인**을 클릭합니다.
        - 새 스키마를 만들려면 **확인**을 클릭하고 주 창에서 **스키마 편집기**를 클릭합니다. 그런 후 [스키마 편집기](#schema-editor) 정보로 이동합니다. 새 스키마 파일을 만들 때 **고급 옵션** 창으로 돌아가 새로 만든 스키마 파일을 포함해야 합니다.

6. 작업을 완료하고 **DocumentDB ODBC 드라이버 DSN 설정** 창을 닫으면 새 사용자 DSN가 사용자 DSN 탭에 추가됩니다.

    ![사용자 DSN 탭의 새 DocumentDB ODBC DSN](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>3단계: 컬렉션 매핑 방법을 사용하여 스키마 정의 만들기

**컬렉션 매핑** 또는 **테이블 구분 기호**의 두 가지 유형의 샘플링 방법을 사용할 수 있습니다. 샘플링 세션은 두 가지 샘플링 방법을 모두 사용할 수 있지만 각 컬렉션은 특정 샘플링 방법만 사용할 수 있습니다. 아래 단계는 컬렉션 매핑 방법을 사용하여 하나 이상의 컬렉션에서 데이터에 대한 스키마를 만듭니다. 이 샘플링 방법은 컬렉션 페이지에서 데이터를 검색하여 데이터의 구조를 확인합니다. 그런 후 컬렉션을 ODBC 쪽의 테이블로 바꿉니다. 이 샘플링 방법은 컬렉션의 데이터가 동일한 형식일 때 효율적이고 빠릅니다. 컬렉션에 형식이 다른 데이터가 포함되어 있을 때는 [테이블 구분 기호 매핑 방법](#table-mapping)을 사용하는 것이 좋습니다. 이 방법이 컬렉션의 데이터 구조를 확인하는 보다 강력한 샘플링 방법을 제공하기 때문입니다. 

1. [DocumentDB 데이터베이스에 연결](#connect)의 1-4단계를 완료한 후 **DocumentDB ODBC 드라이버 DSN 설정** 창에서 **스키마 편집기**를 클릭합니다.

    ![DocumentDB ODBC 드라이버 DSN 설정 창의 스키마 편집기 단추](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-schema-editor.png)
2. **스키마 편집기** 창에서 **새로 만들기**를 클릭합니다.
    **스키마 생성** 창에는 DocumentDB 계정의 모든 컬렉션이 표시됩니다. 
3. 샘플링할 하나 이상의 컬렉션을 선택하고 **샘플**을 클릭합니다. 
4. **디자인 보기** 탭에 데이터베이스, 스키마 및 테이블이 표시됩니다. 테이블 보기에서 검색을 수행하면 열 이름(예: SQL 이름, 원본 이름 등)과 관련된 속성 집합이 표시됩니다.
    각 열에 대해 SQL 열 이름, SQL 형식, SQL 길이(해당되는 경우), 소수 자릿수(해당되는 경우), 전체 자릿수(해당되는 경우) 및 null 허용을 수정할 수 있습니다.
    - 쿼리 결과에서 해당 열을 제외하려면 **Hide Column**을 **true**로 설정합니다. Hide Column = true로 표시된 열은 스키마의 일부이지만 선택 및 프로젝션의 경우에는 반환되지 않습니다. 예를 들어, "_"로 시작하는 모든 DocumentDB 시스템 필수 속성을 숨길 수 있습니다.
    - **id** 열은 정규화된 스키마에서 기본 키로 사용되기 때문에 숨길 수 없는 유일한 필드입니다. 
5. 스키마 정의를 끝낸 후에 **파일** | **저장**을 클릭한 후 스키마를 저장할 디렉터리로 이동하고 **저장**을 클릭합니다.

    나중에 DSN과 함께 이 스키마를 사용하려면 DocumentDB ODBC 드라이버 DNS 설정 창을 열고(ODBC 데이터 원본 관리자 사용) 고급 옵션을 클릭한 후 스키마 파일 상자에서 저장된 스키마로 이동합니다. 스키마 파일을 기존 DSN에 저장하면 해당 스키마로 정의된 데이터 및 구조로 범위가 지정되도록 DSN 연결이 수정됩니다.

## <a id="table-mapping"></a>4단계: 테이블 구분 기호 매핑 방법을 사용하여 스키마 정의 만들기

**컬렉션 매핑** 또는 **테이블 구분 기호**의 두 가지 유형의 샘플링 방법을 사용할 수 있습니다. 샘플링 세션은 두 가지 샘플링 방법을 모두 사용할 수 있지만 각 컬렉션은 특정 샘플링 방법만 사용할 수 있습니다. 

다음 단계는 **테이블 구분 기호** 매핑 방법을 사용하여 하나 이상의 컬렉션에 있는 데이터에 대한 스키마를 만듭니다. 컬렉션에 형식이 다른 데이터가 포함되어 있으면 이 샘플링 방법을 사용하는 것이 좋습니다. 이 방법을 사용하여 샘플링 범위를 특성 집합 및 해당 값으로 지정할 수 있습니다. 예를 들어 문서에 "Type" 속성이 포함된 경우 샘플링 범위를 이 속성의 값으로 지정할 수 있습니다. 샘플링의 최종 결과는 지정한 Type의 각 값에 대한 테이블 집합입니다. 예를 들어, Type = Car를 지정하면 Car 테이블이 생성되지만 Type = Plane을 지정하면 Plane 테이블이 생성됩니다.

1. [DocumentDB 데이터베이스에 연결](#connect)의 1-4단계를 완료한 후 DocumentDB ODBC 드라이버 DSN 설정 창에서 **스키마 편집기**를 클릭합니다.
2. **스키마 편집기** 창에서 **새로 만들기**를 클릭합니다.
    **스키마 생성** 창에는 DocumentDB 계정의 모든 컬렉션이 표시됩니다. 
3. **샘플 보기** 탭에서 컬렉션을 선택하고 컬렉션에 대한 **매핑 정의** 열에서 **편집**을 클릭합니다. 그런 다음 **매핑 정의** 창에서 **테이블 구분 기호** 방법을 선택합니다. 그런 다음 아래 작업을 수행합니다.

    a. **특성** 상자에 구분 기호 속성의 이름을 입력합니다. 이것은 문서에서 샘플링 범위로 지정하려는 속성(예: City)입니다. 그런 후 Enter 키를 누릅니다. 

    b. 방금 입력한 특성에 대한 특정 값으로 샘플링 범위를 지정하려는 경우 선택 상자에서 해당 특성을 선택하고 **값** 상자에 값(예: Seattle)을 입력하고 Enter 키를 누릅니다. 특성에 대해 여러 값을 계속 추가할 수 있습니다. 값을 입력할 때 올바른 속성이 선택되어 있는지 확인하기만 하면 됩니다.

    예를 들어 **특성** 값으로 City를 포함하고 city 값이 New York과 Dubai인 행만 포함하도록 테이블을 제한하려면 특성 상자에 City를 입력하고 **값** 상자에 New York을 입력한 후 Dubai를 입력합니다.
4. **확인**을 클릭합니다. 
5. 샘플링하려는 컬렉션에 대한 매핑 정의를 완료한 후 **스키마 편집기** 창에서 **샘플**을 클릭합니다.
     각 열에 대해 SQL 열 이름, SQL 형식, SQL 길이(해당되는 경우), 소수 자릿수(해당되는 경우), 전체 자릿수(해당되는 경우) 및 null 허용을 수정할 수 있습니다.
    - 쿼리 결과에서 해당 열을 제외하려면 **Hide Column**을 **true**로 설정합니다. Hide Column = true로 표시된 열은 스키마의 일부이지만 선택 및 프로젝션의 경우에는 반환되지 않습니다. 예를 들어, "_"로 시작하는 모든 DocumentDB 시스템 필수 속성을 숨길 수 있습니다.
    - **id** 열은 정규화된 스키마에서 기본 키로 사용되기 때문에 숨길 수 없는 유일한 필드입니다. 
6. 스키마 정의를 끝낸 후에 **파일** | **저장**을 클릭한 후 스키마를 저장할 디렉터리로 이동하고 **저장**을 클릭합니다.
7. **DocumentDB ODBC 드라이버 DSN 설정** 창으로 돌아가 **고급 옵션**을 클릭합니다. 그런 다음 **스키마 파일** 상자에서 저장된 스키마 파일로 이동한 후 **확인**을 클릭합니다. **확인**을 다시 클릭하여 DSN을 저장합니다. 이렇게 하면 만든 스키마가 DSN에 저장됩니다. 

## <a name="optional-creating-views"></a>(선택 사항) 뷰 만들기
샘플링 프로세스의 일부로 뷰를 정의하고 만들 수 있습니다. 이러한 뷰는 SQL 뷰와 비슷합니다. 읽기 전용이며, 정의된 DocumentDB SQL의 선택 및 프로젝션으로 범위가 지정됩니다. 

데이터에 대한 뷰를 만들려면 **스키마 편집기** 창의 **뷰 정의** 열에서 샘플링할 컬렉션 행에 대해 **추가**를 클릭합니다. 그런 다음 **뷰 정의** 창에서 다음을 수행합니다.
1. **새로 만들기**를 클릭하고 뷰 이름(예: EmployeesfromSeattleView)을 입력하고 **확인**을 클릭합니다.
2. **뷰 편집** 창에서 DocumentDB 쿼리를 입력합니다. DocumentDB SQL 쿼리(예: `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`)여야 합니다. 그런 후 **확인**을 클릭합니다.

원하는 수만큼 뷰를 만들 수 있습니다. 뷰 정의가 끝나면 데이터를 샘플링할 수 있습니다. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>5단계: Power BI Desktop과 같은 BI 도구에서 데이터 보기

새 DSN을 사용하여 DocumentADB를 ODBC 호환 도구에 연결할 수 있습니다. 이 단계에서는 Power BI Desktop에 연결하고 Power BI 시각화를 만드는 방법을 보여 줍니다.

1. Power BI Desktop을 엽니다.
2. **데이터 가져오기**를 클릭합니다.
3. **데이터 가져오기** 창에서 **기타** | **ODBC** | **연결**을 클릭합니다.
4. **ODBC에서** 창에서 만든 데이터 원본 이름을 선택하고 **확인**을 클릭합니다. **고급 옵션** 항목은 빈 상태로 두어도 됩니다.
5. **ODBC 드라이버를 사용하여 데이터 원본에 액세스합니다.** 창에서 **기본 또는 사용자 지정**을 클릭하고 **연결**을 클릭합니다. **자격 증명 연결 문자열 속성**은 포함하지 않아도 됩니다.
6. **탐색 창**의 왼쪽 분할 창에서 데이터베이스, 스키마를 확장한 다음 테이블을 선택합니다. 결과 분할 창에는 만든 스키마를 사용하여 해당 데이터가 포함됩니다.
7. Power BI desktop의 데이터를 시각화하려면 테이블 이름 앞의 확인란을 선택하고 **로드**를 클릭합니다.
8. Power BI Desktop의 맨 왼쪽에 있는 데이터 탭을 선택하여 ![Power BI Desktop의 데이터 탭](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-data-tab.png) 데이터를 가져왔는지 확인합니다.
9. 이제 보고서 탭 ![Power BI Desktop의 보고서 탭](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-report-tab.png)을 클릭한 다음 **새 시각적 개체**를 클릭하고 타일을 사용자 지정함으로써 Power BI를 통해 시각적 개체를 만들 수 있습니다. Power BI Desktop에서 시각화를 만드는 방법에 대한 자세한 내용은 [Power BI의 시각화 유형](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

다음 오류가 표시되면 [2단계](#connect)에서 Azure Portal로부터 복사한 **호스트** 및 **액세스 키** 값이 올바른지 확인하고 다시 시도하세요. Azure Portal의 **호스트** 및 **액세스 키** 값 오른쪽에 있는 복사 단추를 사용하여 올바른 값을 복사합니다.

    [HY000]: [Microsoft][DocumentDB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>다음 단계

DocumentDB에 대해 자세히 알아보려면 [DocumentDB란 무엇인가요?](documentdb-introduction.md)를 참조하세요.

