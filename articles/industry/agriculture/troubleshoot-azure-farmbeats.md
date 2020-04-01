---
title: Azure FarmBeats 문제 해결
description: 이 문서에서는 Azure FarmBeats 문제를 해결하는 방법을 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a31eece6629558b14b614853addce59642e698b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422718"
---
# <a name="troubleshoot"></a>문제 해결

이 문서에서는 일반적인 Azure FarmBeats 문제에 대한 솔루션을 제공합니다.

추가 도움이 원전을 farmbeatssupport@microsoft.com원할 경우. 전자 메일에 **deployer.log** 파일을 포함해야 합니다.

**deployer.log** 파일을 다운로드하려면 다음을 수행합니다.

1. **Azure 포털에** 로그인하고 구독 및 Azure AD 테넌트를 선택합니다.
2. Azure Portal의 위쪽 탐색 모음에서 Cloud Shell을 시작합니다.
3. **배쉬를** 선호하는 클라우드 쉘 환경으로 선택합니다.
4. 강조 표시된 아이콘을 선택한 다음 드롭다운 목록에서 **다운로드를**선택합니다.

    ![프로젝트 팜비츠](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. 다음 창에서 **deployer.log** 파일에 대한 경로를 입력합니다. 예를 들어 **팜비트 배포자.log를**입력합니다.

## <a name="sensor-telemetry"></a>센서 원격 분석

### <a name="cant-view-telemetry-data"></a>원격 분석 데이터를 볼 수 없습니다.

**증상**: 장치 또는 센서가 배포되고 FarmBeats를 장치 파트너와 연결했지만 FarmBeats에서 원격 분석 데이터를 얻거나 볼 수는 없습니다.

**시정 조치**:

1. FarmBeats Datahub 리소스 그룹으로 이동합니다.   

2. **이벤트** 허브(DatafeedEventHubNamespace)를 선택한 다음 들어오는 메시지 수를 확인합니다.

3. 다음 중 하나를 수행합니다.   
   - *들어오는 메시지가 없는*경우 장치 파트너에게 문의하십시오.  
   - *수신 메시지가*있는 경우 farmbeatssupport@microsoft.com에 문의하십시오. Datahub 및 가속기 로그와 캡처된 원격 분석을 연결합니다.

로그를 다운로드하는 방법을 이해하려면 ["수동으로 로그 수집"](#collect-logs-manually) 섹션으로 이동하십시오.  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>센서에서 기록/스트리밍 데이터를 수집한 후 원격 분석 데이터를 볼 수 없습니다.

**증상**: 장치 또는 센서가 배포되고 FarmBeats에서 장치/센서를 만들고 EventHub에 수집된 원격 분석을 생성했지만 FarmBeats에서 원격 분석 데이터를 얻거나 볼 수는 없습니다.

**시정 조치**:

1. 파트너 등록을 올바르게 수행했는지 확인 - datahub swagger로 이동하여 이를 확인하고 /파트너 API로 이동하여 Get을 수행하고 파트너가 등록되었는지 확인할 수 있습니다. 그렇지 않은 경우 [다음 단계를](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) 수행하여 파트너를 추가합니다.

2. 올바른 원격 분석 메시지 형식을 사용했습니까?

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Azure 이벤트 허브 연결 문자열이 없습니다.

**시정 조치**:

1. 데이터허브 스와거에서 파트너 API로 이동합니다.
2. **실행** > **해보고** >  **받기를**선택합니다.

> [!NOTE]
> 관심 있는 센서 파트너의 파트너 ID입니다.

3. 파트너 API로 돌아가서 **ID>\<받기를 **선택합니다.
4. 3단계에서 파트너 ID를 지정한 다음 **실행을**선택합니다.

   API 응답에는 이벤트 허브 연결 문자열이 있어야 합니다.

### <a name="device-appears-offline"></a>기기가 오프라인으로 나타납니다.

**증상**: 장치가 설치되어 있으며 FarmBeats를 장치 파트너와 연결했습니다. 장치가 온라인 상태가 되어 원격 분석 데이터를 전송하지만 오프라인으로 나타납니다.

**수정 작업**: 보고 간격이 이 장치에 대해 구성되지 않았습니다. 보고 간격을 설정하려면 장치 제조업체에 문의하십시오. 

### <a name="error-deleting-a-device"></a>장치 삭제 오류

장치를 삭제하는 동안 다음과 같은 일반적인 오류 시나리오 중 하나가 발생할 수 있습니다.  

**메시지**: "장치는 센서에서 참조됩니다: 장치와 연결된 센서가 하나 이상 있습니다. 센서를 삭제한 다음 장치를 삭제합니다."  

**의미**: 장치는 팜에 배포된 여러 센서와 연결됩니다.   

**시정 조치**:  

1. 가속기를 통해 장치와 연결된 센서를 삭제합니다.  
2. 센서를 다른 장치와 연결하려면 장치 파트너에게 동일한 작업을 수행하도록 요청하십시오.  
3. `DELETE API` 호출을 사용하여 장치를 삭제하고 force 매개 변수를 *true로*설정합니다.  

**메시지**: "장치가 ParentDeviceId로 장치에서 참조됨: 이 장치와 연결된 장치가 하나 이상의 장치입니다. 삭제한 다음 이 장치를 삭제하십시오."  

**의미**: 기기에 연결된 다른 장치가 있습니다.  

**정정 작업**

1. 이 특정 장치와 연결된 장치를 삭제합니다.  
2. 특정 장치를 삭제합니다.  

    > [!NOTE]
    > 센서가 연결된 경우 기기를 삭제할 수 없습니다. 관련 센서를 삭제하는 방법에 대한 자세한 내용은 [센서 파트너의 센서 데이터 얻기에서 센서](get-sensor-data-from-sensor-partner.md) **삭제** 섹션을 참조하십시오.

    > 파트너는 장치 또는 센서를 삭제할 수 없습니다. 관리자만 동일한 작업을 수행할 수 있습니다.


## <a name="issues-with-jobs"></a>작업 문제

### <a name="farmbeats-internal-error"></a>팜비츠 내부 오류

**메시지**: "FarmBeats 내부 오류, 자세한 내용은 문제 해결 가이드를 참조하십시오".

**정정 작업**: 이 문제는 데이터 파이프라인의 일시적인 오류로 인해 발생할 수 있습니다. 작업을 다시 만듭니다. 오류가 지속되면 FarmBeats 포럼의 게시물에 오류 메시지를 추가하거나 에 FarmBeatsSupport@microsoft.com문의하십시오.

## <a name="accelerator-troubleshooting"></a>가속기 문제 해결

### <a name="access-control"></a>Access Control

**문제**: 역할 할당을 추가하는 동안 오류가 발생합니다.

**메시지**: "일치하는 사용자를 찾을 수 없습니다."

**수정 작업**: 역할 할당을 추가하려는 전자 메일 ID를 확인합니다. 전자 메일 ID는 Active Directory에서 해당 사용자에 대해 등록된 ID와 정확히 일치해야 합니다. 오류가 지속되면 FarmBeats 포럼의 게시물에 오류 메시지를 추가하거나 에 FarmBeatsSupport@microsoft.com문의하십시오.

### <a name="unable-to-log-in-to-accelerator"></a>액셀러레이터에 로그인할 수 없음

**메시지**: "오류: 서비스를 호출할 권한이 없습니다. 관리자에게 권한을 요청하십시오."

**수정 작업**: 관리자에게 FarmBeats 배포에 액세스할 수 있는 권한을 부여하도록 요청합니다. 이 작업은 역할 할당 API의 POST를 수행하거나 가속기의 **설정** 창에서 액세스 제어를 통해 수행할 수 있습니다.  

이미 액세스 권한이 부여되었지만 이 오류가 발생중인 경우 페이지를 새로 고쳐 다시 시도하십시오. 오류가 지속되면 FarmBeats 포럼의 게시물에 오류 메시지를 추가하거나 에 FarmBeatsSupport@microsoft.com문의하십시오.

![프로젝트 팜비츠](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>가속기 문제  

**문제**: 결정되지 않은 원인의 가속기 오류가 발생했습니다.

**메시지**: "오류: 알 수 없는 오류가 발생했습니다."

**수정 작업**: 페이지를 너무 오래 유휴 상태로 두면 이 오류가 발생합니다. 페이지를 새로 고칩니다.  

오류가 지속되면 FarmBeats 포럼의 게시물에 오류 메시지를 추가하거나 에 FarmBeatsSupport@microsoft.com문의하십시오.

**문제**: FarmBeats 가속기는 FarmBeatsDeploy를 업그레이드 한 후에도 최신 버전을 표시하지 않습니다.

**정정 작업**: 이 오류는 브라우저의 서비스 작업자 지속성으로 인해 발생합니다. 다음을 수행합니다.

1. 가속기가 열려 있는 모든 브라우저 탭을 닫고 브라우저 창을 닫습니다.
2. 브라우저의 새 인스턴스를 시작하고 가속기 URI를 다시 로드합니다. 이 작업은 액셀러레이터의 새 버전을 로드합니다.

## <a name="sentinel-imagery-related-issues"></a>센티넬: 이미지 관련 문제

### <a name="wrong-username-or-password"></a>잘못된 사용자 이름 또는 암호

**작업 실패 메시지**: "이 리소스에 액세스하려면 전체 인증이 필요합니다."

**시정 조치**:

다음 중 하나를 수행합니다.

- 올바른 사용자 이름과 암호로 Datahub를 업그레이드하기 위해 설치 관리자를 다시 실행합니다.
- 실패한 작업을 다시 실행하거나 5~7일 의 날짜 범위에 대한 위성 인덱스 작업을 실행한 다음 작업이 성공했는지 확인합니다.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel 허브: 잘못된 URL 또는 사이트에 액세스할 수 없음 

**작업 실패 메시지**: "죄송합니다, 뭔가 잘못되었습니다. 액세스하려는 페이지는 (일시적으로) 사용할 수 없습니다." 

**시정 조치**:
1. 브라우저에서 [Sentinel을](https://scihub.copernicus.eu/dhus/) 열어 웹 사이트에 액세스할 수 있는지 확인합니다. 
2. 웹 사이트에 액세스할 수 없는 경우 방화벽, 회사 네트워크 또는 기타 차단 소프트웨어가 웹 사이트에 대한 액세스를 차단하고 있는지 확인한 다음 Sentinel URL을 허용하는 데 필요한 단계를 수행합니다. 
3. 실패한 작업을 다시 실행하거나 5~7일 의 날짜 범위에 대한 위성 인덱스 작업을 실행한 다음 작업이 성공했는지 확인합니다.  

### <a name="sentinel-server-down-for-maintenance"></a>센티넬 서버: 유지 관리를 위해 다운

**작업 실패 메시지**: "코페르니쿠스 오픈 액세스 허브가 곧 돌아올 것입니다! 불편을 끼쳐 드려 죄송합니다. 곧 다시 온라인 상태가 될 것입니다!" 

**시정 조치**:

센티넬 서버에서 유지 관리 작업을 수행하는 경우 이 문제가 발생할 수 있습니다.

1. 유지 관리가 수행중이기 때문에 작업또는 파이프라인에 오류가 발생하면 잠시 후 작업을 다시 제출합니다. 

   계획또는 계획되지 않은 센티넬 유지 관리 활동에 대한 자세한 내용은 [Copernicus 오픈 액세스 허브 뉴스](https://scihub.copernicus.eu/news/) 사이트로 이동하십시오.  

2. 실패한 작업을 다시 실행하거나 5~7일 의 날짜 범위에 대한 위성 인덱스 작업을 실행한 다음 작업이 성공했는지 확인합니다.

### <a name="sentinel-maximum-number-of-connections-reached"></a>센티넬: 도달한 최대 연결 수

**작업 실패 메시지**: "사용자\<이름> 의해 달성 된 두 개의 동시 흐름의 최대 수."

**의미:** 최대 연결 수에 도달했기 때문에 작업이 실패하면 다른 소프트웨어 배포에서 동일한 Sentinel 계정이 사용되고 있습니다.

**수정 작업**: 다음 중 하나를 시도하십시오.

* 새 Sentinel 계정을 만든 다음 설치 관리자를 다시 실행하여 새 Sentinel 사용자 이름과 암호를 사용하여 Datahub를 업그레이드합니다.  
* 실패한 작업을 다시 실행하거나 5~7일 의 날짜 범위에 대한 위성 인덱스 작업을 실행한 다음 작업이 성공했는지 확인합니다.

### <a name="sentinel-server-refused-connection"></a>센티넬 서버: 연결 거부 

**작업 실패 메시지**: "서버에서 연결을 http://172.30.175.69:8983/solr/dhus거부했습니다. 

**정정 작업**: Sentinel 서버에서 유지 관리 작업이 수행되는 경우 이 문제가 발생할 수 있습니다. 
1. 유지 관리가 수행중이기 때문에 작업또는 파이프라인에 오류가 발생하면 잠시 후 작업을 다시 제출합니다. 

   계획또는 계획되지 않은 센티넬 유지 관리 활동에 대한 자세한 내용은 [Copernicus 오픈 액세스 허브 뉴스](https://scihub.copernicus.eu/news/) 사이트로 이동하십시오.  

2. 실패한 작업을 다시 실행하거나 5~7일 의 날짜 범위에 대한 위성 인덱스 작업을 실행한 다음 작업이 성공했는지 확인합니다.

### <a name="soil-moisture-map-has-white-areas"></a>토양 수분지도에는 흰색 영역이 있습니다. 

**문제**: 토양 수분지도가 생성되었지만 맵에는 대부분 흰색 영역이 있습니다.

**정정 조치**: 맵이 요청된 시간 동안 생성된 위성 인덱스에 NDVI 값이 0.3 보다 적은 경우 이 문제가 발생할 수 있습니다. 자세한 내용은 [센티넬의 기술 가이드를](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)방문하십시오.
1. 다른 날짜 범위에 대한 작업을 다시 실행하고 위성 인덱스의 NDVI 값이 0.3 보다 작은지 확인합니다.

## <a name="collect-logs-manually"></a>수동으로 로그 수집

[Azure 저장소 탐색기를 설치하고 배포합니다.]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Datahub에서 Azure 데이터 팩터리 작업 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats Datahub 리소스 그룹을 검색합니다.

    > [!NOTE]
    > FarmBeats 설치 중에 지정한 Datahub 리소스 그룹을 선택합니다.

3. 리소스 **그룹** 대시보드에서 *datahublogs\* * 저장소 계정을 검색합니다. 예를 들어, **datahublogsmxmq를**검색합니다.  
4. **이름** 열에서 저장소 계정을 선택하여 **저장소 계정** 대시보드를 봅니다.
5. **\* datahubblogs** 창에서 **탐색기에서 열기를** 선택하여 **Azure 저장소 탐색기 열기** 응용 프로그램을 봅니다.
6. 왼쪽 창에서 **Blob 컨테이너를**선택한 다음 **작업 로그를 선택합니다.**
7. 작업 **로그** 창에서 **다운로드를**선택합니다.
8. 로그를 컴퓨터의 로컬 폴더로 다운로드합니다.
9. 로 다운로드한 .zip farmbeatssupport@microsoft.com파일을 이메일로 보풀어 주십시오.

    ![프로젝트 팜비츠](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>액셀러레이터에서 Azure 데이터 팩터리 작업 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats 가속기 리소스 그룹을 검색합니다.

    > [!NOTE]
    > FarmBeats 설치 중에 지정한 가속기 리소스 그룹을 선택합니다.

3. 리소스 **그룹** 대시보드에서 *저장소\* * 저장소 계정을 검색합니다. 예를 들어, **storagedop4k에\*** 대한 검색 .
4. **이름** 열에서 저장소 계정을 선택하여 **저장소 계정** 대시보드를 봅니다.
5. **저장소\* ** **창에서 탐색기에서 열기를** 선택하여 Azure 저장소 탐색기 응용 프로그램을 엽니다.
6. 왼쪽 창에서 **Blob 컨테이너를**선택한 다음 **작업 로그를 선택합니다.**
7. 작업 **로그** 창에서 **다운로드를**선택합니다.
8. 로그를 컴퓨터의 로컬 폴더로 다운로드합니다.
9. 로 다운로드한 .zip farmbeatssupport@microsoft.com파일을 이메일로 보풀어 주십시오.


### <a name="collect-datahub-app-service-logs"></a>데이터 허브 앱 서비스 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats Datahub 리소스 그룹을 검색합니다.

    > [!NOTE]
    > FarmBeats 설치 중에 지정한 Datahub 리소스 그룹을 선택합니다.

3. 리소스 그룹에서 *datahublogs\* * 저장소 계정을 검색합니다. 예를 들어, **에 대한 검색datahublogsmvxmq\***.
4. **이름** 열에서 저장소 계정을 선택하여 **저장소 계정** 대시보드를 봅니다.
5. **datahubblogs\* ** 창에서 **탐색기에서 열기를** 선택하여 Azure 저장소 탐색기 응용 프로그램을 엽니다.
6. 왼쪽 창에서 **Blob 컨테이너를**선택한 다음 **appinsights-log를 선택합니다.**
7. **앱인사이트-로그** 창에서 **다운로드를**선택합니다.
8. 로그를 컴퓨터의 로컬 폴더로 다운로드합니다.
9. 로 다운로드한 .zip farmbeatssupport@microsoft.com파일을 이메일로 보풀어 주십시오.

### <a name="collect-accelerator-app-service-logs"></a>액셀러레이터 앱 서비스 로그 수집

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **검색** 상자에서 FarmBeats 가속기 리소스 그룹을 검색합니다.

    > [!NOTE]
    > FarmBeats 설치 중에 제공된 FarmBeats 가속기 리소스 그룹을 선택합니다.

3. 리소스 그룹에서 *저장소\* * 저장소 계정을 검색합니다. 예를 들어, **storagedop4k에\*** 대한 검색 .
4. **이름** 열에서 저장소 계정을 선택하여 **저장소 계정** 대시보드를 봅니다.
5. **저장소\* ** **창에서 탐색기에서 열기를** 선택하여 Azure 저장소 탐색기 응용 프로그램을 엽니다.
6. 왼쪽 창에서 **Blob 컨테이너를**선택한 다음 **appinsights-log를 선택합니다.**
7. **앱인사이트-로그** 창에서 **다운로드를**선택합니다.
8. 로그를 컴퓨터의 로컬 폴더로 다운로드합니다.
9. 다운로드한 폴더를 farmbeatssupport@microsoft.com로 전자 메일로 보겠습니다.

## <a name="known-issues"></a>알려진 문제

## <a name="batch-related-issues"></a>일괄 처리 관련 문제

**오류 메시지:**"지정된 구독에 대한 일괄 처리 계정의 지역 계정 할당량에 도달했습니다."

**수정 작업**: 할당량을 늘리거나 사용되지 않는 일괄 처리 계정을 삭제하고 배포를 다시 실행합니다.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory(Azure AD) 관련 문제

**오류 메시지**: "Azure AD 앱 d41axx40-xx21-4fbd-8xxf-97xxx9e2xxcc0: 작업을 완료하기 에 필요한 설정을 업데이트할 수 없습니다. Azure AD 앱에 대해 위의 설정이 제대로 구성되었는지 확인합니다."

**의미**: Azure AD 앱 등록 구성이 제대로 완료되지 않았습니다.  

**수정 작업**: IT 관리자(테넌트 읽기 액세스 권한이 있는 사용자)에게 Azure AD 앱 등록을 만들기 위해 [스크립트를](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) 사용하도록 요청합니다. 이 스크립트는 구성 단계도 자동으로 처리합니다.

**오류 메시지**: "이 테넌트에서\<새\>Active Directory 응용 프로그램 '응용 프로그램 이름을 만들 수 없습니다.

**의미:** 이름이 같은 Azure AD 앱 등록이 이미 있습니다.

**수정 작업**: 기존 Azure AD 앱 등록을 삭제하거나 설치에 다시 사용합니다. 기존 Azure AD 앱 등록을 다시 사용하는 경우 응용 프로그램 ID 및 클라이언트 비밀을 설치 관리자에 전달하고 다시 배포합니다.

## <a name="issues-with-the-inputjson-file"></a>input.json 파일 문제

**오류**: *input.json* 파일에서 입력을 읽는 오류가 있습니다.

**정정 작업**: 이 문제는 일반적으로 설치 관리자에 올바른 *input.json* 파일 경로 또는 이름을 지정하는 오류로 인해 발생합니다. 적절한 수정을 하고 배포를 다시 시도합니다.

**오류**: *input.json* 파일에 값을 구문 분석하는 오류가 있습니다.

**정정 작업**: 이 문제는 주로 *input.json* 파일 내의 잘못된 값으로 인해 발생합니다. 적절한 수정을 하고 배포를 다시 시도합니다.

## <a name="high-cpu-usage"></a>높은 CPU 사용량

**오류**: **높은 CPU 사용**경고를 참조하는 전자 메일 경고를 받습니다. 

**시정 조치**: 
1. FarmBeats Datahub 리소스 그룹으로 이동합니다.
2. 앱 **서비스를**선택합니다.  
3. 앱 서비스 가격 책정 [페이지로](https://azure.microsoft.com/pricing/details/app-service/windows/)이동한 다음 적절한 가격 책정 계층을 선택합니다.

## <a name="next-steps"></a>다음 단계

여전히 FarmBeats 문제에 대해 겪고 있는 경우 [지원 포럼에](https://aka.ms/farmbeatssupport)문의하십시오.
