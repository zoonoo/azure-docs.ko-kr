---
title: '자습서: Azure에서 읽기 액세스 중복 저장소 액세스 오류 시뮬레이션 | Microsoft Docs'
description: 읽기 액세스 지역 중복 저장소 액세스 오류 시뮬레이션
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0cbb4d2bc6449dc1cf12a374085b429743224995
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650243"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>자습서: 읽기 액세스 중복 저장소 액세스 오류 시뮬레이션

이 자습서는 시리즈의 2부입니다. 여기서는 오류를 시뮬레이션하여 [RA-GRS(읽기 액세스 지역 중복 스토리지)](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage)의 이점에 대해 알아봅니다.

오류를 시뮬레이션하려면 [Fiddler](#simulate-a-failure-with-fiddler) 또는 [정적 라우팅](#simulate-a-failure-with-an-invalid-static-route)을 사용하면 됩니다. 각 방법으로 [RA-GRS(읽기 액세스 지역 중복)](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) 스토리지 계정의 기본 엔드포인트에 대한 요청 실패를 시뮬레이션할 수 있고, 애플리케이션이 대신 보조 엔드포인트에서 읽도록 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

시리즈 2부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 애플리케이션 실행 및 일시 중지
> * [Fiddler](#simulate-a-failure-with-fiddler) 또는 [잘못된 고정 경로](#simulate-a-failure-with-an-invalid-static-route)를 사용하여 실패를 시뮬레이션합니다. 
> * 기본 엔드포인트 복원 시뮬레이션

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 이전 자습서: [Azure Storage를 통해 애플리케이션 데이터의 고가용성 지원][previous-tutorial]을 완료하세요.

Fiddler를 사용하여 실패를 시뮬레이션하려면 다음을 수행합니다. 

* [Fiddler 다운로드 및 설치](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-fiddler"></a>Fiddler를 사용하여 실패 시뮬레이션

Fiddler를 사용하여 오류를 시뮬레이션하려면 요청에 대해 실패한 응답을 RA-GRS 스토리지 계정의 기본 엔드포인트에 삽입합니다.

다음 섹션에서는 오류를 시뮬레이션하고 Fiddler를 사용하여 기본 엔드포인트를 복원하는 방법을 설명합니다.

### <a name="launch-fiddler"></a>Fiddler 시작

Fiddler를 열고 **규칙**과 **규칙 사용자 지정**을 선택합니다.

![Fiddler 규칙 사용자 지정](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor가 시작되고 **SampleRules.js** 파일을 표시합니다. 이 파일을 사용하여 Fiddler를 사용자 지정합니다.

다음 코드 샘플을 `OnBeforeResponse` 함수에 붙여 넣습니다. 새 코드는 생성 논리가 즉시 구현되지 않도록 주석 처리가 제거됩니다.

완료되면 **파일**과 **저장**을 선택하여 변경 내용을 저장합니다.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![사용자 지정된 규칙 붙여넣기](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="interrupting-the-application"></a>애플리케이션 중단

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python 및 Java v7](#tab/dotnet-python-java-v7)

IDE 또는 셸에서 애플리케이션을 실행합니다.

애플리케이션이 기본 엔드포인트에서 읽기를 시작하면 콘솔 창에서 **아무 키**나 눌러 애플리케이션을 일시 중지합니다.

![시나리오 앱](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

IDE 또는 셸에서 애플리케이션을 실행합니다.

샘플을 제어할 수 있으므로 오류를 시뮬레이션하기 위해 중단할 필요가 없습니다. 샘플을 실행하고 **P**를 입력하여 스토리지 계정에 파일이 업로드되었는지 확인하기만 하면 됩니다.

![시나리오 앱](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>오류 시뮬레이션

애플리케이션이 일시 중지된 상태에서 Fiddler에 저장한 사용자 지정 규칙을 주석 처리 제거합니다.

코드 샘플에서는 RA-GRS 스토리지 계정에 대한 요청을 찾고, 경로에 `HelloWorld` 파일의 이름이 포함되어 있는 경우 `503 - Service Unavailable` 응답 코드를 반환합니다.

Fiddler로 이동하여 **규칙** -> **규칙 사용자 지정...** 을 선택합니다.

다음 줄의 주석 처리를 제거하고 `STORAGEACCOUNTNAME`을 스토리지 계정 이름으로 바꿉니다. **파일** -> **저장**을 선택하여 변경 내용을 저장합니다. 

> [!NOTE]
> Linux에서 샘플 애플리케이션을 실행하는 경우 Fiddler에서 사용자 지정 논리를 설치하기 위해 **CustomRule.js** 파일을 편집할 때마다 Fiddler를 다시 시작해야 합니다.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python 및 Java v7](#tab/dotnet-python-java-v7)

애플리케이션을 다시 시작하려면 **아무 키**를 누릅니다.

애플리케이션이 다시 실행되기 시작하면 기본 엔드포인트에 대한 요청이 실패하기 시작합니다. 애플리케이션이 기본 엔드포인트로 다시 연결을 5회 시도합니다. 5회의 실패 임계값 후에는 보조 읽기 전용 엔드포인트에서 이미지를 요청합니다. 애플리케이션이 보조 엔드포인트에서 이미지를 20회 성공적으로 검색하면 애플리케이션이 기본 엔드포인트에 연결을 시도합니다. 그래도 기본 엔드포인트에 연결할 수 없는 경우 애플리케이션은 보조 엔드포인트에서 읽기를 다시 시작합니다.

이 패턴은 이전 자습서에서 설명한 [회로 차단기](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) 패턴입니다.

![사용자 지정된 규칙 붙여넣기](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

오류가 도입되었으므로 **G**를 입력하여 오류를 테스트합니다.

기본 파이프라인이 아니라 보조 파이프라인을 사용 중임을 사용자에게 알려줍니다.

---

### <a name="simulate-primary-endpoint-restoration"></a>기본 엔드포인트 복원 시뮬레이션

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python 및 Java v7](#tab/dotnet-python-java-v7)

이전 단계에서의 Fiddler 사용자 지정 규칙 집합으로, 기본 엔드포인트에 대한 요청이 실패합니다.

기본 엔드포인트의 작동을 다시 시뮬레이션하려면 `503` 오류를 생성하는 논리를 삭제합니다.

애플리케이션을 일시 중지하려면 **아무 키**나 누릅니다.

Fiddler로 이동하여 **규칙**, **규칙 사용자 지정...** 을 선택합니다. 

`OnBeforeResponse` 함수에서 사용자 지정 논리를 주석 처리하거나 제거하여 기본 함수를 유지합니다.

**파일**, **저장**을 선택하여 변경 내용을 저장합니다.

![사용자 지정된 규칙 제거](media/storage-simulate-failure-ragrs-account-app/figure5.png)

완료되면 **아무 키**나 눌러 애플리케이션을 다시 시작합니다. 애플리케이션은 999 읽기에 도달할 때까지 기본 엔드포인트에서 읽기를 계속합니다.

![애플리케이션 다시 시작](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

이전 단계에서의 Fiddler 사용자 지정 규칙 집합으로, 기본 엔드포인트에 대한 요청이 실패합니다.

기본 엔드포인트의 작동을 다시 시뮬레이션하려면 `503` 오류를 생성하는 논리를 삭제합니다.

Fiddler로 이동하여 **규칙**, **규칙 사용자 지정...** 을 선택합니다.  `OnBeforeResponse` 함수에서 사용자 지정 논리를 주석 처리하거나 제거하여 기본 함수를 유지합니다.

**파일**, **저장**을 선택하여 변경 내용을 저장합니다.

완료되면 **G**를 입력하여 다운로드를 테스트합니다. 애플리케이션은 다시 기본 파이프라인을 사용 중임을 보고합니다.

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>잘못된 고정 경로를 사용하여 실패 시뮬레이션

[RA-GRS(읽기 액세스 지역 중복 저장소)](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) 계정의 기본 엔드포인트에 대한 모든 요청에 대해 잘못된 고정 경로를 만들 수 있습니다. 이 자습서에서는 로컬 호스트가 저장소 계정에 대한 요청을 라우팅하기 위한 게이트웨이로 사용됩니다. 로컬 호스트를 게이트웨이로 사용하면 저장소 계정의 기본 엔드포인트에 대한 모든 요청이 호스트 내부로 루프 백되어 실패가 발생하게 됩니다. 다음 단계에 따라 실패를 시뮬레이션하고 잘못된 고정 경로를 사용하여 기본 엔드포인트를 복원합니다. 

### <a name="start-and-pause-the-application"></a>애플리케이션 시작 및 일시 중지

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python 및 Java v7](#tab/dotnet-python-java-v7)

IDE 또는 셸에서 애플리케이션을 실행합니다. 애플리케이션이 기본 엔드포인트에서 읽기를 시작하면 콘솔 창에서 **아무 키**나 눌러 애플리케이션을 일시 중지합니다.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

샘플을 제어할 수 있으므로 오류를 테스트하기 위해 중단할 필요가 없습니다.

샘플을 실행하고 **P**를 입력하여 스토리지 계정에 파일이 업로드되었는지 확인합니다.

---

### <a name="simulate-failure"></a>오류 시뮬레이션

애플리케이션이 일시 중지된 상태에서 관리자 권한으로 Windows에서 명령 프롬프트를 시작하거나 Linux에서 루트로 터미널을 실행합니다.

명령 프롬프트 또는 터미널에서 다음 명령을 입력하여 스토리지 계정의 기본 엔드포인트 도메인에 대한 정보를 가져옵니다.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 `STORAGEACCOUNTNAME`을 저장소 계정 이름으로 바꿉니다. 나중에 사용하기 위해 저장소 계정의 IP 주소를 텍스트 편집기에 복사합니다.

로컬 호스트의 IP 주소를 가져오려면 Windows 명령 프롬프트에서 `ipconfig`를 입력하거나 Linux 터미널에서 `ifconfig`를 입력합니다. 

대상 호스트에 대한 정적 경로를 추가하려면 Windows 명령 프롬프트 또는 Linux 터미널에서 다음 명령을 입력합니다. 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a> Windows

`route add <destination_ip> <gateway_ip>`

`<destination_ip>`을 저장소 계정 IP 주소로, `<gateway_ip>`를 로컬 호스트 IP 주소로 바꿉니다.

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python 및 Java v7](#tab/dotnet-python-java-v7)

애플리케이션을 다시 시작하려면 **아무 키**를 누릅니다.

애플리케이션이 다시 실행되기 시작하면 기본 엔드포인트에 대한 요청이 실패하기 시작합니다. 애플리케이션이 기본 엔드포인트로 다시 연결을 5회 시도합니다. 5회의 실패 임계값 후에는 보조 읽기 전용 엔드포인트에서 이미지를 요청합니다. 애플리케이션이 보조 엔드포인트에서 이미지를 20회 성공적으로 검색하면 애플리케이션이 기본 엔드포인트에 연결을 시도합니다. 그래도 기본 엔드포인트에 연결할 수 없는 경우 애플리케이션은 보조 엔드포인트에서 읽기를 다시 시작합니다. 이 패턴은 이전 자습서에서 설명한 [회로 차단기](/azure/architecture/patterns/circuit-breaker) 패턴입니다.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

오류가 도입되었으므로 **G**를 입력하여 오류를 테스트합니다. 기본 파이프라인이 아니라 보조 파이프라인을 사용 중임을 사용자에게 알려줍니다.

---

### <a name="simulate-primary-endpoint-restoration"></a>기본 엔드포인트 복원 시뮬레이션

다시 작동하는 기본 엔드포인트를 시뮬레이션하려면 라우팅 테이블에서 기본 엔드포인트의 고정 경로를 삭제합니다. 이렇게 하면 기본 엔드포인트에 대한 모든 요청이 기본 게이트웨이를 통해 라우팅됩니다.

대상 호스트인 저장소 계정의 고정 경로를 삭제하려면 Windows 명령 프롬프트 또는 Linux 터미널에서 다음 명령을 입력합니다.

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a> Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python 및 Java v7](#tab/dotnet-python-java-v7)

애플리케이션을 다시 시작하려면 **아무 키**를 누릅니다. 애플리케이션은 999 읽기에 도달할 때까지 기본 엔드포인트에서 읽기를 계속합니다.

![애플리케이션 다시 시작](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

**G**를 입력하여 다운로드를 테스트합니다. 애플리케이션은 다시 기본 파이프라인을 사용 중임을 보고합니다.

![애플리케이션 다시 시작](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>다음 단계

시리즈 2부에서는 읽기 액세스 지역 중복 스토리지 오류를 시뮬레이션하는 방법에 대해 알아보았습니다.

RA-GRS 스토리지의 작동 방식 및 관련 위험에 대해 자세히 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [RA-GRS를 사용하여 HA 앱 디자인](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
