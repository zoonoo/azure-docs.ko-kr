<properties  linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="Add a VM to virtual network" pageTitle="Add a virtual machine to a virtual network - Azure" metaKeywords="" description="A tutorial that teaches you how to create a storage account and virtual machine (VM) that you add to an Azure virtual network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Add a Virtual Machine to a Virtual Network" authors="" solutions="" manager="" editor="" />

<h1>가상 네트워크에 가상 컴퓨터 추가</h1>


<!--SOMEWHERE IN THIS TUTORIAL I NEED TO XREF TO THE OTHER VMACHINE TUTORIAL -->

이 자습서에서는 [가상 네트워크][1]에 추가하는 Azure 저장소 계정 및 VM(가상 컴퓨터)을 만드는 단계를 안내합니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다.

 
<div  class="dev-callout"> 
<b>중요</b>

<p>새 Active Directory 포리스트를 설치하기 위해 VM을 만드는 경우 <a href="../active-directory-forest/">Azure의 새 Active Directory 포리스트 설치</a>의 지침을 따르십시오.</p>
</div>

## 목표

이 자습서에서는 다음에 대해 알아봅니다.

* [저장소 계정을 만드는 방법](#CreateStorageAcct)

* [가상 컴퓨터를 만들어 가상 네트워크에 배포하는 방법](#CreateVM)

## 필수 구성 요소

* 다음 자습서 중 하나를 완료합니다.
  
  * [Azure에서 가상 네트워크
    만들기](/ko-kr/manage/services/networking/create-a-virtual-network/)
    
    또는
  
  * [프레미스 간 연결을 위해 가상 네트워크
    만들기](/ko-kr/manage/services/networking/cross-premises-connectivity/)

* 진행 중인 유효한 구독이 하나 이상 있는 Windows Live 계정

* [Azure에서 가상 네트워크 만들기](/ko-kr/manage/services/networking/create-a-virtual-network/) 또는
  [프레미스 간 연결을 위해 가상 네트워크 만들기](/ko-kr/manage/services/networking/cross-premises-connectivity/)에서   지정한 다음 항목의 이름
  
  * 가상 네트워크에 할당한 선호도 그룹
  
  * 가상 네트워크의 이름
  
  * 서브넷의 이름

## <a name="CreateStorageAcct">저장소 계정 만들기</a>

1.  [Azure 관리 포털][2]에서 가상 네트워크를 만든 후 화면 아래의 왼쪽 모서리에서 **새로 만들기**를 클릭합니다.
    
    ![NewStorAcct](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.  탐색 창에서 **데이터 서비스**, **저장소**, **빠른 생성**을 차례로 클릭합니다.
    
    ![QuickCreate](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.  다음 정보를 입력한 후 화면 아래의 오른쪽에 있는 확인 표시를 클릭합니다.

* **URL:** *yourstorage*를 입력합니다.

* **지역/선호도 그룹:** 드롭다운 목록에서 **YourAffinityGroup**을 선택합니다.

* **지역에서 복제 사용:** 이 상자를 선택하지 않은 상태로 둡니다.
  
  ![CreateNewAcct](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

1.  프로세스가 완료되면 **저장소** 페이지의 **상태** 열에 **온라인**이 표시됩니다.
    
    ![NewStorAcctCreated](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)

## <a name="CreateVM">가상 컴퓨터 만들기 및 가상 네트워크에 배포</a>

**가상 컴퓨터를 만들고 가상 네트워크에 배포하려면**

1.  저장소 계정을 만든 후 화면 아래의 왼쪽 모서리에서 **새로 만들기**를 클릭합니다.
    
    ![NewVM](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)

2.  탐색 창에서 **계산**, **가상 컴퓨터**, **갤러리에서**를 차례로 클릭합니다.
    
    ![FromGallery](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)

3.  **VM OS 선택** 화면에서 **Windows Server 2008 R2 SP1, October 2012**(또는 사용
    가능한 최신 버전)를 선택한 후 다음 화살표를 클릭합니다.
    
    ![VMOS](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)

4.  **가상 컴퓨터 구성** 화면에서 다음 정보를 입력한 후 다음 화살표를 클릭합니다. <!-- SHOULD WE TELL
    USERS TO WRITE DOWN USER NAME AND PASS?? -->
    
    **팁:** 이는 새 가상 컴퓨터에 로그인하는 데 사용할 자격 증명이므로 사용자 이름 및 암호를 기록해 두십시오.

* **가상 컴퓨터 이름:** *YourVMachine*을 입력합니다.

* **새 사용자 이름:** 읽기 전용입니다.

* **새 암호:** 강력한 암호를 입력합니다.

* **암호 확인:** 암호를 다시 입력합니다.

* **크기:** **작게**를 선택합니다.
  
  ![VMConfig](./media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

1.  **가상 컴퓨터 모드** 화면에서 다음 정보를 입력한 후 다음 화살표를 클릭합니다.

* **독립 실행형 가상 컴퓨터:** 이 옵션을 선택한 상태로 둡니다.

* **DNS 이름:** *yourcloudapplication*을 입력합니다.

* **저장소 계정:** **yourstorage**를 선택합니다.

* **지역/선호도 그룹/가상 네트워크:** 드롭다운 목록에서 **YourVirtualNetwork**를 선택합니다.
  
  ![VMMode](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

1.  **가상 컴퓨터 옵션** 화면에서 다음 정보를 입력한 후 확인 표시 단추를 클릭합니다. 이제 가상 컴퓨터가 만들어집니다.
    새 컴퓨터를 만드는 데 최대 10분이 걸릴 수 있습니다.
    <!-- CONFIRM HOW LONG IT CAN TAKE ON AVG FOR VMACHINE TO BE CREATED -->

* **가용성 집합:** **없음**을 선택합니다.

* **가상 네트워크 서브넷:** **FrontEndSubnet**을 선택합니다.
  
  <div  class="dev-callout" markdown="1">
  <B>참고</B>
  <P>서브넷을 하나 이상 선택하고 게이트웨이 서브넷은 선택하지 않아야 합니다.</P>
  </div>
  
  ![VMOptions](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

1.  가상 컴퓨터가 만들어지면 가상 컴퓨터 화면에서 **상태**는 **실행 중**이 됩니다.
    
    ![VMInstances](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)

2.  탐색 창에서 **모든 항목**을 클릭합니다. 사용자가 만든 모든 개체가 현재 상태와 함께 표시됩니다.
    
    ![AllTab](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

## 다음 단계

방금 만든 VM에 온-프레미스 Active Directory 도메인에 사용할 추가 도메인 컨트롤러를 설치하려면 [Azure 가상 네트워크에서 복제 Active Directory 도메인 컨트롤러 설치](/ko-kr/manage/services/networking/replica-domain-controller/)를 참조하십시오.

## 참고 항목

* [Azure 가상 네트워크][1]

* [네트워크 구성 파일을 사용하여 가상 네트워크 구성][3]

<!-- LINKS -->



[1]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156007.aspx
[2]: http://manage.windowsazure.com/
[3]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156097.aspx