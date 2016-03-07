<properties 
	pageTitle="DevTest Lab VM에 대한 사용자 지정 아티팩트 만들기 | Microsoft Azure"
	description="DevTest 랩과 함께 사용할 사용자 고유의 아티팩트를 저작하는 방법 알아보기"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="tarcher"/>

#DevTest Lab VM에 대한 사용자 지정 아티팩트 만들기

> [AZURE.NOTE] 이 문서와 함께 제공되는 비디오를 보려면 다음 링크를 클릭하세요. [사용자 지정 아티팩트를 작성하는 방법](/documentation/videos/how-to-author-custom-artifacts)

## 개요
VM이 프로비전된 후 응용 프로그램을 배포하고 구성하기 위해 **아티팩트**가 사용됩니다. 아티팩트는 Git 리포지토리의 폴더에 저장되어 있는 아티팩트 정의 파일 및 다른 스크립트 파일로 구성됩니다. 아티팩트 정의 파일은 VM에 설치하려는 아티팩트를 지정하는 데 사용할 수 있는 JSON과 식으로 구성됩니다. 예를 들어 아티팩트의 이름, 실행할 명령, 명령이 실행되는 경우 사용할 수 있는 매개 변수를 정의할 수 있습니다. 이름으로 아티팩트 정의 파일 내의 다른 스크립트 파일을 참조할 수 있습니다.

##아티팩트 정의 파일 형식
다음 예제에서는 정의 파일의 기본 구조를 구성하는 섹션을 보여 줍니다.

	{
	  "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
	  "title": "",
	  "description": "",
	  "iconUri": "",
	  "targetOsType": "",
	  "parameters": {
	    "<parameterName>": {
	      "type": "",
	      "displayName": "",
	      "description": ""
	    }
	  },
	  "runCommand": {
	    "commandToExecute": ""
	  }
	}

| 요소 이름 | Required? | 설명
| ------------ | --------- | -----------
| $schema | 아니요 | 정의 파일의 유효성을 테스트하는 데 도움이 되는 JSON 스키마 파일의 위치입니다.
| title | 예 | 랩에 표시되는 아티팩트의 이름입니다.
| description | 예 | 랩에 표시되는 아티팩트에 대한 설명입니다.
| iconUri | 아니요 | 랩에 표시되는 아이콘의 URI입니다.
| targetOsType | 예 | 아티팩트를 설치할 VM의 운영 체제입니다. 지원되는 옵션은 Windows 및 Linux입니다.
| 매개 변수 | 아니요 | 아티팩트 설치 명령이 컴퓨터에서 실행될 때 제공되는 값으로 아티팩트를 사용자 지정하는 데 도움이 됩니다.
| runCommand | 예 | VM에서 실행되는 아티팩트 설치 명령입니다.

###아티팩트 매개 변수

정의 파일의 매개 변수 섹션에서 아티팩트를 설치할 때 사용자가 입력할 수 있는 값을 지정합니다. 아티팩트 설치 명령에서 다음 값을 참조할 수 있습니다.

다음과 같은 구조를 사용하여 매개 변수를 정의합니다.

	"parameters": {
	    "<parameterName>": {
	      "type": "<type-of-parameter-value>",
	      "displayName": "<display-name-of-parameter>",
	      "description": "<description-of-parameter>"
	    }
	  }

| 요소 이름 | Required? | 설명
| ------------ | --------- | -----------
| type | 예 | 매개 변수 값의 형식입니다. 허용되는 형식은 아래 목록을 참조하세요.
| displayName 예 | 랩에서 사용자에게 표시되는 매개 변수의 이름입니다.
| description | 예 | 랩에 표시되는 매개 변수의 설명입니다.

허용 유형은 다음과 같습니다.

- 문자열 - 유효한 모든 JSON 문자열
- int - 유효한 모든 JSON 정수
- bool - 유효한 모든 JSON 부울
- array - 유효한 모든 JSON 배열

##아티팩트 식 및 함수

식과 함수를 사용하여 아티팩트 설치 명령을 구성할 수 있습니다. 식은 대괄호([ 및 ]) 안에 들어 있고 아티팩트를 설치할 때 평가됩니다. 식은 JSON 문자열 값에서 어느 위치에나 나타날 수 있으며 항상 다른 JSON 값을 반환합니다. 대괄호([)로 시작하는 리터럴 문자열을 사용해야 하는 경우 대괄호를 두 개([[) 사용해야 합니다. 일반적으로 함수가 포함된 식을 사용하여 값을 구성합니다. JavaScript에서와 마찬가지로 함수 호출은 functionName(arg1,arg2,arg3)으로 형식이 지정됩니다.

다음 목록에는 일반 함수가 나와 있습니다.

- 매개 변수(parameterName) - 아티팩트 명령이 실행될 때 제공되는 매개 변수 값을 반환합니다.
- concat(arg1,arg2,arg3, …..) - 여러 문자열 값을 결합합니다. 이 함수는 임의의 수의 인수를 사용할 수 있습니다.

다음 예제에서는 값을 구성하기 위해 식과 함수를 사용하는 방법을 보여 줍니다.

	runCommand": {
	     "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
	, ' -RawPackagesList ', parameters('packages')
	, ' -Username ', parameters('installUsername')
	, ' -Password ', parameters('installPassword'))]"
	}

##사용자 지정 아티팩트 만들기

아래 단계를 수행하여 사용자 지정 아티팩트를 만듭니다.

1. JSON 편집기 설치 - 아티팩트 정의 파일 작업을 수행하려면 JSON 편집기가 필요합니다. Windows, Linux 및 OS X에 사용할 수 있는 [Visual Studio Code](https://code.visualstudio.com/)를 사용하는 것이 좋습니다.

1. 샘플 artifactfile.json 가져오기 - 다양한 아티팩트 라이브러리를 만들어 놓은 [GitHub 리포지토리](https://github.com/Azure/azure-devtestlab)에서 Azure DevTest Lab 팀이 만든 아티팩트를 확인합니다. 이러한 아티팩트는 사용자 고유 아티팩트를 만드는 데 도움이 됩니다. 아티팩트 정의 파일을 다운로드하고 변경하여 사용자 고유 아티팩트를 만들 수 있습니다.

1. IntelliSense 사용 - 아티팩트 정의 파일을 구성하는 데 사용할 수 있는 유효한 요소를 보려면 IntelliSense를 사용합니다. 또한 요소 값에 대한 다양한 옵션을 볼 수 있습니다. 예를 들어 IntelliSense에 **targetOsType** 요소를 편집할 때 두 가지 선택 항목인 Windows 또는 Linux가 표시됩니다.

1. Git 리포지토리에 아티팩트 저장
	1. 디렉터리 이름이 아티팩트 이름과 동일한 경우 각 아티팩트에 대해 별도의 디렉터리를 만듭니다.
	1. 만든 디렉터리에 아티팩트 정의 파일(artifactfile.json)을 저장합니다.
	1. 아티팩트 설치 명령에서 참조된 스크립트를 저장합니다.

	다음은 아티팩트 폴더가 표시되는 모습의 예입니다.

	![아티팩트 Git 리포지토리 예](./media/devtest-lab-artifact-author/git-repo.png)

1. 랩에 아티팩트 리포지토리 추가 - [DevTest Lab에 Git 아티팩트 리포지토리 추가](devtest-lab-add-artifact-repo.md) 문서를 참조하세요.

## 다음 단계

- [DevTest Lab에 Git 아티팩트 리포지토리 추가](devtest-lab-add-artifact-repo.md) 방법에 대해 알아봅니다.

<!---HONumber=AcomDC_0224_2016-->