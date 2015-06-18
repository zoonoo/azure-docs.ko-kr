<properties 
	pageTitle="Azure 기계 학습에서 사용자 지정 R 모듈 작성 | Azure" 
	description="Azure 기계 학습에서 사용자 지정 R 모듈을 작성하기 위한 빠른 시작입니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/04/2015" 
	ms.author="bradsev" />


# Azure 기계 학습에서 사용자 지정 R 모듈 작성

이 항목에서는 Azure 기계 학습에서 사용자 지정 R 모듈을 작성하는 방법을 간략하게 설명합니다. 사용자 지정 R 모듈의 정의와 이를 정의하는 데 사용되는 파일을 설명합니다. 또한 이러한 파일을 생성하여 기계 학습 작업 영역에서 배포용 모듈을 등록하는 방법을 보여 줍니다. 그런 다음 사용자 지정 모듈의 정의에 사용되는 요소 및 특성에 대해 자세히 설명합니다. 보조 기능과 파일 및 여러 출력을 사용하는 방법도 간략하게 소개합니다. 

## 사용자 지정 R 모듈이란?
사용자 지정 모듈은 사용자의 작업 영역에 업로드하고 Azure 기계 학습 실험의 일부로 실행할 수 있는 사용자 정의 모듈입니다. 사용자 지정 R 모듈은 기계 학습에서 사용자 정의 R 함수를 실행하는 사용자 지정 모듈입니다. R은 통계학자 및 데이터 과학자가 알고리즘을 구현하는 데 널리 사용되는 통계 컴퓨팅 및 그래픽용 프로그래밍 언어입니다. 기계 학습 언어 지원은 사용자 지정 모듈에서 기본적으로 R 언어를 사용하도록 설정됩니다.

사용자 지정 모듈은 다른 모든 모듈처럼 사용할 수 있다는 점에서 Azure 기계 학습에서 첫 번째 클래스 상태를 유지합니다. 다른 모듈과 함께 실행하거나, 게시된 실험에 포함하거나, 시각화할 수 있습니다. 사용자는 모듈에 의해 구현되는 알고리즘, 사용할 입력 및 출력 포트, 모델링 매개 변수 및 기타 여러 런타임 동작을 제어할 수 있습니다.


## 사용자 지정 R 모듈의 파일
사용자 지정 R 모듈은 최소한 다음 두 개의 파일을 포함하는 .zip 파일로 정의됩니다.

* 모듈에 의해 노출되는 R 함수를 구현하는 파일
* 사용자 지정 모듈을 설명하는 XML 정의 파일

사용자 지정 모듈에서 액세스할 수 있는 기능을 제공하는 추가 보조 파일을 .zip 파일에 포함할 수도 있습니다. 이 옵션은 아래에 설명되어 있습니다.

## 빠른 시작 예제
이 예제에서는 사용자 지정 R 모듈에 필요한 파일을 생성하고 이를 zip 파일에 패키지한 다음 기계 학습 작업 영역에 모듈을 등록하는 방법을 보여 줍니다.

두 데이터 집합(데이터 프레임)의 행(관찰)을 연결하는 데 사용되는 행 추가 모듈의 표준 구현을 수정하는 사용자 지정 My Add Rows 모듈의 예제를 고려해 보겠습니다. 행 추가 모듈은 rbind 알고리즘을 사용하여 두 번째 입력 데이터 집합의 행을 첫 번째 입력 데이터 집합의 끝에 추가합니다. 사용자 지정 `myAddRows` 함수는 두 데이터 집합을 허용할 뿐만 아니라 추가 부울 스왑 매개 변수도 입력으로 허용합니다. 스왑 매개 변수가 **FALSE**이면 표준 구현과 동일한 데이터 집합을 반환합니다. 그러나 스왑 매개 변수가 **TRUE**인 경우에는 대신 첫 번째 입력 데이터 집합의 행을 두 번째 데이터 집합의 끝에 추가합니다. My Add Rows 모듈에 의해 노출되는 R myAddRows 함수를 구현하는 파일은 다음 R 코드를 포함합니다.

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (dataset)
	}

이 `myAddRows` 함수를 Azure 기계 학습 모듈로 노출하려면 XML 정의 파일을 만들어 My Add Rows 모듈의 표시 및 동작 방식을 지정해야 합니다. 

	<Module Version="v0.00.1" type="Public" insync="false" Owner="myName">
	  <GUID>{1CE529D1-B9D2-496F-AB42-8DBA60DE8279}</GUID>
	  <ID>myAddRows</ID>
	  <Name>My Add Rows</Name>	
	  <State>Custom</State>
	  <Description>This is my module description. </Description>
	  <Language Name="R" EntryPointFile="myAddRows.R" EntryPoint="myAddRows" />  
	    <Ports>
	      <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	      </output>
	      <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	      </input>
	      <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	      </input>
	    </Ports>
	    <Arguments>
	      <arg id="swap" display="swap" type="bool" >
	        <Description>Swaps inputs</Description>
	      </arg>
	    </Arguments>
	  <Category>My Category</Category>
	</Module>

 
