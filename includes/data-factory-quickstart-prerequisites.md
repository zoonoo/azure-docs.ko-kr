## <a name="prerequisites"></a>필수 조건

### <a name="azure-subscription"></a>Azure 구독
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

### <a name="azure-roles"></a>Azure 역할
Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다. Azure Portal에서 오른쪽 위 모서리에 있는 **사용자 이름**을 클릭한 다음 **권한**을 선택하면 구독에 따른 사용자 권한을 볼 수입니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다. 사용자를 역할에 추가하는 방법에 대한 샘플 지침은 [역할 추가](../articles/billing/billing-add-change-azure-subscription-administrator.md) 문서를 참조하세요.

### <a name="azure-storage-account"></a>Azure Storage 계정
이 빠른 시작에서는 범용 Azure Storage 계정(특히 Blob Storage)을 **원본** 및 **대상** 데이터 저장소로 사용합니다. 범용 Azure Storage 계정이 없는 경우 [저장소 계정 만들기](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)를 참조하여 새로 만듭니다. 

#### <a name="get-storage-account-name-and-account-key"></a>저장소 계정 이름 및 계정 키 가져오기
이 빠른 시작에서 Azure Storage 계정 이름 및 키를 사용합니다. 다음 프로시저에서는 저장소 계정 이름 및 키를 가져오는 단계를 제공합니다. 

1. 웹 브라우저를 시작하고 [Azure Portal](https://portal.azure.com)로 이동합니다. Azure 사용자 이름과 암호를 사용하여 로그인합니다. 
2. 왼쪽 메뉴에서 **더 많은 서비스>**를 클릭하고 **저장소** 키워드를 사용하여 필터링하고 **저장소 계정**을 선택합니다.

    ![저장소 계정 검색](media/data-factory-quickstart-prerequisites/search-storage-account.png)
3. 저장소 계정 목록에서 저장소 계정(필요한 경우)을 필터링한 다음 **저장소 계정**을 선택합니다. 
4. **저장소 계정** 페이지의 메뉴에서 **액세스 키**를 선택합니다.

    ![저장소 계정 이름 및 키 가져오기](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. **저장소 계정 이름** 및 **key1** 필드의 값을 클립보드에 복사합니다. 메모장이나 다른 편집기에 붙여넣고 저장합니다. 이 빠른 시작의 뒷부분에서 사용됩니다.   

#### <a name="create-input-folder-and-files"></a>입력 폴더 및 파일 만들기
이 섹션에서는 Azure Blob Storage에 **adftutorial**이라는 Blob 컨테이너를 만듭니다. 그런 다음 컨테이너에 **입력**이라는 폴더를 만든 다음 입력 폴더에 샘플 파일을 업로드합니다. 

1. **저장소 계정** 페이지에서 **개요**로 전환한 다음, **Blobs**을 클릭합니다. 

    ![Blob 옵션 선택](media/data-factory-quickstart-prerequisites/select-blobs.png)
2. **Blob service** 페이지의 도구 모음에서 **+ 컨테이너**를 클릭합니다. 

    ![컨테이너 단추 추가](media/data-factory-quickstart-prerequisites/add-container-button.png)    
3. **새 컨테이너** 대화 상자에서 **adftutorial**을 이름으로 입력하고 **확인**을 클릭합니다. 

    ![컨테이너 이름 입력](media/data-factory-quickstart-prerequisites/new-container-dialog.png)
4. 컨테이너 목록에서 **adftutorial**을 클릭합니다. 

    ![컨테이너를 선택합니다.](media/data-factory-quickstart-prerequisites/seelct-adftutorial-container.png)
1. **컨테이너** 페이지의 도구 모음에서 **업로드**를 클릭합니다.  

    ![업로드 단추](media/data-factory-quickstart-prerequisites/upload-toolbar-button.png)
6. **BLOB 업로드** 페이지에서 **고급**을 클릭합니다.

    ![고급 링크 클릭합니다.](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
7. **메모장**을 시작하고 다음과 같은 내용으로 **emp.txt**이라는 파일을 만듭니다: 그것을 **c:\ADFv2QuickStartPSH** 폴더에 저장합니다: **ADFv2QuickStartPSH** 폴더가 아직 없는 경우 새로 만듭니다.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Azure Portal의 **BLOB 업로드** 페이지에서 **파일** 필드에 대해 **emp.txt** 파일을 탐색 및 선택합니다. 
9. **입력**을 **폴더로 업로드** 필드 값으로 입력합니다. 

    ![BLOB 업로드 설정](media/data-factory-quickstart-prerequisites/upload-blob-settings.png)    
10. 폴더는 **입력**이고 파일은 **emp.txt**인지 확인한 후 **업로드**를 클릭합니다.
11. 목록에서 **emp.txt** 파일 및 업로드 상태를 참조하세요. 
12. 모서리에 있는 **X**를 클릭하여 **BLOB 업로드** 페이지를 닫습니다. 

    ![BLOB 업로드 닫기](media/data-factory-quickstart-prerequisites/close-upload-blob.png)
1. **컨테이너** 페이지를 열린 상태로 유지합니다. 이 빠른 시작의 끝부분에서 출력을 확인하는 데 사용합니다.