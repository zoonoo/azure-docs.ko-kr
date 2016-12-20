---
title: "DocumentDB 스크립트 탐색기, JavaScript 편집기 | Microsoft Docs"
description: "저장 프로시저, 트리거 및 사용자 정의 함수를 포함하여 DocumentDB 서버 쪽 프로그래밍 아티팩트를 관리하는 Azure 포털 도구인 DocumentDB 스크립트 탐색기에 대해 알아봅니다."
keywords: "javascript 편집기"
services: documentdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 9d0620da-2449-4c17-82a4-24aaa46e9b3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 70d08de5e7ba775578ea1765ea1c0dda9b04e427


---
# <a name="create-and-run-stored-procedures-triggers-and-user-defined-functions-using-the-documentdb-script-explorer"></a>저장 프로시저, 트리거 및 DocumentDB 스크립트 탐색기를 사용하는 사용자 정의 함수 만들기 및 실행
이 문서에서는 저장 프로시저, 트리거 및 사용자 정의 함수를 포함하여 DocumentDB 서버 쪽 프로그래밍 아티팩트를 보고 실행할 수 있도록 하는 Azure 포털의 JavaScript 편집기인 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 스크립트 탐색기의 개요를 제공합니다. DocumentDB 서버 쪽 프로그래밍에 대한 자세한 내용은 [Stored procedures, database triggers, and UDFs](documentdb-programming.md) (저장 프로시저, 데이터베이스 트리거 및 UDF) 문서를 참조하세요.

## <a name="launch-script-explorer"></a>스크립트 탐색기 시작
1. Azure 포털의 이동 표시줄에서 **DocumentDB(NoSQL)**를 클릭합니다. **DocumentDB 계정**이 표시되지 않으면 **서비스 추가**를 클릭한 다음 **DocumentDB(NoSQL)**를 클릭합니다.
2. 리소스 메뉴에서 **스크립트 탐색기**를 클릭합니다.
   
    ![스크립트 탐색기 명령의 스크린샷](./media/documentdb-view-scripts/scriptexplorercommand.png)
   
    **데이터베이스** 및 **컬렉션** 드롭다운 목록 상자는 스크립트 탐색기를 실행하는 컨텍스트에 따라 미리 채워집니다.  예를 들어 데이터베이스 블레이드에서 실행할 경우 현재 데이터베이스가 미리 채워집니다.  컬렉션 블레이드에서 실행할 경우에는 현재 컬렉션이 미리 채워집니다.
3. **데이터베이스** 및 **컬렉션** 드롭다운 목록 상자를 사용하면 스크립트 탐색기를 닫았다가 다시 시작할 필요 없이 현재 스크립트를 보고 있는 컬렉션을 쉽게 변경할 수 있습니다.  
4. 또한 스크립트 탐색기에서는 현재 로드된 스크립트 집합을 ID 속성별로 필터링할 수 있습니다.  필터 상자에 입력하기만 하면 스크립트 탐색기 목록의 결과가 제공된 기준에 따라 필터링됩니다.
   
    ![필터링된 결과와 함께 스크립트 탐색기의 스크린샷](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)

    > [AZURE.IMPORTANT] 스크립트 탐색기 필터 기능은 ***현재*** 로드된 스크립트 집합만 필터링하고 현재 선택한 컬렉션을 자동으로 새로 고치지 않습니다.

1. 스크립트 탐색기에서 로드한 스크립트 목록을 새로 고치려면, 블레이드 맨 위에 있는 **새로 고침** 명령을 클릭하면 됩니다.
   
    ![스크립트 탐색기 새로 고침 명령의 스크린샷](./media/documentdb-view-scripts/scriptexplorerrefresh.png)

## <a name="create-view-and-edit-stored-procedures-triggers-and-user-defined-functions"></a>저장 프로시저, 트리거 및 사용자 정의 함수 만들기, 보기 및 편집
스크립트 탐색기를 사용하면 DocumentDB 서버 쪽 프로그래밍 아티팩트에 대해 쉽게 CRUD 작업을 수행할 수 있습니다.  

* 스크립트를 만들려면 스크립트 탐색기 내에서 해당하는 만들기 명령을 클릭하고 ID를 제공한 다음 스크립트 내용을 입력하고 **저장**을 클릭합니다.
  
    ![JavaScript 편집기를 보여 주는 스크립트 탐색기 만들기 옵션의 스크린샷](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)
* 트리거를 만드는 경우 트리거 유형 및 트리거 작업도 지정해야 합니다.
  
    ![스크립트 탐색기 트리거 만들기 옵션의 스크린샷](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)
* 스크립트를 보려면 관심있는 스크립트를 클릭하면 됩니다.
  
    ![스크립트 탐색기 보기 스크립트 환경의 스크린샷](./media/documentdb-view-scripts/scriptexplorerviewscript.png)
