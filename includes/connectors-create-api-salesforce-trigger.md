이 연습에서는 **Salesforce - 개체를 만들 때** 트리거를 사용하여 Salesforce에서 새 잠재 고객을 만들 때 논리 앱 워크플로를 시작하는 방법을 배웁니다.

> [!NOTE]
> Salesforce에 대한 *연결*을 아직 만들지 않은 경우 Salesforce 계정에 로그인하라는 메시지가 표시됩니다.  
> 
> 

1. Logic Apps 디자이너에서 검색 상자에 *salesforce*를 입력한 후 **Salesforce - 개체를 만들 때** 트리거를 선택합니다.  
   ![Salesforce 트리거 이미지 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. **개체를 만들 때** 컨트롤이 표시됩니다.  
   ![Salesforce 트리거 이미지 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. **개체 형식** 을 선택하고 개체 목록에서 *Lead* 를 선택합니다. 이 단계에서는 Salesforce에서 새 잠재 고객을 만들 때마다 논리 앱에 알리는 트리거를 만들고 있음을 지정합니다.   
   ![Salesforce 트리거 이미지 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. 이것으로 끝입니다. 트리거가 만들어졌습니다. 그러나 이 유효한 논리 앱을 만들기 위해서는 하나 이상의 동작을 만들어야 합니다.    
   ![Salesforce 트리거 이미지 4](./media/connectors-create-api-salesforce/trigger-4.png)   

이제, 논리 앱은 Salesforce에서 새 항목이 만들어질 때 워크플로의 다른 트리거 및 동작의 실행을 시작하는 트리거로 구성되었습니다.  

