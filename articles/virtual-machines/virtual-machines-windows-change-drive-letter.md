<properties
	pageTitle="VM의 D 드라이브를 데이터 디스크로 만들기 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 만든 Windows VM의 D: 드라이브를 데이터 드라이브로 사용할 수 있도록 드라이브 문자를 변경하는 방법을 설명합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="cynthn"/>

# D 드라이브를 Windows VM의 데이터 드라이브로 사용 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


D 드라이브를 사용하여 데이터를 저장해야 하는 경우 다음 지침에 따라 임시 디스크에 다른 드라이브를 사용할 수 있습니다. 보관해야 하는 데이터를 저장하는 데 임시 디스크를 사용하지 마세요.

## 데이터 디스크 연결

우선 가상 컴퓨터에 데이터 디스크를 연결해야 합니다. 새 디스크를 연결하려면 [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법][Attach]을 참조하세요.

기존 데이터 디스크를 사용하려면 VHD를 저장소 계정으로 업로드해야 합니다. 지침은 [Windows Server VHD를 만들어 Azure에 업로드][VHD]의 3, 4단계를 참조하세요.


## pagefile.sys를 C 드라이브로 임시 이동

1. 가상 컴퓨터에 연결합니다. 

2. **시작** 메뉴를 마우스 오른쪽 단추로 클릭하고 **시스템**을 선택합니다.

3. 왼쪽 메뉴에서 **고급 시스템 설정**을 선택합니다.

4. **성능** 섹션에서 **설정**을 선택합니다.

5. **고급** 탭을 선택합니다.

5. **가상 메모리** 섹션에서 **변경**을 선택합니다.

6. **C** 드라이브를 선택한 후 **시스템이 관리하는 크기**를 클릭하고 **설정**을 클릭합니다.

7. **D** 드라이브를 선택하고 **페이징 파일 없음**을 클릭하고 **설정**을 클릭합니다.

8. 적용을 클릭합니다. 변경 내용을 적용하려면 컴퓨터를 다시 시작해야 한다는 경고가 표시됩니다.

9. 가상 컴퓨터를 다시 시작합니다.




## 드라이브 문자 변경 

1. VM이 다시 시작되면 VM에 다시 로그인합니다.

2. **시작** 메뉴를 클릭하고 **diskmgmt.msc**를 입력한 후 Enter 키를 누릅니다. 디스크 관리가 시작됩니다.

3. 임시 저장소 드라이브인 **D**를 마우스 오른쪽 단추로 클릭하고 **드라이브 문자 및 경로 변경**을 선택합니다.

4. 드라이브 문자에서 드라이브 **G**를 선택한 후 **확인**을 클릭합니다.

5. 데이터 디스크를 마우스 오른쪽 단추로 클릭하고 **드라이브 문자 및 경로 변경**을 선택합니다.

6. 드라이브 문자에서 드라이브 **D**를 선택한 후 **확인**을 클릭합니다.

7. 임시 저장소 드라이브인 **G**를 마우스 오른쪽 단추로 클릭하고 **드라이브 문자 및 경로 변경**을 선택합니다.

8. 드라이브 문자에서 드라이브 **E**를 선택한 후 **확인**을 클릭합니다.

> [AZURE.NOTE]VM에 다른 디스크 또는 드라이브가 있으면 동일한 방법을 사용하여 다른 디스크 및 드라이브의 드라이브 문자를 다시 할당합니다. 디스크 구성은 C: 운영 체제 디스크 - D: 데이터 디스크- E: 임시 디스크로 구성하는 것이 좋습니다.



## pagefile.sys를 임시 저장소 드라이브로 다시 이동합니다. 

1. **시작** 메뉴를 마우스 오른쪽 단추로 클릭하고 **시스템**을 선택합니다.

2. 왼쪽 메뉴에서 **고급 시스템 설정**을 선택합니다.

3. **성능** 섹션에서 **설정**을 선택합니다.

4. **고급** 탭을 선택합니다.

5. **가상 메모리** 섹션에서 **변경**을 선택합니다.

6. 운영 체제 드라이브 **C**를 선택하고 **페이징 파일 없음**을 클릭하고 **설정**을 클릭합니다.

7. 임시 저장소 드라이브 **E**를 선택한 후 **시스템이 관리하는 크기**를 클릭하고 **설정**을 클릭합니다.

8. **Apply**를 클릭합니다. 변경 내용을 적용하려면 컴퓨터를 다시 시작해야 한다는 경고가 표시됩니다.

9. 가상 컴퓨터를 다시 시작합니다.




## 추가 리소스
[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Logon]

[Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법][Detach]

[Azure 저장소 계정 정보][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md

[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Nov15_HO2-->