XML 파일의 ID 요소 내용은 함수 이름과 정확히 일치해야 합니다. 

이 두 파일을 *myAddRows.R*과 *myAddRows.xml*로 저장한 다음 *myAddRows.zip* 파일로 압축합니다.

이 파일을 기계 학습 작업 영역에 등록하려면 기계 학습 스튜디오의 작업 영역으로 이동한 후 아래쪽에서 **+새로 만들기** 단추를 클릭하고 **모듈 -> Zip 패키지**를 선택하여 새 사용자 지정 My Add Rows 모듈을 업로드합니다.

![](http://i.imgur.com/RFJhCls.png)

이제 기계 학습 실험에서 My Add Rows 모듈에 액세스할 준비가 완료되었습니다.

## XML 정의 파일의 요소

### 입력 및 출력
사용자 지정 모듈의 입력 및 출력은 XML 정의 파일의 포트 섹션 하위 요소에 지정됩니다. 이러한 입력 및 출력 요소의 순서에 따라 사용자의 레이아웃(UX)이 결정됩니다. XML 파일의 포트 요소에 나열된 첫 번째 하위는  기계 학습 UX의 맨 왼쪽 입력 포트가 됩니다. 입력 및 출력 포트에 지원되는 데이터 형식은 다음과 같습니다. 

**DataTable**: 이 형식은 data.frame으로 R 함수에 전달됩니다. 실제로 기계 학습에서 지원되고 DataTable과 호환되는 모든 형식(예: CSV 파일 또는 ARFF 파일)은  자동으로 data.frame으로 변환됩니다. 

       <input id="dataset1" display="Input 1" type="DataTable" IsOptional="false">
           <Description>Input Dataset 1</Description>
       </input>

**Zip**: 사용자 지정 모듈에서는 zip 파일을 입력으로 사용할 수 있습니다. 이 입력은 함수의 실행 디렉터리에 압축이 해제됩니다.

       <input id="zippedData" display="Zip Input" type="Zip" IsOptional="false">
           <Description>Zip Input for port</Description>
       </input>

각 입력 및 출력 포트와 연결된 ID 특성에는 고유 값이 있어야 하며, 이러한 값은 R 함수의 명명된 매개 변수와 일치해야 합니다. 또한 입력이 선택 사항인 특성에 대한 기본값을 지정해야 합니다. IsOptional 특성은 DataTable 및 Zip 형식에 둘 다에 대해 선택 사항이며, 기본적으로 false입니다. 이 기본값은 입력 형식이 선택 사항이 아님을 나타냅니다.


### 매개 변수
사용자 지정 모듈의 매개 변수는 XML 정의 파일의 인수 섹션 하위 요소에 지정됩니다. 포트 섹션의 하위 요소와 마찬가지로 인수 섹션의 매개 변수 순서는 UX 레이아웃을 정의합니다. 나열된 첫 번째 매개 변수는 첫 번째 함수 매개 변수가 됩니다. 기계 학습에서 지원하는 매개 변수 형식은 다음과 같습니다. 선택적 특성은 XML 정의에 해당 기본값이 지정되어야 합니다. 이는 함수를 사용할 때 매개 변수 값이 지정되지 않은 경우에 사용되는 값입니다. 각 형식에 대한 선택적 특성이 표시되어 있습니다.


**int** - 정수(32비트) 형식 매개 변수입니다.

       <arg id="intValue1" display="My int Param" type="int" IsOptional="false" MinValue="0" MaxValue="100">
           <Description>Integer Parameter 1</Description>
       </arg>

int의 선택 사항은 IsOptional, MinValue 및 MaxValue입니다.

**double** - 실수(Double) 형식 매개 변수입니다.

       <arg id="doubleValue1" display="My double Param" type="double" IsOptional="false" min="0.000" max="0.999" default="0.3">
           <Description>Double Parameter 1</Description>
       </arg>
double의 선택 사항은 IsOptional, min, max 및 default입니다.

**bool** - UX에서 확인란으로 표시되는 부울 매개 변수입니다.

       <arg id="boolValue1" display="My boolean Param" type="bool" default="true">
           <Description>Boolean Parameter 1 </Description>
       </arg>

bool의 선택 사항은 default입니다.

**string**: 표준 문자열입니다.

        <arg id="stringValue1" display="My string Param" type="string" default="default value" IsOptional="true">
           <Description>String Parameter 1</Description>
        </arg>

string의 선택 사항은 default 및 IsOptional입니다.

**ColumnPickerFor**: 열 선택 매개 변수입니다. 이 형식은 UX에서 열 선택기로 렌더링됩니다. 열을 선택하는 DataTable의 ID가 형식 특성 값의 테이블 부분을 대체합니다. 변수는 문자열 목록으로 함수에 전달됩니다. 

        <arg id="columnSelection1" display="My Column Param" type="ColumnPickerFor:table">
           <Description>My column selector Param 1</Description>		
        </arg>

예를 들어 ID가 dataset1인 DataTable이 있는 경우 형식은 다음과 같습니다. 

		type="ColumnPickerFor:dataset1" 
                            
**enum:<DropDown Type ID>**: 열거(드롭다운) 목록입니다. 선택한 값은 문자열로 R 함수에 전달됩니다. 이 형식을 사용하려면 먼저 인수 섹션 내에서 유효한 열거 값을 정의해야 합니다.

       <DropDownType id="myDropDown1">
           <o id="red" display="Red"/>
           <o id="yellow" display="Yellow"/>
           <o id="blue" display="Blue"/>
       </DropDownType>
       <arg id="enum1" display="My Enum Param" default="red" type="enum:myDropDown1">
           <Description>My Enum Param 1</Description>
       </arg>

입력 및 출력과 마찬가지로 각 매개 변수에는 고유한 ID 값이 연결되어 있어야 합니다. 또한 ID 값은 R 함수의 명명된 매개 변수에 해당해야 합니다. 이 빠른 시작 예제에서 연결된 ID/매개 변수는 *swap*입니다.

### 언어 정의
XML 정의 파일의 언어 요소는 언어별 기능을 정의합니다. R 모듈의 경우 일반적으로 다음과 같습니다. 

	<Language Name="R" EntryPointFile="myFunc.R" EntryPoint="myFunc" AddDisplayOutputPort="false"/>

이는 특정 언어, 함수가 정의된 파일 및 해당 정의의 진입점을 지정합니다. AddDisplayOutputPort 특성은 언어 요소의 선택 사항입니다. R 스크립트 실행 모듈(링크 TBD)과 마찬가지로, 차트/그래프를 시각화하는 데 사용할 수 있는 출력 포트를 추가하려면 AddDisplayOutputPort 태그에 대해 *true*를 선택합니다. 그러면 추가 출력 포트가 표시됩니다. 

### 보조 기능

이 예제에는 나와 있지 않지만 사용자 지정 모듈 작성자가 사용할 수 있는 여러 특성이 있습니다. 예를 들어 모듈의 동작은 결정적이거나 비결정적일 수 있습니다. 결정적 모듈은 동일한 입력 데이터와 동일한 매개 변수 구성이 주어진 경우 두 번 실행되지 않습니다. 대신 캐시된 결과가 사용되며 모든 다운스트림 모듈에 전파됩니다. Azure 기계 학습의 결정적 모듈에 대한 예는 행 추가 모듈입니다. 비결정적 모듈의 예는 판독기입니다. 사용자 지정 모듈을 비결정적 모듈로 설정하려면 다음 특성을 정의에 추가하여 기본 설정을 변경합니다.

	<IsDeterministic>false</IsDeterministic>

### 보조 파일

사용자 지정 모듈 ZIP 파일에 있는 모든 파일은 실행 시간 동안 사용할 수 있습니다. 디렉터리 구조가 있는 경우 해당 구조가 그대로 유지됩니다. 따라서 파일 소싱이 로컬과 Azure 기계 학습 실행에서 동일하게 작동합니다. 

예를 들어 데이터 집합을 myAddRows에서 출력하기 전에 NA가 있는 모든 행과 모든 중복 행을 데이터 집합에서 제거하려는 경우 removeDupNARows.R 파일에서 이 작업을 수행하는 R 함수를 이미 작성했다고 가정해 보겠습니다.

	removeDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
myAddRows 함수에서 보조 파일 removeDupNARows.R을 소싱할 수 있습니다.

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
		source("removeDupNARows.R")
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	dataset <- removeDupNARows(dataset)
	return (dataset)
	}

그런 다음 'myAddRows.R', 'myAddRows.xml' 및 'removeDupNARows.R'이 포함된 zip 파일을 사용자 지정 R 모듈로 업로드합니다.

### 여러 출력

지원되는 데이터 형식의 개체를 두 개 이상 출력하려면 XML 정의 파일에 해당 출력 포트를 지정하고 개체를 목록으로 반환해야 합니다. 출력 개체는 반환되는 목록에서 개체가 배치된 순서를 나타내도록 왼쪽으로 오른쪽으로 출력 포트에 할당됩니다.
 
예를 들어 dataset, Dataset1 및 Dataset2를 각각 왼쪽에서 오른쪽 순으로 출력 포트 dataset, dataset1 및 dataset2로 출력하려면 'myAddRows.xml' 파일에서 출력 포트를 다음과 같이 정의합니다.

	<Ports>
	    <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset1" display="dataset1" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset2" display="dataset2" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
	    <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	    </input>
	    <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	    </input>
	</Ports>

그런 다음 'myAddRows.R'에서 올바른 순서로 목록에 있는 개체 목록을 반환합니다.

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (list(dataset, Dataset1, Dataset2))
	}

## 실행 환경

TBD

## 다음 단계

TBD

<!--HONumber=49--> 