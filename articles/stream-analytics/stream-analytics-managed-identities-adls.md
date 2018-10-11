---
title: 관리 ID를 사용하여 Azure Data Lake Storage Gen1 출력(미리 보기)에 대한 Azure Stream Analytics 작업 인증
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2018
ms.openlocfilehash: 72bf467cc0f2ba195aa4f25228bc9e08605cd4ee
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018597"
---
# <a name="use-managed-identities-to-authenticate-azure-stream-analytics-jobs-to-azure-data-lake-storage-gen1-output-preview"></a>관리 ID를 사용하여 Azure Data Lake Storage Gen1 출력(미리 보기)에 대한 Azure Stream Analytics 작업 인증

Azure Stream Analytics는 ADLS(Azure Data Lake Storage) Gen1 출력을 사용하여 관리 ID 인증을 지원합니다. 관리 ID는 지정된 Stream Analytics 작업을 나타내는 Azure Active Directory에 등록된 관리되는 응용 프로그램이며, 대상 리소스를 인증하는 데 사용될 수 있습니다. 관리 ID는 암호 변경 또는 90일마다 발생하는 사용자 토큰 만료로 인해 다시 인증해야 하는 것과 마찬가지로 사용자 기반 인증 방법의 제한을 제거합니다. 또한 관리 ID는 Azure Data Lake Storage Gen1을 출력되는 Stream Analytics 작업 배포의 자동화에 도움이 됩니다.

