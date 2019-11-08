---
title: 문제 해결
description: Azure FarmBeats 문제를 해결 하는 방법
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ab0d3aac6944e331223d91eb98b50b3308be12c3
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797635"
---
# <a name="troubleshooting"></a>문제 해결

다음 섹션에서는 일반적인 Azure FarmBeats 문제와 이러한 문제를 해결 하는 방법에 대해 설명 합니다.

추가 도움말을 보려면 farmbeatssupport@microsoft.com에서이 전자 메일에 배포자 파일을 포함 하세요.

 다음 단계를 사용 하 여 배포자 파일을 다운로드 합니다.

1. 강조 표시 된 아이콘을 선택 하 고 드롭다운에서 **다운로드** 옵션을 선택 합니다.

    ![프로젝트 팜 비트](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. 다음 화면에서 배포자 파일의 경로를 입력 합니다. 예를 들면 farmbeats-deployer입니다.

## <a name="sensor-telemetry"></a>센서 원격 분석

### <a name="telemetry-not-seen"></a>원격 분석이 표시 되지 않음

**증상**: 장치/센서가 배포 되 고 FarmBeats를 장치 파트너와 연결 했습니다. 그러나 FarmBeats에서 원격 분석 데이터를 가져오거나 볼 수 없습니다.

**정정 작업**: Azure Portal 방문 하 여 다음 단계를 수행 합니다.

1. FarmBeats Data hub 리소스 그룹으로 이동 합니다.   
2. **이벤트 허브** (DatafeedEventHubNamespace ...)를 선택 합니다.  들어오는 메시지의 수를 확인 합니다.   
3. **들어오는 메시지가 없는**경우 장치 파트너에 게 문의 하세요.  
4. **들어오는 메시지가**있는 경우 데이터 허브 및 가속기 로그를 사용 하 여 farmbeatssupport@microsoft.com에 연결 하 고 캡처된 원격 분석을 사용 합니다.

로그를 다운로드 하는 방법을 이해 하려면 문서의 [로그 섹션](#collect-logs-manually) 을 참조 하세요.    

### <a name="device-appears-offline"></a>장치가 오프 라인으로 표시 됩니다.

**증상**: 장치가 설치 되 고 FarmBeats를 장치 파트너와 연결 했습니다. 장치가 온라인 상태이 고 원격 분석 데이터를 전송 하지만 오프 라인으로 표시 됩니다.

**정정 동작**:이 장치에 대해 보고 간격이 구성 되지 않았습니다. 보고 간격을 설정 하려면 장치 제조업체에 문의 하세요. 

### <a name="error-deleting-a-resource"></a>리소스를 삭제 하는 동안 오류 발생

장치를 삭제 하는 동안 발생 하는 일반적인 오류 시나리오는 다음과 같습니다.  

**메시지**: 장치가 센서에서 참조 됨: 장치와 연결 된 센서가 하나 이상 있습니다. 센서를 삭제 한 다음 장치를 삭제 합니다.  

**의미**: 장치가 팜에 배포 된 여러 센서와 연결 되어 있습니다.   

**수정 동작**:  

1. 가속기를 통해 장치와 연결 된 센서를 삭제 합니다.  
2. 센서를 다른 장치에 연결 하려는 경우 장치 파트너에 게 동일한 작업을 수행 하도록 요청 합니다.  
3. DELETE API 호출을 사용 하 여 장치를 삭제 하 고 force 매개 변수를 ' t r u e '로 설정 합니다.  

**메시지**: 장치에서 parentdeviceid로 장치를 참조 합니다 .이 장치에 자식 장치로 연결 된 장치가 하나 이상 있습니다. 해당 장치를 삭제 한 후이 장치를 삭제 합니다.  

**의미**: 장치에 다른 장치가 연결 되어 있습니다.  

**정정 작업**

1. 특정 장치에 연결 된 장치 삭제  
2. 특정 장치 삭제  

    > [!NOTE]
    > 센서가 연결 되어 있는 경우에는 장치를 삭제할 수 없습니다. 관련 센서를 삭제 하는 방법에 대 한 자세한 내용은 센서 데이터 가져오기 챕터에서 [센서 삭제](get-sensor-data-from-sensor-partner.md) 를 참조 하세요.


## <a name="issues-with-jobs"></a>작업 관련 문제

### <a name="farmbeats-internal-error"></a>FarmBeats 내부 오류

**메시지**: FarmBeats 내부 오류입니다. 자세한 내용은 문제 해결 가이드를 참조 하세요.

**정정 동작**: 데이터 파이프라인에서 일시적인 오류가 발생 했기 때문일 수 있습니다. 작업을 다시 한 번 만듭니다. 오류가 계속 발생 하면 FarmBeats 포럼의 게시물에 오류를 추가 하거나 FarmBeatsSupport@microsoft.com에 문의 하세요.

## <a name="accelerator-troubleshooting"></a>액셀러레이터 키 문제 해결

### <a name="access-control"></a>Access Control

**역할 할당을 추가 하는 동안 오류 발생**

**메시지**: 일치 하는 사용자를 찾을 수 없음

**정정 작업**: 역할 할당을 수행 하려는 전자 메일 ID를 확인 합니다. 전자 메일 ID는 Active Directory에서 해당 사용자에 대해 등록 된 것과 정확히 일치 해야 합니다. 오류가 계속 발생 하면 FarmBeats 포럼의 게시물에 오류를 추가 하거나 연락처 FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>액셀러레이터에 로그인 할 수 없습니다.

**메시지**: 오류: 서비스를 호출할 수 있는 권한이 없습니다. 관리자에 게 권한 부여를 문의 합니다.

**정정 작업**: 관리자에 게 FarmBeats 배포에 대 한 액세스 권한을 부여 하도록 요청 합니다. 이 작업은 RoleAssignment을 게시 하거나 액셀러레이터의 설정 창에 있는 Access Control를 통해 수행할 수 있습니다.  

이미 추가 했 고이 오류가 발생 하는 경우 페이지를 새로 고쳐 다시 시도 하세요.  오류가 계속 발생 하면 FarmBeats 포럼의 게시물에 오류를 추가 하거나 FarmBeatsSupport@microsoft.com에 문의 하세요.

![프로젝트 팜 비트](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Accelerator-알 수 없는 오류가 발생 했습니다.  

**메시지**: 오류: 알 수 없는 오류가 발생 했습니다.

**정정 동작**: 페이지를 유휴 상태로 유지 하는 경우에 발생 합니다. 페이지를 새로 고칩니다.  

그래도 오류가 계속 발생 하면 FarmBeats 포럼 또는 담당자에 게 게시에 오류를 추가 FarmBeatsSupport@microsoft.com

**FarmBeats Accelerator는 FarmBeatsDeployment을 업그레이드 한 후에도 최신 버전을 표시 하지 않습니다.**

**수정 작업**: 브라우저에서 서비스 작업자 지 속성으로 인해 발생 합니다.
액셀러레이터 키가 열려 있는 모든 브라우저 탭을 닫고 브라우저 창을 닫습니다. 브라우저의 새 인스턴스를 시작 하 고 액셀러레이터 키 URI를 다시 로드 합니다. 이렇게 하면 액셀러레이터 키의 새 버전이 로드 됩니다.

## <a name="sentinel-imagery-related-issues"></a>센티널 이미지 관련 문제

### <a name="sentinel-wrong-username-or-password"></a>센티널 잘못 된 사용자 이름 또는 암호

**작업 실패 메시지** 이 리소스에 액세스 하려면 전체 인증이 필요 합니다.

**수정 작업**: 올바른 사용자 이름과 암호를 사용 하 여 데이터 허브를 업그레이드 하는 설치 관리자를 다시 실행 합니다.

**정정 작업**: 실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 하 고, 작업이 성공 했는지 확인 합니다.

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>센티널 허브 URL이 잘못 되었거나 액세스할 수 없음 

**작업 실패 메시지**: 오류가 발생 했습니다. 액세스 하려는 페이지를 일시적으로 사용할 수 없습니다. 

**수정 동작**:
1.  브라우저에서 센티널 URL (https://scihub.copernicus.eu/dhus/)을 열고 웹 사이트에 액세스할 수 있는지 확인 합니다. 
2.  웹 사이트에 액세스할 수 없는 경우 방화벽/회사 네트워크 등이 있는지 확인 하 고, 위의 URL을 허용 하는 데 필요한 단계를 수행 합니다. 
3.  실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 하 고, 작업이 성공 했는지 확인 합니다.  

### <a name="sentinel-server-down-for-maintenance"></a>유지 관리를 위한 센티널 서버 작동 중단

**작업 실패 메시지**: Copernicus 오픈 액세스 허브가 곧 다시 출시 될 예정입니다. 불편을 끼쳐 드려 지금은 몇 가지 유지 관리 작업을 수행 하 고 있습니다. 곧 다시 온라인 상태가 될 예정입니다. 

**수정 동작**:

1.  이 문제는 센티널 서버에서 유지 관리 작업을 수행 하는 경우 발생할 수 있습니다. 
2.  위의 이유로 인해 작업/파이프라인이 실패 하면 잠시 후에 작업을 다시 제출 합니다. 
3.  사용자는 https://scihub.copernicus.eu/news/를 방문 하 여 계획/계획 되지 않은 센티널 유지 관리 활동에 대 한 정보를 확인할 수 있습니다.  
4.  실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 하 고, 작업이 성공 했는지 확인 합니다.

### <a name="sentinel-maximum-number-of-connections-reached"></a>센티널 최대 연결 수에 도달 했습니다.

**작업 실패 메시지**: "<username>" 사용자가 수행 하는 최대 두 개의 동시 흐름 수 

**정정 작업**
1.  위의 이유로 인해 작업이 실패 하면 다른 배포/소프트웨어에서 동일한 센티널 계정이 사용 됩니다. 
2.  사용자는 새 센티널 계정을 만들고 새 센티널 사용자 이름 및 암호를 사용 하 여 데이터 허브를 업그레이드 하기 위한 설치 관리자를 다시 실행할 수 있습니다.  
3.  실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 하 고, 작업이 성공 했는지 확인 합니다.

### <a name="sentinel-server-refused-connection"></a>센티널 서버가 연결을 거부 했습니다. 

**작업 실패 메시지**:

서버에서 연결을 거부 했습니다. http://172.30.175.69:8983/solr/dhus 

**정정 동작**: 센티널 서버에서 유지 관리 작업을 수행 하는 경우이 문제가 발생할 수 있습니다. 
1.  위의 이유로 인해 작업/파이프라인이 실패 하면 잠시 후에 작업을 다시 제출 합니다. 
2.  사용자는 https://scihub.copernicus.eu/news/를 방문 하 여 계획/계획 되지 않은 센티널 유지 관리 활동에 대 한 정보를 확인할 수 있습니다.  
3.  실패 한 작업을 다시 실행 하거나, 5-7 일의 날짜 범위에 대해 위성 인덱스 작업을 실행 하 고, 작업이 성공 했는지 확인 합니다.


## <a name="collect-logs-manually"></a>수동으로 로그 수집

Azure Storage 탐색기에 [설치 하 고 배포]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) 합니다.

### <a name="how-to-collect-data-hub-adf-job-logs"></a>데이터 허브 ADF 작업 로그를 수집 하는 방법
1. https://portal.azure.com에 로그인
2. **검색** 텍스트 상자에서 FarmBeats Data hub 리소스 그룹을 검색 합니다.

    > [!NOTE]
    > FarmBeats 배포 시 지정 된 데이터 허브 리소스 그룹을 선택 합니다.

리소스 그룹 대시보드에서 (datahublogs) 저장소 계정을 검색 합니다. 예: datahublogsmvxmq  

1.  **저장소** 계정 대시보드를 보려면 **이름** 열에서 storage 계정을 선택 합니다.
2.  (Datahubblogs ...) 페이지에서 **탐색기에서 열기** 를 선택 하 여 **열린 Azure Storage 탐색기** 응용 프로그램을 표시 합니다.
3.  왼쪽 패널의 (datahubblogs), **Blob 컨테이너**에서 **작업-로그**를 선택 합니다.
4.  **작업-로그** 탭에서 **다운로드**를 선택 합니다.
5.  컴퓨터의 로컬 폴더에 대 한 로그를 다운로드 하는 위치를 선택 합니다.
6.  다운로드 한 zip 파일을 farmbeatssupport@microsoft.com에 전자 메일로 보냅니다.

    ![프로젝트 팜 비트](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>액셀러레이터 키 ADF 작업 로그를 수집 하는 방법

1.  https://portal.azure.com에 로그인
2.  **검색** 텍스트 상자에서 FarmBeats Accelerator 리소스 그룹을 검색 합니다.

    > [!NOTE]
    > FarmBeats 배포 시 지정 된 액셀러레이터 리소스 그룹을 선택 합니다.

3.  리소스 그룹 대시보드에서 저장소를 검색 합니다. 저장소 계정. 예: storagedop4k
4.  저장소 계정 대시보드를 보려면 **이름** 열에서 storage 계정을 선택 합니다.
5.  (저장소 ....) 페이지에서 **탐색기에서 열기** 를 선택 하 여 열린 Azure Storage 탐색기 응용 프로그램을 표시 합니다.
6.  왼쪽 < 패널의 저장소 ....), **Blob 컨테이너**에서 **작업-로그**를 선택 합니다.
7.  **작업-로그** 탭에서 **다운로드**를 선택 합니다.
8.  컴퓨터의 로컬 폴더에 대 한 로그를 다운로드 하는 위치를 선택 합니다.
9.  다운로드 한 zip 파일을 farmbeatssupport@microsoft.com에 전자 메일로 보냅니다.


### <a name="how-to-collect-data-hub-app-service-logs"></a>데이터 허브 app service 로그를 수집 하는 방법

1.  https://portal.azure.com에 로그인
2.  **검색** 텍스트 상자에서 FarmBeats Data hub 리소스 그룹을 검색 합니다.

    > [!NOTE]
    > FarmBeats 배포 시 지정 된 데이터 허브 리소스 그룹을 선택 합니다.

3.  리소스 그룹에서 (datahublogs) 저장소 계정을 검색 합니다. 예: datahublogsmvxmq
4.  **저장소** 계정 대시보드를 보려면 **이름** 열에서 storage 계정을 선택 합니다.
5.  (Datahubblogs ...) 페이지에서 **탐색기에서 열기** 를 선택 하 여 **열린 Azure Storage 탐색기** 응용 프로그램을 표시 합니다.
6.  왼쪽 패널의 (datahubblogs ...), **Blob 컨테이너**에서 appinsights-로그를 선택 합니다.
7.  Appinsights-로그 탭에서 **다운로드**를 선택 합니다.
8.  경로를 선택 하 여 컴퓨터의 로컬 폴더로 로그를 다운로드 합니다.
9.  다운로드 한 폴더를 farmbeatssupport@microsoft.com에 전자 메일로 보냅니다.

### <a name="how-to-collect-accelerator-app-service-logs"></a>Accelerator app service 로그를 수집 하는 방법

1.  https://portal.azure.com에 로그인
2.  **검색**에서 FarmBeats Accelerator 리소스 그룹을 검색 합니다.

    > [!NOTE]
    > FarmBeats 배포 시 제공 되는 Farmbeats accelerator 리소스 그룹을 선택 합니다.

3.  리소스 그룹에서 (저장소 ...) 저장소 계정을 검색 합니다. 예: storagedop4k
4.  **저장소** 계정 대시보드를 보려면 **이름** 열에서 storage 계정을 선택 합니다.
5.  (저장소 ....) 페이지에서 **탐색기에서 열기** 를 선택 하 여 **열린 Azure Storage 탐색기** 응용 프로그램을 표시 합니다.
6.  왼쪽 패널의 (저장소 ....), **Blob 컨테이너**에서 appinsights-로그를 선택 합니다.
7.  Appinsights-로그 탭에서 **다운로드**를 선택 합니다.
8.  컴퓨터의 로컬 폴더에 대 한 로그를 다운로드 하는 위치를 선택 합니다.
9.  다운로드 한 폴더를 farmbeatssupport@microsoft.com에 전자 메일로 보냅니다.

## <a name="known-issues"></a>알려진 문제

## <a name="batch-related-issues"></a>일괄 처리 관련 문제

**오류**: 지정 된 구독에 대 한 Batch 계정의 지역 계정 할당량에 도달 했습니다.

**정정 작업**: 할당량을 늘리거나 사용 하지 않는 일괄 처리 계정을 삭제 하 고 배포를 다시 실행 합니다.

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory 관련 문제

**오류**: Azure AD 앱 d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: 권한이 부족 하 여 작업을 완료할 수 없습니다. 위의 설정이 Azure AD 앱에 대해 올바르게 구성 되어 있는지 확인 하십시오.

**의미**: Azure AD 앱 등록 구성이 제대로 수행 되지 않았습니다.  

**정정 작업**: IT 관리자에 게 문의 하 여 Azure AD 앱 등록 생성을 생성 하는 [스크립트](https://aka.ms/PPCreateAADappregistration) 를 사용 합니다. 이 스크립트는 구성 단계도 자동으로 처리 합니다. 는  

**오류**:이 테 넌 트에서 새 Active Directory 응용 프로그램 "dummyname"을 만들 수 없습니다. 속성 식별자 uri에 대해 동일한 값을 가진 다른 개체가 이미 있습니다.

**의미**: 이름이 같은 Azure AD 앱 등록이 이미 있습니다.

**수정 작업**: 기존 Azure AD 앱 등록을 삭제 하거나 설치를 위해 다시 사용 합니다. 기존 Azure AD를 다시 사용 하는 경우 응용 프로그램 ID 및 클라이언트 암호를 설치 관리자에 전달 하 고 다시 배포 합니다.

## <a name="inputjson-related-issues"></a>Input. json 관련 문제

입력 json 파일에서 입력을 읽는 **동안 오류가 발생 했습니다** .

**정정 작업**:이 문제는 설치 관리자에 올바른 입력 json 경로 또는 이름을 지정 하지 못할 경우 발생 합니다. 적절 한 수정 작업을 수행 하 고 다시 배포를 다시 시도 합니다.

**Json 입력 구문 분석 오류**

**정정 작업**:이 문제는 주로 입력 json 파일 내의 잘못 된 값으로 인해 발생 합니다. 적절 한 수정 작업을 수행 하 고 배포를 다시 시도 합니다.

## <a name="high-cpu-usage"></a>높은 CPU 사용량

**오류**: 높은 CPU 사용량 경고를 참조 하는 전자 메일 경고를 받게 됩니다. 

**수정 동작**: 
1.  FarmBeats Data hub 리소스 그룹으로 이동 합니다.
2.  App service를 선택 합니다.  
3.  수직 확장 (App Service 계획)으로 이동 하 고 적절 한 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/windows/) 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

여전히 문제가 발생 하는 경우 microsoft [지원 포럼](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)에 문의 하세요.
