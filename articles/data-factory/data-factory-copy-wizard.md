<properties
	pageTitle="데이터 팩터리 복사 마법사 | Microsoft Azure"
	description="Data Factory 복사 마법사를 사용하여 지원되는 데이터 소스의 데이터를 싱크로 복사하는 방법에 대해 알아보세요."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2016"
	ms.author="spelluru"/>

# 데이터 팩터리 복사 마법사
Azure Data Factory 복사 마법사를 사용하면 연결된 서비스, 데이터 집합 및 파이프라인에 대한 JSON 정의를 작성하지 않고도 지원되는 원본에서 대상으로 데이터를 복사하는 파이프라인을 만들 수 있습니다. 복사 마법사를 시작하려면 Data Factory 홈 페이지에서 **데이터 복사** 타일을 클릭합니다.

![복사 마법사](./media/data-factory-copy-wizard/copy-data-wizard.png)

## 기능

### 데이터를 복사할 수 있는 직관적이고 원활한 마법사
이 마법사를 사용하면 다음과 같은 간단한 단계에 따라 몇 분이면 원본에서 대상으로 데이터를 간단하게 이동할 수 있습니다.

1.	원본을 선택합니다.
2.	대상을 선택합니다.
3.	설정을 구성합니다.

![데이터 원본 선택](./media/data-factory-copy-wizard/select-data-source-page.png)

### 풍부한 데이터 탐색 및 스키마 매핑
마법사 내에서 테이블 및 폴더를 탐색하고, 데이터를 미리 보고, 스키마를 매핑하고, 식의 유효성을 검사하고, 간단한 데이터 변환을 수행할 수 있습니다.

**테이블/폴더 탐색** ![테이블 및 폴더 찾아보기](./media/data-factory-copy-wizard/browse-tables-folders.png)

### 다양한 데이터 및 개체 유형에 따라 규모를 조정할 수 있는 환경
이 환경은 처음부터 빅 데이터를 염두에 두고 설계되었습니다. 수백 개의 폴더, 파일 또는 테이블을 이동하는 Data Factory 파이프라인을 간단하고 효율적으로 만들 수 있습니다.

**데이터 미리 보기** ![파일 형식 설정](./media/data-factory-copy-wizard/file-format-settings.png)

**스키마 매핑** ![스키마 매핑](./media/data-factory-copy-wizard/schema-mapping.png)

**간단한 변환 수행** ![식 유효성 검사](./media/data-factory-copy-wizard/validate-expressions.png)

### 다양한 데이터 및 개체 유형에 따라 규모를 조정할 수 있는 환경
복사 마법사를 사용하여 간단하고 효율적으로 수백 개의 폴더, 파일 또는 테이블을 이동할 수 있습니다.

![데이터를 복사할 테이블 선택](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### 다양한 일정 옵션
복사 작업을 한 번만 또는 일정에 따라(시간별, 일별, 등) 실행할 수 있습니다. 온-프레미스, 클라우드 및 로컬 데스크톱 복사에 걸쳐 광범위한 커넥터에 이 두 옵션을 모두 사용할 수 있습니다.

일 회 복사 작업을 통해 원본에서 대상으로 한 번만 데이터를 이동할 수 있습니다. 이는 모든 크기 및 지원되는 형식의 데이터에 적용됩니다. 예약 복사를 통해 미리 정해진 반복 주기에 따라 데이터를 복사할 수 있습니다. 다양한 설정(예: 재시도, 시간 제한, 경고 등)을 사용하여 예약 복사를 구성할 수 있습니다.

![일정 속성](./media/data-factory-copy-wizard/scheduling-properties.png)

## Azure Blob 폴더 경로의 변수
폴더 경로의 변수를 사용하여 [WindowStart 시스템 변수](data-factory-functions-variables.md#data-factory-system-variables)를 기반으로 런타임 시 결정되는 폴더의 데이터를 복사할 수 있습니다. 지원되는 변수는 **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** 및 **{custom}**입니다. 예: inputfolder/{year}/{month}/{day}.

다음과 같은 형식의 입력 폴더가 있다고 가정하겠습니다.

	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

**파일 또는 폴더**의 **찾아보기** 단추를 클릭하여 이러한 폴더(예: 2016->03->01->02)중 하나를 찾아서 **선택**을 클릭합니다. 텍스트 상자에 **2016/03/01/02**가 표시됩니다. 이제 **2016**을 **{year}**로, **03**을 **{month}**로, **01**을 **{day}**로, **02**를 **{hour}**로 바꾸고 탭을 누릅니다. 이러한 네 가지 변수의 형식을 선택하는 드롭다운 목록이 표시됩니다.

![시스템 변수 사용](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

다음 스크린샷에 표시된 것처럼 **사용자 지정** 변수 및 모든 [지원되는 형식 문자열](https://msdn.microsoft.com/library/8kb3ddd4.aspx)도 사용할 수 있습니다. 해당 구조의 폴더를 선택하려면 먼저 **찾아보기** 단추를 사용합니다. 그런 다음 값을 **{custom}**으로 바꾸고, 탭을 누르면 형식 문자열을 입력할 수 있는 텍스트 상자가 표시됩니다.

![사용자 지정 변수 사용](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## 다음 단계
Data Factory 복사 마법사를 사용하여 복사 작업이 있는 파이프라인을 만드는 방법은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

<!---HONumber=AcomDC_0824_2016-->