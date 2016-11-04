## 저장소 연결 문자열 설정
Azure Storage Client Library for .NET은 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기 위한 끝점 및 자격 증명을 구성하는 작업을 지원합니다. 저장소 연결 문자열을 유지하는 가장 좋은 방법은 구성 파일 안에 있습니다.

연결 문자열에 대한 자세한 내용은 [Azure 저장소에 연결 문자열 구성](../articles/storage/storage-configure-connection-string.md)을 참조하세요.

> [!NOTE]
> 저장소 계정 키는 저장소 계정의 루트 암호와 비슷합니다. 항상 저장소 계정 키를 보호해야 합니다. 다른 사용자에게 배포하거나 하드 코딩하거나 다른 사람이 액세스할 수 있는 일반 텍스트 파일에 저장하지 마십시오. 손상되었다고 생각되면 Azure 포털을 사용하여 키를 다시 생성합니다.
> 
> 

### 대상 환경 확인
이 가이드의 예제를 실행하기 위해 다음과 같은 두 가지 환경 옵션이 있습니다.

* Azure 저장소 에뮬레이터에 대해 코드를 실행할 수 있습니다. 저장소 에뮬레이터는 클라우드에서 Azure 저장소 계정을 에뮬레이트하는 로컬 환경입니다. 에뮬레이터는 응용 프로그램을 개발하는 동안 코드의 테스트 및 디버깅하기 위한 무료 옵션입니다. 에뮬레이터는 잘 알려진 계정 및 키를 사용합니다. 자세한 내용은 [개발 및 테스트에 Azure 저장소 에뮬레이터 사용](../articles/storage/storage-use-emulator.md)을 참조하세요.
* 클라우드에서 Azure 저장소 계정에 대한 코드를 실행할 수 있습니다. 

클라우드에서 저장소 계정을 대상으로 하는 경우 Azure 포털에서 저장소 계정에 대한 기본 선택키를 복사합니다. 자세한 내용은 [저장소 액세스 키 보기 및 복사](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)를 참조하세요.

> [!NOTE]
> Azure 저장소와 연결하여 비용이 초래되지 않도록 저장소 에뮬레이터를 대상으로 할 수 있습니다. 그러나 클라우드에서 Azure 저장소 계정을 대상으로 하도록 선택하는 경우 이 자습서를 수행하는 비용은 무시됩니다.
> 
> 

### .NET 구성을 사용하여 연결 문자열 구성
응용 프로그램이 데스크톱 또는 모바일 장치나 Azure 가상 컴퓨터 또는 Azure 웹앱에서 실행되는 경우 .NET 구성을 사용하여 연결 문자열을 저장합니다(*예:* 응용 프로그램의 `web.config` 또는 `app.config` 파일에). 다음과 같이 `<appSettings>` 요소를 사용하여 연결 문자열을 저장합니다. 저장소 계정의 이름으로 `account-name`을(를) 바꾸고를 계정 액세스 키로 `account-key`을(를) 바꿉니다.

    <configuration>
          <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
          </appSettings>
    </configuration>

예를 들어, 구성 설정은 다음과 유사합니다.

    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=account-key" />

저장소 에뮬레이터를 대상으로 하려면 잘 알려진 계정 이름 및 키에 매핑되는 바로 가기를 사용할 수 있습니다. 이 경우에 연결 문자열 설정은 다음과 같습니다.

    <add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

### Azure 클라우드 서비스에 대한 연결 문자열 구성
응용 프로그램이 Azure 클라우드 서비스에서 실행되는 경우 Azure 서비스 구성 파일(`*.csdef` 및 `*.cscfg` 파일)을 사용하여 연결 문자열을 저장합니다. Azure 클라우드 서비스 구성에 대한 자세한 내용은 [클라우드 서비스 만들기 및 배포 방법](../articles/cloud-services/cloud-services-how-to-create-deploy.md)을 참조하세요.

<!---HONumber=AcomDC_0406_2016-->