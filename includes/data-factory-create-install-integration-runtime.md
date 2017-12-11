## <a name="create-a-self-hosted-ir"></a>자체 호스팅 IR 만들기

이 섹션에서는 자체 호스팅 통합 런타임을 만들고 이를 Microsoft SQL Server 데이터베이스와 함께 온-프레미스 컴퓨터에 연결합니다. 자체 호스팅된 통합 런타임은 사용자 컴퓨터의 Microsoft SQL Server에서 Azure Blob 저장소로 데이터를 복사하는 구성 요소입니다. 

1. Integration Runtime의 이름에 대한 변수를 만듭니다. 고유한 이름을 사용하고 이름을 기록해 둡니다. 이 자습서의 뒷부분에서 사용합니다. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. 자체 호스팅 통합 런타임을 만듭니다. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   샘플 출력은 다음과 같습니다.

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 다음 명령을 실행하여 만든 통합 런타임의 상태를 검색합니다. **State** 속성의 값이 **NeedRegistration**으로 설정되어 있는지 확인합니다. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   샘플 출력은 다음과 같습니다.

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 다음 명령을 실행하여 **인증 키**를 검색하여 자체 호스팅 통합 런타임을 클라우드의 Data Factory 서비스에 등록합니다. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   샘플 출력은 다음과 같습니다.

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. 다음 단계에서 컴퓨터에 설치할 자체 호스팅된 통합 런타임을 등록하기 위한 키(따옴표 제외) 중 하나를 복사합니다.  

## <a name="install-integration-runtime"></a>Integration Runtime 설치
1. **Microsoft Integration Runtime**이 컴퓨터에 있는 경우 **프로그램 추가/제거**를 사용하여 제거합니다. 
2. 로컬 Windows 컴퓨터에서 자체 호스팅된 Integration Runtime을 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)하고 설치를 실행합니다. 
3. **Microsoft Integration Runtime 설치 마법사 시작**에서 **다음**을 클릭합니다.  
4. **최종 사용자 사용권 계약** 페이지에서 사용권 계약에 동의하고 **다음**을 클릭합니다. 
5. **대상 폴더** 페이지에서 **다음**을 클릭합니다. 
6. **Microsoft Integration Runtime을 설치할 준비가 됨**에서 **설치**를 클릭합니다. 
7. 컴퓨터를 사용하지 않는 동안 절전 또는 최대 절전 모드로 전환되도록 구성하는 방법에 대한 경고 메시지가 표시되면 **확인**을 클릭합니다. 
8. **전원 옵션** 창이 표시되면 그것을 닫은 후 설치 창으로 전환합니다. 
9. **Microsoft Integration Runtime 설치 마법사 완료** 페이지에서 **마침**을 클릭합니다.
10. **Integration Runtime(자체 호스팅) 등록** 페이지에 이전 섹션에서 저장한 키를 붙여넣고 **등록**을 클릭합니다. 

   ![통합 런타임 등록](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)
2. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 메시지가 표시됩니다.

   ![성공적으로 등록되었습니다.](media/data-factory-create-install-integration-runtime/registered-successfully.png)

3. **새로운 Integration Runtime(자체 호스팅) 노드** 페이지에서 **다음**을 클릭합니다. 

    ![새 Integration Runtime 노드 페이지](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)
4. **인트라넷 통신 채널**에서 **건너뛰기**를 클릭합니다. 다중 노드 Integration Runtime 환경에서 노드 내 통신을 보호하기 위한 TLS/SSL 인증을 선택할 수 있습니다. 

    ![인트라넷 통신 채널 페이지](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)
5. **Integration Runtime(자체 호스팅) 등록** 페이지에서 **구성 관리자 시작**을 클릭합니다. 
6. 노드가 클라우드 서비스에 연결되면 다음 페이지가 표시됩니다.

   ![노드가 연결됨](media/data-factory-create-install-integration-runtime/node-is-connected.png)
7. 이제, Microsoft SQL Server 데이터베이스에 대한 연결을 테스트합니다.

    ![진단 탭](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    - **Configuration Manager** 창에서 **진단** 탭으로 전환합니다.
    - **데이터 원본 유형**에 대한 **SqlServer**를 선택합니다.
    - **서버** 이름을 입력합니다.
    - **데이터베이스** 이름을 입력합니다. 
    - **인증** 모드를 선택합니다. 
    - **사용자** 이름을 입력합니다. 
    - 사용자 이름에 대한 **암호**를 입력합니다.
    - 통합 런타임을 Microsoft SQL Server에 연결할 수 있는지 확인하려면 **테스트**를 클릭합니다. 연결이 성공적인 경우 녹색 확인 표시가 나타납니다. 그렇지 않다면 실패와 관련된 오류 메시지가 나타납니다. 모든 문제를 해결하고 통합 런타임을 Microsoft SQL Server에 연결할 수 있는지 확인합니다.    

    > [!NOTE]
    > 이러한 값(인증 형식, 서버, 데이터베이스, 사용자, 암호)을 적어 둡니다. 이 자습서의 뒷부분에서 사용됩니다. 
    
