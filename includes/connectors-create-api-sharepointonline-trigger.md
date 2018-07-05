이 예제에서는 **SharePoint Online - 새 항목을 만들 때** 트리거를 사용하여 SharePoint Online 목록에 새 항목이 만들어질 때 논리 앱 워크플로를 시작하는 방법을 보여 줍니다.

> [!NOTE]
> SharePoint Online에 대한 *연결*을 아직 만들지 않은 경우 SharePoint 계정에 로그인하라는 메시지가 표시됩니다.  
> 
> 

1. Logic Apps 디자이너에서 검색 상자에 *sharepoint*를 입력한 후 **SharePoint Online - 새 항목을 만들 때** 트리거를 선택합니다.  
   ![SharePoint online 트리거 이미지](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. **새 항목을 만들 때** 컨트롤이 표시됩니다.  
   ![SharePoint online 트리거 이미지 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. **사이트 URL**을 선택합니다. 워크플로를 트리거하기 위해 새 항목에 대해 모니터링할 SharePoint Online 사이트입니다.  
   ![SharePoint online 트리거 이미지 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. **목록 이름**을 선택합니다. 워크플로를 트리거하는 새 항목에 대해 모니터링할 SharePoint Online 사이트의 목록입니다.  
   ![SharePoint online 트리거 이미지 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

이제, 논리 앱은 워크플로의 다른 트리거 및 동작의 실행을 시작하는 트리거로 구성되었습니다. 이 작업은 선택한 SharePoint Online 목록에 새 항목이 만들어질 때마다 수행됩니다.  

