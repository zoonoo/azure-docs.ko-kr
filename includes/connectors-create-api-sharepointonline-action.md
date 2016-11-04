트리거를 추가했으므로 트리거에 의해 생성되는 데이터로 흥미로운 작업을 진행해볼 수 있습니다. 다음 단계에 따라 **SharePoint Online - 파일 만들기** 동작을 추가합니다. 이 동작은 새 항목 트리거가 실행될 때마다 SharePoint Online에서 파일을 만듭니다.

이 동작을 구성하려면 다음 정보를 제공해야 합니다. 이 정보를 제공하면 트리거에 의해 생성된 데이터를 새 파일의 일부 속성에 대한 입력으로 쉽게 사용할 수 있습니다.

| 파일 속성 만들기 | 설명 |
| --- | --- |
| 사이트 URL |새 파일을 만들려는 SharePoint Online 사이트의 URL입니다. 제공된 목록에서 사이트를 선택합니다. |
| 폴더 경로 |새 파일을 배치할 폴더(이전 단계에서 선택한 사이트 URL에 있는)입니다. 폴더를 찾아 선택합니다. |
| 파일 이름 |만들어지는 파일의 이름입니다. |
| 파일 콘텐츠 |파일에 쓸 콘텐츠입니다. |

1. **+ 새 단계**를 선택하여 동작을 추가합니다.  
   ![SharePoint Online 동작 이미지 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. **동작 추가** 링크를 선택합니다. 수행할 동작을 검색할 수 있는 검색 상자가 열립니다. 이 예제에서는 SharePoint 동작을 사용합니다.  
   ![SharePoint Online 동작 이미지 2](./media/connectors-create-api-sharepointonline/action-2.png)  
3. *sharepoint*를 입력하여 SharePoint와 관련된 동작을 검색합니다.
4. 수행할 동작으로 **SharePoint Online - 파일 만들기**를 선택합니다. **참고**: 이전에 SharePoint Online에 대한 연결을 만들지 않았으면 논리 앱에 SharePoint 계정에 액세스하기 위한 권한을 부여하라는 메시지가 표시됩니다.  
   ![SharePoint Online 동작 이미지 3](./media/connectors-create-api-sharepointonline/action-3.png)  
5. **파일 만들기** 컨트롤이 열립니다.  
   ![SharePoint Online 동작 이미지 4](./media/connectors-create-api-sharepointonline/action-4.png)  
6. **사이트 URL**을 선택하고 파일을 만들려는 사이트를 찾은 후 해당 사이트로 이동합니다.  
   ![SharePoint Online 동작 이미지 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. **폴더 경로**를 선택하고 새 파일을 저장할 폴더를 찾은 후 해당 폴더로 이동합니다.  
   ![SharePoint Online 동작 이미지 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. **파일 이름** 컨트롤을 선택하고 만들려는 파일의 이름을 입력합니다. 여기서 파일 이름을 직접 입력하거나 이전에 만든 트리거의 속성 중 하나를 사용할 수 있습니다. **새 항목이 만들어질 때의 출력** 목록에서 속성을 선택하여 이 작업을 수행합니다. 이 목록은 **파일 이름** 컨트롤을 선택한 후에만 표시됩니다. 이 연습에서는 ID(새 목록 항목의 ID)를 **SharePoint Online - 파일 만들기** 동작에 의해 만들어지는 파일의 이름으로 선택했습니다.  
   ![SharePoint Online 동작 이미지 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. **파일 콘텐츠** 컨트롤을 선택하고 만들 파일에 기록할 콘텐츠를 입력합니다. 파일 콘텐츠를 입력할 때 이전에 만든 트리거의 모든 속성을 사용할 수 있습니다. 제공된 목록에서 속성을 선택하기만 하면 됩니다. 또는 컨트롤에 직접 **파일 콘텐츠** 텍스트를 입력할 수도 있습니다. 이 예제에서는 일부 속성을 선택하고 각 속성 사이에 공백 및 하이픈을 추가했습니다.  
   ![SharePoint Online 동작 이미지 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. 워크플로에 대한 변경 내용을 저장합니다.  
11. 축하합니다. 이제 SharePoint Online 목록에 새 항목이 추가될 때 트리거되는 전 기능 논리 앱이 구현되었습니다. 앱에서 새 목록 항목의 속성 중 일부를 사용하여 파일을 만듭니다. 이제 SharePoint 목록에 새 항목을 만들어 테스트할 수 있습니다. 

<!---HONumber=AcomDC_0727_2016-->
