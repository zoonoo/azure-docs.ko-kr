---
title: Stream Analytics를 사용하여 IoT 솔루션 빌드 | Microsoft Docs
description: 요금 창구 시나리오의 Stream Analytics IoT 솔루션 시작하기 자습서
keywords: iot 솔루션, 창 함수
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/21/2018
ms.author: sngun
ms.openlocfilehash: 6ee2c1c4b1255b462f2974c111e626d62608de5c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Stream Analytics를 사용하여 IoT 솔루션 빌드

## <a name="introduction"></a>소개
이 자습서에서는 Azure Stream Analytics를 사용하여 데이터에서 실시간으로 통찰력을 얻는 방법을 배웁니다. 개발자는 클릭 스트림, 로그 및 장치에서 생성된 이벤트와 같은 데이터 스트림을 기록 레코드 또는 참조 데이터와 조합하여 비즈니스 통찰력을 얻을 수 있습니다. Azure Stream Analytics는 Microsoft Azure에 호스트된 완전히 관리되는 실시간 스트림 계산 서비스로, 기본 제공 복원력, 낮은 대기 시간 및 확장성을 제공하여 몇 분 안에 실행할 수 있습니다.

이 자습서를 완료하고 나면 다음을 수행할 수 있습니다.

* Azure Stream Analytics 포털 숙지
* 스트리밍 작업 구성 및 배포
* Stream Analytics 쿼리 언어를 사용하여 실제 문제 명시 및 해결
* 안심하고 Stream Analytics를 사용하여 고객에 대한 스트리밍 솔루션 개발
* 모니터링 및 로깅 경험을 사용하여 문제 해결

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 최신 버전의 [Azure PowerShell](/powershell/azure/overview)
* Visual Studio 2017, 2015 또는 무료 [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* [Azure 구독](https://azure.microsoft.com/pricing/free-trial/)
* 컴퓨터에 대한 관리자 권한
* [azure-stream-analytics GitHub 리포지토리](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp)에서 TollApp 센서 데이터 분석에 대한 소스 코드를 다운로드합니다. 이 리포지토리는 다음 섹션에서 사용할 샘플 데이터와 쿼리를 포함합니다. 

## <a name="scenario-introduction-hello-toll"></a>시나리오 소개: “Hello, Toll!”
톨게이트 요금소는 일반적인 현상입니다. 전 세계의 많은 고속도로, 다리 및 터널에서 이러한 요금소를 만날 수 있습니다. 각 요금소에는 여러 개의 요금 창구가 있습니다. 유인 창구에서는 직원에게 요금을 지불하기 위해 멈춰야 합니다. 무인 창구에서는 차량이 톨게이트 창구를 통과할 때 각 창구 상단의 센서가 차량의 윈드쉴드에 부착된 RFID 카드를 검색합니다. 이러한 요금소를 통과하는 차량 흐름을 흥미로운 작업을 수행할 수 있는 이벤트 스트림으로 쉽게 시각화할 수 있습니다.

![요금 창구에 서 있는 자동차 사진](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>들어오는 데이터
이 자습서에서는 두 가지 데이터 스트림을 사용합니다. 톨게이트 요금소의 입구 및 출구에 설치된 센서가 첫 번째 스트림을 생성합니다. 두 번째 스트림은 차량 등록 데이터가 포함된 정적 조회 데이터 집합입니다.

### <a name="entry-data-stream"></a>진입 데이터 스트림
진입 데이터 스트림에는 톨게이트 요금소로 들어가는 자동차에 대한 정보가 포함됩니다.

| TollId | EntryTime | LicensePlate | 시스템 상태 | 계정을 | 모델 | VehicleType | VehicleWeight | Toll | 태그 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |1007 BNJ |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

다음은 열에 대한 간단한 설명입니다.

| 열 | 설명 |
| --- | --- |
| TollId |톨게이트 요금소를 고유하게 식별하는 요금 창구 ID |
| EntryTime |요금 창구에 차량이 진입하는 날짜 및 시간(UTC) |
| LicensePlate |차량 번호판 번호 |
| 시스템 상태 |미국의 주 이름 |
| 계정을 |자동차 제조업체 |
| 모델 |자동차 모델 번호 |
| VehicleType |여객 차량의 경우 1, 화물 차량의 경우 2 |
| WeightType |차량 무게(톤), 승용차는 0 |
| Toll |통행료 값(USD) |
| 태그 |요금을 수동으로 지불할 때 이 지불을 자동으로 비워놓는 자동차의 e-태그 |

### <a name="exit-data-stream"></a>진출 데이터 스트림
진출 데이터 스트림에는 요금소를 떠나는 차량에 대한 정보가 포함됩니다.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |1007 BNJ |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

다음은 열에 대한 간단한 설명입니다.

| 열 | 설명 |
| --- | --- |
| TollId |톨게이트 요금소를 고유하게 식별하는 요금 창구 ID |
| ExitTime |차량이 요금소를 빠져 나가는 날짜 및 시간(UTC) |
| LicensePlate |차량 번호판 번호 |

### <a name="commercial-vehicle-registration-data"></a>화물 차량 등록 데이터
이 자습서에서는 화물 차량 등록 데이터베이스의 정적 스냅숏을 사용합니다.

| LicensePlate | RegistrationId | 만료됨 |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

다음은 열에 대한 간단한 설명입니다.

| 열 | 설명 |
| --- | --- |
| LicensePlate |차량 번호판 번호 |
| RegistrationId |차량 등록 ID |
| 만료됨 |차량의 등록 상태: 차량 등록이 활성화된 경우는 0, 등록이 만료된 경우는 1 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Azure Stream Analytics를 위한 환경 설정
이 자습서를 완료하려면 Microsoft Azure 구독이 필요합니다. Microsoft는 Microsoft Azure 서비스에 대한 평가판을 제공합니다.

Azure 계정이 없는 경우 [평가판 버전을 요청](http://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.

> [!NOTE]
> 평가판에 등록하려면 문자 메시지 및 유효한 신용 카드를 받을 수 있는 모바일 장치가 필요합니다.
> 
> 

이 문서 끝에 나오는 "Azure 계정 정리" 섹션의 단계를 수행해야 Azure 크레딧을 최대한 활용할 수 있습니다.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>자습서에 필요한 Azure 리소스 프로비전
이 자습서는 *진입* 및 *진출* 데이터 스트림을 받기 위해 2개의 이벤트 허브가 필요합니다. Azure SQL Database는 Stream Analytics 작업의 결과를 출력합니다. Azure Storage는 차량 등록에 대한 참조 데이터를 저장합니다.

GitHub에 있는 TollApp 폴더의 Setup.ps1 스크립트를 사용하여 필요한 모든 리소스를 만들 수 있습니다. 시간 형편상 이를 실행하는 것이 좋습니다. Azure Portal에서 이러한 리소스를 구성하는 방법에 대해 자세히 알아보려면 "Azure Portal에서 자습서 리소스 구성" 부록을 참조하세요.

[TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) 폴더 및 파일 지원을 다운로드하고 저장합니다.

**Microsoft Azure PowerShell** 창을 *관리자 권한으로*엽니다. Azure PowerShell이 아직 없는 경우 [Azure PowerShell 설치 및 구성](/powershell/azure/overview) 지침에 따라 설치합니다.

Windows는 .ps1, .dll 및 .exe 파일을 자동으로 차단하기 때문에 스크립트를 실행하기 전에 실행 정책을 설정해야 합니다. Azure PowerShell 창이 *관리자 권한*으로 실행되고 있는지 확인합니다. **Set-ExecutionPolicy unrestricted**를 실행합니다. 메시지가 표시되면 **Y**를 입력합니다.

![Azure PowerShell 창에서 실행 중인 "Set-executionpolicy unrestricted"의 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

**Get-ExecutionPolicy** 를 실행하여 명령이 작동하는지 확인합니다.

![Azure PowerShell 창에서 실행 중인 "Get-ExecutionPolicy"의 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

스크립트와 생성기 응용 프로그램이 있는 디렉터리로 이동합니다.

![Azure PowerShell 창에서 실행 중인 "cd .\TollApp\TollApp"의 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

**.\\Setup.ps1**을 입력하여 Azure 계정을 설정하고 필요한 모든 리소스를 만들고 구성한 다음 이벤트 생성을 시작합니다. 스크립트가 리소스를 만들 영역을 임의로 선택합니다. 영역을 명시적으로 지정하려면 다음 예제와 같이 **-location** 매개 변수를 전달할 수 있습니다.

**.\\Setup.ps1 -location “Central US”**

![Azure 로그인 페이지의 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

이 스크립트는 Microsoft Azure의 **로그인** 페이지를 엽니다. 계정 자격 증명을 입력합니다.

> [!NOTE]
> 계정에 여러 구독에 대한 액세스 권한이 있는 경우 자습서에 사용하려는 구독 이름을 입력하라는 메시지가 표시됩니다.
> 
> 

스크립트를 실행하는 데 몇 분 정도 걸릴 수 있습니다. 완료되면 아래 스크린샷과 같은 출력이 표시되어야 합니다.

![Azure PowerShell 창의 스크립트 출력 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

아래 스크린샷과 비슷한 다른 창도 표시됩니다. 이 응용 프로그램은 자습서를 실행하는 데 필요한 Azure Event Hubs에 이벤트를 전송합니다. 따라서 자습서를 완료할 때까지 응용 프로그램을 중지하거나 이 창을 닫으면 안 됩니다.

!["이벤트 허브 데이터 전송 중" 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

이제 리소스를 Azure Portal에서 볼 수 있습니다. <https://portal.azure.com>으로 이동하고, 계정 자격 증명으로 로그인합니다. 현재 일부 기능은 클래식 포털을 활용합니다. 다음 단계가 명확하게 표시됩니다.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Azure Portal에서 왼쪽 관리 창 아래에 있는 **추가 서비스**를 클릭합니다. 제공된 필드에 **Event Hubs**를 입력하면 **tolldata**로 시작하는 새 이벤트 허브 네임스페이스를 볼 수 있습니다. 이 네임스페이스는 Setup.ps1 스크립트에 의해 생성됩니다. 이 네임스페이스에서 만든 **진입**과 **진출**이라는 두 개의 이벤트 허브가 표시됩니다.

### <a name="azure-storage-container"></a>Azure Storage 컨테이너
Azure Portal에서 저장소 계정으이동로 하면 **tolldata**로 시작하는 저장소 계정이 표시됩니다. 차량 등록 데이터가 들어 있는 업로드된 JSON 파일을 보려면 **tolldata** 컨테이너를 클릭합니다.

### <a name="azure-sql-database"></a>Azure SQL Database
1. 브라우저에서 열려 있는 첫 번째 탭에서 Azure Portal로 다시 이동합니다. 자습서에서 사용되는 SQL Database를 보려면 Azure Portal 왼쪽에 있는 **SQL Database**를 클릭하고 **tolldatadb**를 클릭합니다.
   
    ![만든 SQL Database의 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. 포트 번호 없이 서버 이름(예:*servername*.database.windows.net)을 복사합니다.
    ![만든 SQL Database DB의 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>Visual Studio에서 데이터베이스에 연결
Visual Studio를 사용하여 출력 데이터베이스의 쿼리 결과에 액세스합니다.

Visual Studio에서 SQL Database(대상)에 연결합니다.

1. Visual Studio를 열고 **도구** > **데이터베이스에 연결**을 클릭합니다.
2. 표시되는 메시지에 따라 **Microsoft SQL Server**를 데이터 원본으로 클릭합니다.
   
    ![데이터 원본 변경 대화 상자](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. **서버 이름** 필드에 Azure Portal에서 이전 섹션에 복사한 이름(예: *servername*.database.windows.net)을 붙여 넣습니다.
4. **SQL Server 인증 사용**을 클릭합니다.
5. **사용자 이름** 필드에 **tolladmin**을, **암호** 필드에 in the **123toll!**을 입력합니다.
6. **데이터베이스 이름 선택 또는 입력**을 클릭하고 데이터베이스로 **TollDataDB**를 선택합니다.
   
    ![연결 추가 대화 상자](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. **확인**을 클릭합니다.
8. 서버 탐색기를 엽니다.
   
    ![서버 탐색기](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. TollDataDB 데이터베이스에 있는 4개의 테이블을 봅니다.
   
    ![TollDataDB 데이터베이스의 테이블](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>이벤트 생성기: TollApp 샘플 프로젝트
PowerShell 스크립트는 TollApp 응용 프로그램 예제를 사용하여 이벤트 보내기를 자동으로 시작합니다. 추가 단계를 수행할 필요가 없습니다.

그러나 구현 세부 정보에 관심이 있는 경우 GitHub [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source)에서 TollApp 응용 프로그램의 원본 코드를 찾을 수 있습니다.

![Visual Studio에 표시된 샘플 코드의 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기
1. Azure Portal에서 페이지의 왼쪽 위 모퉁이에 있는 녹색 더하기 기호를 클릭하여 새 Stream Analytics 작업을 만듭니다. **인텔리전스+분석**을 선택한 다음 **Stream Analytics 작업**을 클릭합니다.
   
    ![새 단추](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. 작업 이름을 지정하고 구독이 올바른지 유효성을 검사한 다음 Event hub 저장소와 동일한 지역에 새 리소스 그룹을 만듭니다. 스크립트의 경우 기본값은 미국 중남부입니다.
3. 페이지의 맨 아래에서 **대시보드에 고정** 및 **만들기**를 클릭합니다.
   
    ![Stream Analytics 작업 만들기 옵션](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>입력 원본 정의
1. 작업을 만들고 작업 페이지를 엽니다. 또는 포털 대시보드에서 만든 분석 작업을 클릭할 수 있습니다.

2. **입력** 탭을 열어 원본 데이터를 정의합니다.
   
    ![입력 탭](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. **입력 추가**를 클릭합니다.
   
    ![입력 추가 옵션](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. **입력 별칭**으로 **EntryStream**을 입력합니다.
5. 원본 형식은 **데이터 스트림**입니다.
6. 원본은 **Event hub**입니다.
7. **Service Bus 네임스페이스**는 드롭다운에서 TollData 항목이어야 합니다.
8. **Event hub 이름**은 **항목**으로 설정해야 합니다.
9. **이벤트 허브 정책 이름*은 **RootManageSharedAccessKey**(기본값)입니다.
10. **이벤트 직렬화 형식**에 대해 **JSON**을, **인코딩**에 대해 **UTF8**을 선택합니다.
   
    설정이 다음과 같이 표시됩니다.
   
    ![이벤트 허브 설정](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. 마법사를 완료하려면 페이지 맨 아래에서 **만들기**를 클릭합니다.
    
    진입 스트림을 만들었으므로 이제 동일한 단계를 수행하여 진출 스트림을 만듭니다. 다음 스크린샷과 같이 값을 입력해야 합니다.
    
    ![진출 스트림에 대한 설정](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    다음 두 진입 스트림을 정의했을 것입니다.
    
    ![Azure Portal에 정의된 입력 스트림](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    다음은 차량 등록 데이터가 있는 blob 파일에 대해 참조 데이터 입력을 추가합니다.
11. **추가**를 클릭한 다음 스트림 입력에 대한 동일한 프로세스를 수행하지만 **데이터 스트림** 대신 **참조 데이터**를 선택하고 **입력 별칭**은 **등록**입니다.

12. **tolldata**로 시작하는 저장소 계정입니다. 컨테이너 이름이 **tolldata**여야 하고 **경로 패턴**은 **registration.json**이어야 합니다. 이 파일 이름은 대/소문자를 구분하므로 **소문자**여야 합니다.
    
    ![블로그 저장소 설정](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. **만들기**를 클릭하여 마법사를 마칩니다.

이제 모든 입력이 정의되었습니다.

## <a name="define-output"></a>출력 정의
1. Stream Analytics 작업 개요 창에서 **출력**을 선택합니다.
   
    ![출력 탭 및 "출력 추가" 옵션](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. **추가**를 클릭합니다.
3. **출력 별칭**을 '출력'으로 설정한 다음 **싱크**를 **SQL Database**로 설정합니다.
3. 이 문서의 "Visual Studio에서 데이터베이스에 연결" 섹션에 사용된 서버 이름을 선택합니다. 데이터베이스 이름은 **TollDataDB**입니다.
4. **사용자 이름** 필드에 **tolladmin**을, **암호** 필드에 **123toll!**을, **테이블** 필드에 **TollDataRefJoin**을 입력합니다.
   
    ![SQL Database 설정](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. **만들기**를 클릭합니다.

## <a name="azure-stream-analytics-query"></a>Azure Stream Analytics 쿼리
**쿼리** 탭에는 들어오는 데이터에 대해 변환을 수행하는 SQL 쿼리가 있습니다.

![쿼리 탭에 추가된 쿼리](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

이 자습서에서는 톨게이트 요금 데이터와 관련된 몇 가지 비즈니스 질문에 대답하고 관련 대답을 제공하기 위해 Azure Stream Analytics에서 사용할 수 있는 Stream Analytics 쿼리를 생성해봅니다.

첫 번째 Stream Analytics 작업을 시작하기 전에 몇 가지 시나리오 및 쿼리 구문을 살펴보겠습니다.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Azure Stream Analytics 쿼리 언어 소개
- - -
요금 창구에 들어가는 차량 수를 계산해야 한다고 가정해봅시다. 이 작업은 연속 이벤트 스트림이므로 "기간"을 정의해야 합니다. 이 질문을 "3분 간격으로 요금 창구에 진입하는 차량은 몇 대입니까?”로 수정해보겠습니다. 이를 일반적으로 연속 개수(Tumbling Count)라고 합니다.

이 질문에 대답하는 Azure Stream Analytics 쿼리를 살펴보겠습니다.

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

아시다시피 Azure Stream Analytics는 SQL과 유사한 쿼리를 사용하고 몇 가지 확장을 추가하여 쿼리의 시간 관련 측면을 지정합니다.

자세한 내용을 보려면 MSDN의 쿼리에 사용된 [시간 관리](https://msdn.microsoft.com/library/azure/mt582045.aspx) 및 [창 작업](https://msdn.microsoft.com/library/azure/dn835019.aspx) 구문을 참조하세요.

## <a name="testing-azure-stream-analytics-queries"></a>Azure Stream Analytics 쿼리 테스트
이제 첫 번째 Azure Stream Analytics 쿼리를 작성했으므로 다음 경로의 TollApp 폴더에 있는 샘플 데이터 파일을 사용하여 테스트해보겠습니다.

**..\\TollApp\\TollApp\\Data**

이 폴더에는 다음 파일이 포함되어 있습니다.

* Entry.json
* Exit.json
* registration.json

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>질문 1: 요금 창구에 들어가는 차량 수
1. Azure Portal을 열고 만든 Azure Stream Analytics 작업으로 이동합니다. **쿼리** 탭을 열고 이전 섹션의 쿼리를 붙여 넣습니다.

2. 샘플 데이터에 대한 이 쿼리의 유효성을 검사하려면 ... 기호를 클릭하고 **파일에서 샘플 데이터 업로드**를 선택하여 EntryStream 입력에 데이터를 업로드합니다.

    ![Entry.json 파일 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. 표시되는 창에서 로컬 컴퓨터에 있는 Entry.json 파일을 선택하고 **확인**을 클릭합니다. 이제 **테스트** 아이콘이 밝아져서 클릭할 수 있습니다.
   
    ![Entry.json 파일 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. 쿼리의 출력이 예상대로인지 확인합니다.
   
    ![테스트의 결과](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>질문 2: 각 차량이 요금 창구를 통과하는 총 시간 보고
자동차가 요금소를 통과하는 데 필요한 평균 시간은 프로세스의 효율성과 고객 경험을 평가하는 데 도움이 됩니다.

총 시간을 확인하려면 ExitTime 스트림과 EntryTime 스트림을 조인해야 합니다. TollId 및 LicencePlate 열의 스트림을 조인합니다. **JOIN** 연산자에서는 조인된 이벤트 간에 허용할 수 있는 시간 차이를 설명하는 일시적인 시간 여유를 지정해야 합니다. **DATEDIFF** 함수를 사용하여 이벤트 사이의 간격이 15분 이하가 되도록 지정합니다. **DATEDIFF** 함수를 진출과 진입 시간에 적용하여 차량이 요금소에서 사용하는 실제 시간을 계산합니다. **SELECT** 문에서 **DATEDIFF**를 사용할 때 **JOIN** 조건에서 사용하는 것에 비해 어떤 차이가 있는지 적어둡니다.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. 이 쿼리를 테스트하려면 작업의 **쿼리** 탭에서 쿼리를 업데이트합니다. 앞서서 **EntryStream**에 입력한 것과 같이 **ExitStream**에 테스트 파일을 추가합니다.
   
2. **테스트**를 클릭합니다.

3. 쿼리를 테스트하고 출력을 보려면 이 확인란을 선택합니다.
   
    ![테스트의 출력](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>질문 3: 등록 기간이 만료된 모든 화물 차량 보고
Azure Stream Analytics는 데이터의 정적 스냅숏을 사용하여 임시 데이터 스트림과 조인할 수 있습니다. 이 기능을 보여 주기 위해 다음 샘플 질문을 사용하겠습니다.

화물 차량이 요금 회사에 등록된 경우 검사받기 위해 정차하지 않고 요금 창구를 통과할 수 있습니다. 화물 차량 등록 조회 테이블을 사용하여 등록 기간이 만료된 모든 화물 차량을 식별할 수 있습니다.

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

참조 데이터를 사용하여 쿼리를 테스트하려면 이미 수행한 참조 데이터에 대한 입력 원본을 정의해야 합니다.

이 쿼리를 테스트하려면 **쿼리** 탭에 쿼리를 붙여넣고 **테스트**를 클릭한 다음 2개의 입력 원본 및 등록 샘플 데이터를 지정하고 **테스트**를 클릭합니다.  
   
![테스트의 출력](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>Stream Analytics 작업 시작
이제 구성을 완료하고 작업을 시작해보겠습니다. 질문 3의 쿼리를 저장하면 출력 테이블 **TollDataRefJoin** 의 스키마와 일치하는 출력이 생성됩니다.

작업 **대시보드**로 가서 **시작**을 클릭합니다.

![작업 대시보드의 시작 단추 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

대화 상자가 나타나면 **시작 출력** 시간을 **사용자 지정 시간**으로 변경합니다. 현재 시간보다 1시간 이전으로 변경합니다. 자습서를 시작할 때 이벤트를 생성하기 시작했으므로 이렇게 변경하면 이벤트 허브의 모든 이벤트가 확실히 처리될 수 있습니다. 이제 **시작** 단추를 클릭하여 작업을 시작합니다.

![사용자 지정 시간 선택 항목](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

작업을 시작하는 데 몇 분 정도 걸릴 수 있습니다. Stream Analytics의 최상위 페이지에서 상태를 볼 수 있습니다.

![작업의 상태 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>Visual Studio에서 결과 확인
1. Visual Studio 서버 탐색기를 열고 **TollDataRefJoin** 테이블을 마우스 오른쪽 단추로 클릭합니다.
2. **테이블 데이터 표시**를 선택하면 작업의 출력을 볼 수 있습니다.
   
    ![서버 탐색기의 "테이블 데이터 표시" 선택 항목](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업 규모 지정
Azure Stream Analytics는 탄력적으로 크기를 조정하여 많은 양의 데이터를 처리할 수 있도록 디자인되었습니다. Azure Stream Analytics 쿼리에서 **PARTITION BY** 절을 사용하면 이 단계에서 확장하는 시스템을 알 수 있습니다. **PartitionId**는 입력(이벤트 허브)의 파티션 ID와 일치하도록 시스템이 추가하는 특수한 열입니다.

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. 현재 작업을 중지하고 **쿼리** 탭에서 쿼리를 업데이트하고 작업 대시보드에서 **설정** 기어를 엽니다. **크기 조정**을 클릭합니다.
   
    **스트리밍 단위**는 작업이 검색할 수 있는 계산 능력의 크기를 정의합니다.
2. 드롭다운을 1에서 6으로 변경합니다.
   
    ![6개의 스트리밍 단위를 선택하는 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. **출력** 탭으로 이동하고 SQL 테이블 이름을 **TollDataTumblingCountPartitioned**로 변경합니다.

이제 작업을 시작하면 Azure Stream Analytics가 더 많은 계산 리소스에 작업을 배포하고 처리량을 높일 수 있습니다. TollApp 응용 프로그램이 TollId로 분할된 이벤트를 보내고 있다는 것에 유의하세요.

## <a name="monitor"></a>모니터
**모니터링** 영역에는 실행 중인 작업에 대한 통계가 들어 있습니다. 동일한 지역에서 저장소 계정을 사용하는 데 처음으로 구성이 필요합니다(이 문서의 나머지 부분과 같은 이름 toll).   

![모니터링 스크린샷](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

작업 대시보드에서 **설정** 영역에서도 **작업 로그**에 액세스할 수 있습니다.


## <a name="conclusion"></a>결론
이 자습서에서는 Azure Stream Analytics 서비스에 대해 소개했습니다. Stream Analytics 작업에 대해 입력 및 출력을 구성하는 방법을 보여 줍니다. 이 자습서에서는 지금까지 요금 데이터 시나리오를 통해 동작 중인 데이터 공간에서 발생하는 일반적인 문제 유형과 Azure Stream Analytics에서 간단한 SQL 유사 쿼리를 사용하여 이러한 문제를 해결하는 방법을 설명했습니다. 또한 임시 데이터 작업을 위한 SQL 확장 구성을 설명했습니다. 데이터 스트림을 조인하는 방법 및 정적 참조 데이터를 사용하여 데이터 스트림을 보강하는 방법과 처리량을 높일 수 있도록 쿼리를 확장하는 방법을 설명했습니다.

이 자습서가 개요에 해당하는 내용을 잘 소개하고는 있지만 완전하지는 않습니다. [일반적인 Stream Analytics 사용 패턴에 대한 쿼리 예제](stream-analytics-stream-analytics-query-patterns.md)에서 SQL 언어를 사용하는 추가 쿼리 패턴을 찾을 수 있습니다.
Azure Stream Analytics에 대한 자세한 내용을 알아보려면 [온라인 설명서](https://azure.microsoft.com/documentation/services/stream-analytics/)를 참조하세요.

## <a name="clean-up-your-azure-account"></a>Azure 계정 정리
1. Azure Portal에서 Stream Analytics 작업을 중지합니다.
   
    Setup.ps1 스크립트는 2개의 이벤트 허브 및 SQL Database를 만듭니다. 다음 지침을 통해 자습서 끝부분에서 리소스를 정리할 수 있습니다.
2. PowerShell 창에서 **.\\Cleanup.ps1**을 입력합니다. 그러면 자습서에서 사용한 리소스를 삭제하는 스크립트가 시작됩니다.
   
   > [!NOTE]
   > 리소스는 이름으로 식별됩니다. 제거를 확인하기 전에 각 항목을 신중하게 검토해야 합니다.
   > 
   > 