* 스크립트를 편집하려면 JavaScript 편집기에서 원하는 대로 변경하고 **저장**을 클릭합니다.
  
    ![스크립트 탐색기 보기 스크립트 환경의 스크린샷](./media/documentdb-view-scripts/scriptexplorereditscript.png)
* 스크립트에 대한 보류 중인 변경 내용을 모두 취소하려면 **취소** 명령을 클릭하면 됩니다.
  
    ![스크립트 탐색기 변경 내용 취소 환경의 스크린샷](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)
* 문서 탐색기에서는 **속성** 명령을 클릭하여 현재 로드된 스크립트의 시스템 속성을 쉽게 볼 수도 있습니다.
  
    ![스크립트 탐색기 스크립트 속성 보기의 스크린샷](./media/documentdb-view-scripts/scriptproperties.png)
  
  > [!NOTE]
  > 타임스탬프(_ts) 속성은 내부적으로 epoch 시간으로 표시되지만 스크립트 탐색기에서는 이 값을 사람이 읽을 수 있는 GMT 형식으로 표시합니다.
  > 
  > 
* 스크립트를 삭제하려면 스크립트 탐색기에서 스크립트를 선택하고 **삭제** 명령을 클릭합니다.
  
    ![스크립트 탐색기 삭제 명령의 스크린샷](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)
* **예**를 클릭하여 삭제 작업을 확인하거나, **아니요**를 클릭하여 삭제 작업을 취소합니다.
  
    ![스크립트 탐색기 삭제 명령의 스크린샷](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## <a name="execute-a-stored-procedure"></a>저장 프로시저 실행
> [!WARNING]
> 스크립트 탐색기에서 저장 프로시저의 실행은 서버 쪽 분할된 컬렉션에 대해 아직 지원되지 않습니다. 자세한 내용은 [DocumentDB의 분할 및 크기 조정](documentdb-partition-data.md)을 참조하세요.
> 
> 

스크립트 탐색기를 사용하면 Azure 포털에서 서버 쪽 저장 프로시저를 실행할 수 있습니다.

* 새로 만든 저장 프로시저 블레이드를 열면 기본 스크립트(*prefix*)가 이미 제공되어 있습니다. *prefix* 스크립트 또는 자체 스크립트를 실행하려면 *id* 및 *inputs*를 추가합니다. 여러 매개 변수를 허용하는 저장 프로시저의 경우 *["foo", "bar"]*와 같이 모든 입력이 배열 내에 있어야 합니다.
  
    ![입력을 추가하고 저장 프로시저를 실행하는 스크립트 탐색기 저장 프로시저 블레이드의 스크린샷](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)
* 저장 프로시저를 실행하려는 경우 스크립트 편집기 창에서 **저장 후 실행** 명령을 클릭하면 됩니다.
  
  > [!NOTE]
  > **저장 후 실행** 명령은 실행하기 전에 저장 프로시저를 저장하여 이전에 저장된 저장 프로시저 버전을 덮어씁니다.
  > 
  > 
* 저장 프로시저 실행이 성공하면 *저장 프로시저를 저장 및 실행했습니다.* 상태가 설정되며 반환된 결과가 *결과* 창에 채워집니다.
  
    ![저장 프로시저를 실행하는 스크립트 탐색기 저장 프로시저 블레이드의 스크린샷](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)
* 실행 중 오류가 발생하면 *결과* 창에 오류가 채워집니다.
  
    ![스크립트 탐색기 스크립트 속성 보기의 스크린샷 오류가 있는 저장 프로시저 실행](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## <a name="work-with-scripts-outside-the-portal"></a>포털 외부 스크립트 작업
Azure 포털의 스크립트 탐색기는 저장 프로시저, 트리거 및 DocumentDB의 사용자 정의 함수를 사용하는 방법 중 한 가지일 뿐입니다. REST API 및 [클라이언트 SDK](documentdb-sdk-dotnet.md)를 사용하여 스크립트 작업을 수행할 수도 있습니다. REST API 설명서에는 [REST를 사용하는 저장 프로시저](https://msdn.microsoft.com/library/azure/mt489092.aspx), [REST를 사용하는 사용자 정의 함수](https://msdn.microsoft.com/library/azure/dn781481.aspx) 및 [REST를 사용하는 트리거](https://msdn.microsoft.com/library/azure/mt489116.aspx) 작업을 위한 샘플이 포함되어 있습니다. 또한 [C#을 사용하는 스크립트 작업](documentdb-dotnet-samples.md#server-side-programming-examples) 및 [Node.js를 사용하는 스크립트 작업](documentdb-nodejs-samples.md#server-side-programming-examples) 방법을 설명하는 샘플도 제공됩니다.

## <a name="next-steps"></a>다음 단계
DocumentDB 서버 쪽 프로그래밍에 대한 자세한 내용은 [저장 프로시저, 데이터베이스 트리거 및 UDF](documentdb-programming.md) 문서를 참조하세요.

[학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/) 도 DocumentDB에 대해 자세히 파악할 수 있는 유용한 리소스입니다.  




<!--HONumber=Nov16_HO3-->


