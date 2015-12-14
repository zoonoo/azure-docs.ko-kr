<properties
	pageTitle="저장된 프로시저, 트리거 및 DocumentDB 스크립트 탐색기를 사용한 사용자 정의 함수 보기 | Microsoft Azure"
	description="저장된 프로시저, 트리거 및 사용자 정의 함수를 포함하여 DocumentDB 서버 쪽 프로그래밍 아티팩트를 볼 수 있도록 하는 Azure 포털 도구, DocumentDB 스크립트 탐색기에 대해 알아봅니다."
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="12/01/2015"
	ms.author="anhoh"/>

# 저장된 프로시저, 트리거 및 DocumentDB 스크립트 탐색기를 사용한 사용자 정의 함수 보기, 편집 및 만들기

이 문서에서는 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) 스크립트 탐색기, 저장된 프로시저, 트리거 및 사용자 정의 함수를 포함하여 DocumentDB 서버 쪽 프로그래밍 아티팩트를 볼 수 있도록 하는 Azure 포털 도구를 간략하게 설명합니다. [여기](documentdb-programming.md)에서 DocumentDB 서버 쪽 프로그래밍에 대해 읽어 보세요.

이 자습서를 완료하면 다음 질문에 대답할 수 있습니다.

-	웹 브라우저를 통해 DocumentDB 저장 프로시저를 쉽게 볼 수 있는 방법
-	웹 브라우저를 통해 DocumentDB 트리거를 쉽게 볼 수 있는 방법
-	웹 브라우저를 통해 DocumentDB 사용자 정의 함수를 쉽게 볼 수 있는 방법

## 스크립트 탐색기 실행 및 탐색

스크립트 탐색기는 DocumentDB 계정, 데이터베이스 및 컬렉션 블레이드 중 어디에서나 실행할 수 있습니다.

1. DocumentDB 계정 또는 데이터베이스 블레이드의 위에서 **스크립트 탐색기** 명령을 클릭하면 됩니다.

	![스크립트 탐색기 명령의 스크린샷](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
2. 또는, 각 블레이드의 아래쪽에 **스크립트 탐색기** 부분을 포함하는 **개발자 도구** 렌즈가 있습니다.

	![스크립트 탐색기 부분의 스크린샷](./media/documentdb-view-scripts/scriptexplorerpart.png)

2. 스크립트 탐색기를 시작하려면 명령 또는 부분을 클릭하면 됩니다.

	<p>**데이터베이스** 및 **컬렉션** 드롭다운 목록 상자는 스크립트 탐색기를 실행하는 컨텍스트에 따라 미리 채워집니다. 예를 들어 데이터베이스 블레이드에서 실행할 경우 현재 데이터베이스가 미리 채워집니다. 컬렉션 블레이드에서 실행할 경우에는 현재 컬렉션이 미리 채워집니다.

	![스크립트 탐색기의 스크린샷](./media/documentdb-view-scripts/scriptexplorerinitial.png)


3. **데이터베이스** 및 **컬렉션** 드롭다운 목록 상자를 사용하면 스크립트 탐색기를 닫았다가 다시 실행할 필요 없이 현재 스크립트를 보고 있는 컬렉션을 쉽게 변경할 수 있습니다.

4. 또한 스크립트 탐색기에서는 현재 로드된 스크립트 집합을 ID 속성별로 필터링할 수 있습니다. 필터 상자에 입력하면 됩니다.

	![강조 표시하는 필터와 함께 스크립트 탐색기의 스크린샷](./media/documentdb-view-scripts/scriptexplorerfilter.png)

	또한 스크립트 탐색기 목록의 결과는 제공된 기준에 따라 필터링됩니다.

	![필터링된 결과와 함께 스크립트 탐색기의 스크린샷](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


	> [AZURE.IMPORTANT]스크립트 탐색기 필터 기능은 ***현재*** 로드된 스크립트 집합만 필터링하고 현재 선택한 컬렉션을 자동으로 새로 고치지 않습니다.

5. 스크립트 탐색기에서 로드한 스크립트 목록을 새로 고치려면, 블레이드 맨 위에 있는 **새로 고침** 명령을 클릭하면 됩니다.

	![스크립트 탐색기 새로 고침 명령의 스크린샷](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## 스크립트 탐색기를 사용하여 저장된 프로시저, 트리거 및 사용자 정의 함수 보기, 편집, 만들기 및 삭제

스크립트 탐색기를 사용하면 DocumentDB 서버 쪽 프로그래밍 아티팩트에 대해 쉽게 CRUD 작업을 수행할 수 있습니다.

- 스크립트를 만들려면 스크립트 탐색기 내에서 해당하는 만들기 명령을 클릭하고 ID를 제공한 다음 스크립트 내용을 입력하고 **저장** 명령을 클릭하면 됩니다.

	![스크립트 탐색기 만들기 옵션의 스크린샷](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- 트리거를 만드는 경우 트리거 유형 및 트리거 작업도 지정해야 합니다.

	![스크립트 탐색기 트리거 만들기 옵션의 스크린샷](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- 스크립트를 보려면 관심있는 스크립트를 클릭하면 됩니다.

	![스크립트 탐색기 보기 스크립트 환경의 스크린샷](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- 스크립트를 편집하려면 원하는 대로 변경하고 **저장** 명령을 클릭하면 됩니다.

	![스크립트 탐색기 보기 스크립트 환경의 스크린샷](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- 스크립트에 대한 보류 중인 변경 내용을 모두 취소하려면 **취소** 명령을 클릭하면 됩니다.

	![스크립트 탐색기 변경 내용 취소 환경의 스크린샷](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- 문서 탐색기에서는 **속성** 명령을 클릭하여 현재 로드된 스크립트의 시스템 속성을 쉽게 볼 수도 있습니다.

	![스크립트 탐색기 스크립트 속성 보기의 스크린샷](./media/documentdb-view-scripts/scriptproperties.png)

	> [AZURE.NOTE]타임스탬프(\_ts) 속성은 내부적으로 epoch 시간으로 표시되지만 스크립트 탐색기에서는 이 값을 사람이 읽을 수 있는 GMT 형식으로 표시합니다.

- 스크립트를 삭제하려면 스크립트 탐색기에서 스크립트를 선택하고 **삭제** 명령을 클릭합니다.

	![스크립트 탐색기 삭제 명령의 스크린샷](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- **예**를 클릭하여 삭제 작업을 확인하거나, **아니요**를 클릭하여 삭제 작업을 취소합니다.

	![스크립트 탐색기 삭제 명령의 스크린샷](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## 다음 단계

DocumentDB에 대해 자세히 알아보려면 [여기](http://azure.com/docdb)를 클릭하세요.
 

<!---HONumber=AcomDC_1203_2015-->