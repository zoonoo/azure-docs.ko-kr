트리거를 추가해 보겠습니다.

1. Logic Apps 디자이너의 검색 상자에 *sftp*를 입력한 후 **SFTP - 파일을 추가하거나 수정할 때** 트리거를 선택합니다.   
   ![SFTP 트리거 이미지 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. **파일을 추가하거나 수정할 때** 컨트롤이 열립니다.  
   ![SFTP 트리거 이미지 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. 컨트롤의 오른쪽에 있는 **...** 를 선택합니다. 이렇게 하면 폴더 선택 컨트롤이 열립니다.  
   ![SFTP 트리거 이미지 3](./media/connectors-create-api-sftp/action-1.png)  
4. **SFTP** 를 선택하여 새롭거나 수정된 파일을 모니터링할 폴더로 루트 폴더를 선택합니다. 이제 루트 폴더는 **폴더** 컨트롤에 표시됩니다.  
   ![SFTP 트리거 이미지 4](./media/connectors-create-api-sftp/action-2.png)   

이제, 논리 앱은 파일이 특정 SFTP 폴더에서 수정되거나 만들어질 때 워크플로의 다른 트리거 및 동작의 실행을 시작하는 트리거로 구성되었습니다. 

> [!NOTE]
> 논리 앱이 작동하려면 하나 이상의 트리거와 동작이 있어야 합니다. 다음 섹션의 단계에 따라 동작을 추가합니다.  
> 
> 

