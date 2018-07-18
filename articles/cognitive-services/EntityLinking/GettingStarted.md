---
title: Entity Linking API 시작 | Microsoft Docs
description: Cognition Services의 Entity Linking API를 사용하여 텍스트를 분석하고, 명명된 엔터티를 기술 자료의 관련 항목에 연결합니다.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 54c4a3bbb3637c248bd7705ed291633368b542c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373255"
---
# <a name="get-started-with-entity-linking-api-in-c35"></a>C#에서 Entity Linking API 시작

Microsoft의 Entity Linking은 텍스트를 분석하고 명명된 엔터티를 기술 자료의 관련 항목에 연결하기 위한 자연어 처리 도구입니다. 

이 자습서에서는 Entity Linking 클라이언트 라이브러리를 NuGet 패키지로 사용하여 엔터티 연결을 탐색합니다. 

### <a name="Prerequisites">필수 구성 요소</a>

- Visual Studio 2015
- Microsoft Cognitive Services API 키
- 클라이언트 라이브러리 및 예제 가져오기
- Microsoft Entity Linking NuGet 패키지

[SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows)를 통해 Entity Linking Intelligence Service API 클라이언트 라이브러리를 다운로드할 수 있습니다. 다운로드한 zip 파일은 사용자가 선택한 폴더에 추출되어야 하며, 많은 사용자가 Visual Studio 2015 폴더를 선택합니다.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">1단계: Entity Linking Intelligence Service 구독 및 키 가져오기</a>
Entity Linking Intelligence Service를 사용하려면 API 키에 등록해야 합니다. [구독](https://www.microsoft.com/cognitive-services/en-us/sign-up)을 참조하세요. 기본 키와 보조 키 모두 이 자습서에서 사용할 수 있습니다.

### <a name="step-2-create-a-new-project-in-visual-studio">2단계: Visual Studio에서 새 프로젝트 만들기</a>

Visual Studio에서 새 프로젝트를 만들어 보겠습니다. 먼저, 시작 메뉴에서 Visual Studio 2015를 시작합니다. 그런 다음, 프로젝트 템플릿에 대한 **설치 → 템플릿 → Visual C# → Windows 유니버설 → 빈 앱**을 선택하여 새 프로젝트를 만듭니다.

 ![유니버설 앱 만들기](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">3단계: 프로젝트에 Entity Linking NuGet 패키지 추가</a>

Cognitive Services의 Entity Linking은 NuGet.org 패키지로 릴리스되며 설치한 후 사용할 수 있습니다.
패키지에 추가하려면 **솔루션 탐색기** 탭으로 이동하고, 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 선택합니다.

먼저 **NuGet 패키지 관리자** 창에서 오른쪽 위 모서리에 있는 NuGet.org를 **패키지 원본**으로 선택합니다. 왼쪽 위 모서리에서 **찾아보기**를 선택하고 검색 상자에 “ProjectOxford.EntityLinking”을 입력합니다. **Microsoft.ProjectOxford.EntityLinking** NuGet 패키지를 선택하고 **설치**를 클릭합니다.

다음으로, Newtonsoft.Json을 검색하고 설치합니다. 변경 내용을 검토하라는 메시지가 표시되면 **확인**을 클릭합니다. EntityLinking 사용 조건이 표시되면 **동의**를 클릭합니다.

이제 Entity Linking이 응용 프로그램의 일부로 설치되었습니다. 솔루션 탐색기에서 프로젝트의 일부로 **Microsoft.ProjectOxford.EntityLinking** 참조가 있으면 확인할 수 있습니다.

 ![프로젝트에 포함된 nuget 라이브러리](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">4단계: 앱의 XAML에 입력 및 출력 텍스트 블록 추가</a>
**솔루션 탐색기**에서 ** MainPage.xaml **로 이동한 다음, 파일을 두 번 클릭하면 새 창에서 열립니다. 편의를 위해 **디자이너** 탭에서 **XAML** 단추를 두 번 클릭할 수 있습니다. 그러면 **비주얼 디자이너**가 숨겨지고 코드 보기에 대한 모든 공간이 예약됩니다.

 ![프로젝트에 포함된 nuget 라이브러리](./Images/UWPMainPage.png)
 
텍스트 서비스로서 기능을 시각화하는 가장 좋은 방법은 입력 및 출력 텍스트 블록을 만드는 것입니다. 이를 수행하려면 다음 XAML을 **그리드**에 추가합니다. 이 코드는 입력 텍스트 상자, 출력 텍스트 블록 및 시작 단추의 세 가지 구성 요소를 추가합니다.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">5단계: Entity Linking Intelligence Service API 추가 진행</a>
 
이제 사용자 인터페이스가 만들어집니다. Entity Linking Service를 사용하려면 단추 클릭 처리기를 추가해야 합니다. **솔루션 탐색기**에서 **MainPage.xaml**을 엽니다. 단추의 끝에서 button_Click 처리기를 추가합니다.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
단추 클릭 처리기는 코드에서 구현해야 합니다. **솔루션 탐색기**에서 **MainPage.xaml.cs**를 열어 단추 클릭을 구현합니다. EntityLinkingServiceClient는 Entity Linking 응답을 검색하는 래퍼입니다. EntityLinkingServiceClient의 생성자 인수는 Cognitive Services 구독 키입니다. **1단계**에서 가져온 구독 키를 붙여넣어 Entity Linking Service를 호출합니다. 

다음은 Entity Linking Service를 사용하여 응답에 “wikipediaId”를 추가하는 예제 코드입니다. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
이제 프로그램 첫 번째 자연어 처리 Entity Linking App을 실행할 준비가 되었습니다. **F5 키**를 눌러 응용 프로그램을 컴파일하고 시작합니다. 텍스트 코드 조각 또는 단락을 입력 상자에 붙여넣습니다. “결과 가져오기” 단추를 누르고 출력 블록에서 식별된 엔터티를 관찰합니다.
 
 ![UWP 샘플 결과](./Images/DemoCodeResult.png)
 
### <a name="summary">요약</a>
 
이 자습서에서는 C# 및 XAML 코드의 몇 줄만으로 Entity Linking Intelligence Service 클라이언트 라이브러리를 활용하는 응용 프로그램을 만드는 방법을 배웠습니다. 

