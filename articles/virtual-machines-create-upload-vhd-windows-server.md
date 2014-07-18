<properties  linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn how to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Windows Server Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

# Windows Server 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드

Azure의 가상 컴퓨터는 가상 컴퓨터를 만들 때 선택하는 운영 체제를 실행합니다. Azure는 VHD 형식(.vhd 파일)의 가상 하드 디스크에 가상 컴퓨터의 운영 체제를 저장합니다. 중복을 위해 준비된 운영 체제의 VHD를 이미지라고 합니다. 이 문서에서는 사용자가 설치하여 범용화한 운영 체제가 포함된 .vhd 파일을 업로드하여 고유한 이미지를 만드는 방법을 보여 줍니다. Azure의 디스크 및 이미지에 대한 자세한 내용은 [디스크 및 이미지 관리][1]를 참조하십시오.

**참고**: 가상 컴퓨터를 만들 때 응용 프로그램을 원활하게 실행하도록 운영 체제 설정을 사용자 지정할 수 있습니다. 사용자가
설정한 구성은 가상 컴퓨터의 디스크에 저장됩니다. 자세한 내용은 [사용자 지정 가상 컴퓨터를 만드는
방법](/en-us/manage/windows/how-to-guides/custom-create-a-vm/)을 참조하십시오.

## 필수 구성 요소

이 문서에서는 사용자에게 다음과 같은 항목이 있다고 가정합니다.

**관리 인증서** - VHD를 업로드하려는 구독용 관리 인증서를 만들었고 인증서를 .cer 파일을 내보냈습니다. 인증서 만들기에 대한 자세한 내용은 [Azure용 관리 인증서 만들기][2]를 참조하십시오.

**.vhd 파일에 저장된 지원되는 Windows 운영 체제** - 지원되는 Windows Server 운영 체제를 가상 하드 디스크에 설치했습니다. .vhd 파일을 만들 수 있는 도구로는 여러 개가 있습니다. Hyper-V와 같은 가상화 솔루션을 사용하여 .vhd 파일을 만들고 운영 체제를 설치할 수 있습니다. 지침에 대해서는 [Hyper-V 역할 설치 및 가상 시스템 구성][3]을
참조하십시오.

**중요**: 최신 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.

* **Window Server 운영 체제 미디어.** 이 작업을 수행하려면 Windows Server 운영 체제가 포함된 .iso 파일이 필요합니다. 다음 Windows Server 버전이 지원됩니다.
  <table  border="1" width="600">
  <tr  bgcolor="#E9E7E7">
    <th>OS</th>
  
    <th>SKU</th>
  
    <th>서비스 팩</th>
  
    <th>아키텍처</th>
  
  </tr>
  
  <tr>
    <td>Windows Server 2012</td>
  
    <td>모든 버전</td>
  
    <td>해당 없음</td>
  
    <td>x64</td>
  
  </tr>
  
  <tr>
    <td>Windows Server 2008 R2</td>
  
    <td>모든 버전</td>
  
    <td>SP1</td>
  
    <td>x64</td>
  
  </tr>
  
  </table>
  

</P>

* Azure PowerShell 모듈의 일부인 [Add-AzureVHD][4] cmdlet. 모듈을 다운로드하려면 [Azure 다운로드](/en-us/develop/downloads/) 페이지를 참조하십시오.

이 작업에는 다음 단계가 포함됩니다.