이 미리 보기에 등록하고 새 기능에 대해 자세히 알려면 [Azure Stream Analytics의 8가지 새로운 기능](https://azure.microsoft.com/en-us/blog/eight-new-features-in-azure-stream-analytics/) 블로그 게시물을 참조합니다.

이 문서에서는 Azure Data Lake Storage Gen1로 출력되는 Azure Stream Analytics 작업에 대해 관리 ID를 사용하도록 설정하기 위한 두 가지 방법을 보여줍니다. 즉, Azure Portal 및 Azure Resource Manager 템플릿 배포를 통한 방법입니다.

## <a name="enable-managed-identity-with-azure-portal"></a>Azure Portal을 통해 관리 ID를 사용하도록 설정

1. 새 Stream Analytics 작업을 만들거나 Azure Portal에서 기존 작업을 열어 시작합니다. 화면 왼쪽에 있는 메뉴 모음에서 **구성** 아래에 있는 **관리 ID(미리 보기)** 를 선택합니다.

   ![Stream Analytics 관리 ID 미리 보기를 구성](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. 오른쪽에 표시 되는 창에서 **시스템 할당 관리 ID(미리 보기) 사용**을 선택합니다. **저장**을 클릭하여 Azure Active Directory에서 Stream Analytics 작업의 ID에 대한 서비스 주체를 만듭니다. 새로 만든 ID의 수명 주기는 Azure에서 관리합니다. Stream Analytics 작업을 삭제하면 연결된 ID(즉, 서비스 주체)는 Azure에서 자동으로 삭제합니다.

   구성이 저장되는 경우 서비스 주체의 OID(개체 ID)는 아래와 같이 보안 주체 ID로 나열됩니다.

   ![Stream Analytics 보안 주체 ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   서비스 주체에는 Stream Analytics 작업과 동일한 이름이 사용됩니다. 예를 들어 작업 이름이 **MyASAJob**인 경우 만든 서비스 주체 이름도 **MyASAJob**이 됩니다.

3. ADLS Gen1 출력 싱크의 출력 속성 창에서 인증 모드 드롭다운을 클릭하고 **관리 ID(미리 보기)** 를 선택합니다.

4. 나머지 속성을 입력합니다. ADLS 출력을 만드는 방법에 대한 자세한 내용은 [Stream Analytics를 사용하여 Data Lake Store 출력 만들기](../data-lake-store/data-lake-store-stream-analytics.md)를 참조하세요. 작업을 마쳤으면 **저장**을 클릭합니다.

   ![Azure Data Lake Storage 구성](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. ADLS Gen1 개요 페이지로 이동하여 **데이터 탐색기**를 클릭합니다.

   ![Data Lake Storage 개요 구성](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. 데이터 탐색기 창에서 **액세스**를 선택하고 액세스 창에서 **추가**를 클릭합니다.

   ![Data Lake Storage 액세스 구성](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. **사용자 또는 그룹 선택** 창의 텍스트 상자에 서비스 주체의 이름을 입력합니다. 서비스 주체의 이름은 해당 Stream Analytics 작업의 이름이기도 합니다. 주체 이름을 입력하면 해당 이름이 텍스트 상자 아래에 표시됩니다. 원하는 서비스 주체 이름을 선택하고 **선택**을 클릭합니다.

   ![서비스 주체 이름 선택](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. **사용 권한** 창에서 **쓰기** 및 **실행** 권한을 확인하고 **이 폴더 및 모든 하위 폴더**에 할당합니다. 그런 다음, **확인**을 클릭합니다.

   ![사용 권한 선택](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. 서비스 주체는 아래와 같이 **액세스** 창의 **할당된 권한** 아래에 나열됩니다. 이제 다시 돌아가서 Stream Analytics 작업을 시작할 수 있습니다.

   ![액세스 목록](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Data Lake Storage Gen1 파일 시스템 사용 권한에 대한 자세한 내용은 [Azure Data Lake Storage Gen1의 Access Control](../data-lake-store/data-lake-store-access-control.md)을 참조하세요.

## <a name="resource-manager-template-deployment"></a>Resource Manager 템플릿 배포

1. Resource Manager 템플릿의 리소스 섹션에서 다음 속성을 포함하여 관리 ID를 통해 *Microsoft.StreamAnalytics/streamingjobs* 속성을 만들 수 있습니다.

   ```json
   "Identity": {
   "Type": "SystemAssigned",
   },
   ```

   이 속성을 통해 Azure Resource Manager에서 Azure Stream Analytics 작업에 대한 ID를 만들어서 관리합니다.

   **샘플 작업**

   ```json
   { 
   "Name": "AsaJobWithIdentity", 
   "Type": "Microsoft.StreamAnalytics/streamingjobs", 
   "Location": "West US",
   "Identity": {
     "Type": "SystemAssigned", 
     }, 
   "properties": {
      "sku": {
       "name": "standard"
       },
   "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {        
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": “myDataLakeAccountName",
                 "filePathPrefix": “cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
                 }
                 
   }
   ```
  
   **샘플 작업 응답**

   ```json
   { 
   "Name": "mySAJob", 
   "Type": "Microsoft.StreamAnalytics/streamingjobs", 
   "Location": "West US",
   "Identity": {
   "Type": "SystemAssigned",
    "principalId": "GUID", 
    "tenantId": "GUID", 
   }, 
   "properties": {
           "sku": {
             "name": "standard"
           },
   }
   ```

   필요한 ADLS 리소스에 대한 액세스 권한을 부여하려면 작업 응답에서 보안 주체 ID를 기록해 둡니다.

   **테넌트 ID**는 서비스 주체를 만든 Azure Active Directory 테넌트의 ID입니다. 서비스 주체는 구독이 신뢰하는 Azure 테넌트에서 생성됩니다.

   **형식**은 관리 ID 형식에서 설명한 것처럼 관리 ID 형식을 나타냅니다. 시스템 할당 형식만 지원됩니다.

2. PowerShell을 사용하여 서비스 주체에 대한 액세스 권한을 제공합니다. PowerShell을 통해 서비스 주체에 액세스하려면 다음 명령을 실행합니다.

   ```powershell
   Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId**는 서비스 주체의 개체 ID로서 서비스 주체를 만들면 포털 화면에 나열됩니다. Resource Manager 템플릿 배포를 사용하여 작업을 만든 경우 개체 ID는 작업 응답의 ID 속성에 나열됩니다.

   **예제**

   ```powershell
   PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   위의 PowerShell 명령에 대한 자세한 내용은 [Set-AzureRmDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters) 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics를 사용하여 Data Lake Store 만들기](../data-lake-store/data-lake-store-stream-analytics.md)
