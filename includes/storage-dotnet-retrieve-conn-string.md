### 연결 문자열 검색
**CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. Azure 프로젝트 템플릿을 사용하고 있거나 Microsoft.WindowsAzure.CloudConfigurationManager 네임스페이스에 대한 참조가 있는 경우 **CloudConfigurationManager** 형식을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조 없이 응용프로그램을 만드는 중이며 위에 표시된 대로 연결 문자열이 `web.config` 또는 `app.config`에 있는 경우 **ConfigurationManager**를 사용하여 연결 문자열을 검색할 수 있습니다. System.Configuration.dll에 대한 참조를 프로젝트에 추가하고 다른 네임스페이스 선언을 추가해야 합니다.

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.AppSettings["StorageConnectionString"]);

<!----HONumber=AcomDC_0309_2016-->