* [1단계: 업로드할 이미지 준비](#prepimage)
* [2단계: Azure에서 저장소 계정 만들기](#createstorage)
* [3단계: Azure에 대한 연결 준비](#prepAzure)
* [4단계: .vhd 파일 업로드](#upload)

## <a id="prepimage"> </a>1단계: 업로드할 이미지 준비

Azure에 이미지를 업로드하려면 먼저 Sysprep 명령을 사용하여 범용화해야 합니다. Sysprep 사용에 대한 자세한 내용은 [Sysprep 사용 방법: 소개][5](영문)를 참조하십시오.

방금 만든 가상 컴퓨터에서 다음 절차를 완료하십시오.

1.  운영 체제에 로그인합니다.

2.  관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\\system32\\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.
    
    ![명령 프롬프트 창
    열기](./media/virtual-machines-create-upload-vhd-windows-server/sysprepcommand.png)

3.  **시스템 준비 도구** 대화 상자가 나타납니다.
    
    ![Sysprep
    시작](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다.

5.  **종료 옵션**에서 **종료**를 선택합니다.

6.  **확인**을 클릭합니다.

## <a id="createstorage"> </a>2단계: Azure에서 저장소 계정 만들기

저장소 계정은 저장소 서비스에 액세스하는 네임스페이스의 가장 높은 수준을 나타내며 Azure 구독과 연결됩니다. Azure에. vhd 파일을 업로드하려면 Azure의 저장소 계정이 필요합니다. 이 계정은 가상 컴퓨터를 만드는 데에도 사용할 수 있습니다.
Azure 관리 포털을 사용하여 저장소 계정을 만들 수 있습니다.

1.  Azure 관리 포털에 로그인합니다.

2.  명령 모음에서 **새로 만들기**를 클릭합니다.

3.  **저장소 계정**을 클릭한 후 **빠른 생성**을 클릭합니다.
    
    ![저장소 계정 빠른
    생성](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4.  다음과 같이 필드를 작성합니다.
    
    ![저장소 계정 정보
    입력](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

* **URL**에서 저장소 계정의 URL에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 이름은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URL 내의 호스트 이름이 됩니다.

* 저장소 계정의 위치 또는 선호도 그룹을 선택합니다. 선호도 그룹을 지정하여 동일한 데이터 센터에 저장소와 클라우드 서비스를 함께 배치할 수 있습니다.

* 저장소 계정에 지역에서 복제를 사용할지 여부를 결정합니다. 지역에서 복제는 기본적으로 설정되어 있습니다. 이 옵션은 저장소가 기본 위치에서 처리할 수 없는 중대한 오류가 발생하는 경우 저장소가 대체 위치로 장애 조치(Failover)하도록 별도의 비용 없이 데이터를 대체 위치로 복제합니다. 대체 위치는 자동으로 할당되며 변경할 수 없습니다. 법적 요구 사항 또는 조직 정책에서 클라우드 기반 저장소의 위치에 대한 보다 긴밀한 제어를 요구하는 경우 지역에서 복제를 해제할 수 없습니다. 그러나 나중에 지역에서 복제를 설정하는 경우 기존 데이터를 대체 위치로 복제하는 데 일회성 데이터 전송 요금이 청구됩니다. 지역에서 복제
  기능이 없는 저장소 서비스는 할인 요금으로 제공됩니다.

1.  **저장소 계정 만들기**를 클릭합니다.
    
    이제 계정이 **저장소 계정**에 나타납니다.
    
    ![저장소 계정을 성공적으로
    만들었음](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

## <a id="PrepAzure"> </a>3단계: Azure에 대한 연결 준비

.vhd 파일을 업로드하려면 먼저 컴퓨터 및 Azure의 구독 간에 안전한 연결을 설정해야 합니다.

1.  Azure PowerShell 창을 엽니다.

2.  다음을 입력합니다.
    
    `Get-AzurePublishSettingsFile`
    
    이 명령은 브라우저 창을 열고 Azure 구독용 인증서 및 정보를 포함하는 .publishsettings 파일을 자동으로
    다운로드합니다.

3.  .publishsettings 파일을 저장합니다.

4.  다음을 입력합니다.
    
    `Import-AzurePublishSettingsFile <PathToFile>`
    
    여기서 `<PathToFile>`는 .publishsettings 파일의 전체
    경로입니다.
    
    자세한 내용은 [Azure Cmdlet 시작][6]을 참조하십시오.

## <a id="upload"> </a>4단계: .vhd 파일 업로드

.vhd 파일을 업로드할 때 Blob 저장소 내 어디에나 .vhd 파일을 저장할 수 있습니다. 다음 명령 예에서, **BlobStorageURL**은 2단계에서 만든 저장소 계정의 URL이고, **YourImagesFolder**는 이미지를 저장할 Blob 저장소 내의 컨테이너입니다. **VHDName**은 관리 포털에 나타나는 레이블이며 가상 하드 디스크를 식별합니다. **PathToVHDFile**은 .vhd 파일의 전체 경로 및 이름입니다.

1.  이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력하십시오.
    
	`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`
    
    자세한 정보는 [Add-AzureVhd][4]를 참조하십시오.

## 사용자 지정 이미지 목록에 이미지 추가

.vhd를 업로드한 후 구독과 연결된 사용자 지정 이미지 목록에 이미지로 추가합니다.

1.  관리 포털의 **모든 항목**에서 **가상 컴퓨터**를 클릭합니다.

2.  가상 컴퓨터에서 **이미지**를 클릭한 후 **만들기**를 클릭합니다.

3.  **VHD에서 이미지 만들기**에서 업로드한 이름 및 URL을 지정합니다.

4.  **이 VHD에 연결된 가상 컴퓨터에서 Sysprep를 실행했습니다.**를 선택하여 2단계에서 운영 체제를 범용화했음을 확인한 후 **확인**을 클릭합니다.

## 다음 단계

이미지가 목록에서 사용 가능해지면 이미지를 사용하여 가상 컴퓨터를 만들 수 있습니다. 지침에 대해서는 [Windows Server를 실행하는 가상 컴퓨터 만들기](../virtual-machines-windows-tutorial/)를 참조하십시오.



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj672979.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx
[3]: http://technet.microsoft.com/en-us/library/hh846766.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/dn205185.aspx
[5]: http://technet.microsoft.com/en-us/library/bb457073.aspx
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx