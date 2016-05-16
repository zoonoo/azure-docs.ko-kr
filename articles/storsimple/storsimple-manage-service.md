<properties 
   pageTitle="StorSimple Manager 서비스 배포 | Microsoft Azure"
   description="Azure 클래식 포털에서 StorSimple 관리자 서비스를 만들고 삭제하는 방법 및 서비스 등록 키를 관리하는 방법에 대해 설명합니다."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/04/2016"
   ms.author="v-sharos" />

# StorSimple 관리자 서비스 배포

## 개요

StorSimple 관리자 서비스는 Microsoft Azure에서 실행되며 여러 StorSimple 장치에 연결됩니다. 서비스를 만든 후에 브라우저에서 실행되는 Microsoft Azure 클래식 포털에서 이러한 장치를 관리하는 데 사용할 수 있습니다. 하나의 중앙 위치에서 StorSimple 관리자 서비스에 연결된 모든 장치를 모닝터링하여 관리 부담을 최소화할 수 있습니다.

StorSimple 관리자 방문 페이지에는 StorSimple 저장소 장치를 관리하는 데 사용할 수 있는 모든 StorSimple 관리자 서비스가 나열되어 있습니다. 각 StorSimple 관리자 서비스의 경우, 다음 정보가 StorSimple 관리자 페이지에 있습니다.

- **이름** – 만들었을 때 StorSimple 관리자 서비스에 할당된 이름입니다. 서비스 이름은 서비스를 만든 후에 변경할 수 없습니다.

- **상태** – **활성**, **만들기** 또는 **온라인** 등의 서비스 상태입니다.

- **위치** – StorSimple 장치를 배포할 지리적 위치입니다.

- **구독** – 서비스와 연관된 청구 구독입니다.

StorSimple 관리자 페이지를 통해 수행할 수 있는 일반적인 작업은 다음과 같습니다.

- 서비스 만들기
- 서비스 삭제
- 서비스 등록 키 가져오기
- 서비스 등록 키 생성

이 자습서에서는 이러한 각 작업을 수행하는 방법을 설명합니다.

## 서비스 만들기

StorSimple 장치를 배포하려는 경우 **빠른 생성** 옵션을 사용하여 StorSimple 관리자 서비스를 만듭니다. 서비스를 만들려면:

- 엔터프라이즈 계약을 사용하여 구독
- 활성 Microsoft Azure 저장소 계정
- 액세스 관리에 사용되는 청구 정보

또한 서비스를 만들 때 기본 저장소 계정을 생성하도록 선택할 수 있습니다.

하나의 서비스로 여러 장치를 관리할 수 있습니다. 하지만 하나의 장치는 여러 서비스로 확장할 수 없습니다. 대규모 엔터프라이즈는 서로 다른 구독, 조직 또는 배포 위치와 동작하는 여러 서비스 인스턴스를 가질 수 있습니다. StorSimple 8000 시리즈 장치와 StorSimple 가상 배열을 관리하려면 별도의 StorSimple Manager 서비스 인스턴스가 필요합니다.

서비스를 만들려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## 서비스 삭제

서비스를 삭제하기 전에 사용 중인 연결 장치가 없는지 확인합니다. 서비스를 사용 중인 경우 연결된 장치를 비활성화합니다. 비활성화 작업은 장치와 서비스 간의 연결을 제공하지만 클라우드의 장치 데이터는 유지합니다.

[AZURE.IMPORTANT] 서비스가 삭제된 후에는 작업을 되돌릴 수 없습니다. 서비스를 사용하던 모든 장치는 다른 서비스에 사용하기 전에 공장 기본 설정을 해야 합니다. 이 시나리오에서는 구성뿐 아니라 장치의 로컬 데이터도 손실됩니다.

서비스를 삭제하려면 다음 단계를 수행합니다.

### 서비스를 삭제하려면

1. **StorSimple 관리자 서비스** 페이지에서 삭제하려는 서비스를 선택합니다.

1. 페이지 맨 아래에서 **삭제**를 클릭합니다.

1. 확인 알림에서 **예**를 클릭합니다. 서비스 삭제에는 몇 분 정도 걸릴 수 있습니다.

## 서비스 등록 키 가져오기

서비스를 성공적으로 만든 후에 서비스에 StorSimple 장치를 등록해야 합니다. 처음으로 StorSimple 장치를 등록하려면 서비스 등록 키가 필요합니다. 기존 StorSimple 서비스에 추가 장치를 등록하려면 등록 키와 서비스 데이터 암호화 키(등록 시 첫 번째 장치에서 생성된 키)가 모두 필요합니다. 서비스 데이터 암호화 키에 대한 자세한 내용은 [StorSimple 보안](storsimple-security.md)을 참조하세요. **서비스** 페이지의 **등록 키**에 액세스하여 등록 키를 가져올 수 있습니다.

서비스 등록 키를 가져오려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

서비스 등록 키를 안전한 장소에 보관합니다. 이 키와 서비스 데이터 암호화 키는 이 서비스에 추가 장치를 등록할 때 필요합니다. 서비스 등록 키를 가져온 후 StorSimple 인터페이스용 Windows PowerShell을 통해 장치를 구성해야 합니다.

이 등록 키 사용 방법에 대한 세부 정보는 [3단계: StorSimple용 Windows PowerShell을 통해 장치 구성 및 등록](storsimple-deployment-walkthrough.md#step-2-configure-and-register-the-device-through-windows-powershell-for-storsimple)을 참조하세요.

## 서비스 등록 키 생성

키 회전을 수행해야 하거나 서비스 관리자 목록이 변경된 경우 서비스 등록 키를 다시 생성해야 합니다. 키를 다시 생성하면 후속 장치 등록을 위해서만 새 키가 사용됩니다. 이미 등록된 장치는 이 과정에 영향을 받지 않습니다.

서비스 등록 키를 다시 생성하려 다음 단계를 수행합니다.

### 서비스 등록 키를 다시 생성하려면

1. **StorSimple 관리자 서비스** 페이지에서 **등록 키**를 클릭합니다.

1. **서비스 등록 키** 대화 상자에서 **등록**을 클릭합니다.

1. 확인 메시지가 표시됩니다. **확인**을 클릭하여 등록을 계속합니다.

1. 새 서비스 등록 키가 나타납니다.

1. 이 서비스에 새 장치 등록을 위해 이 키를 복사하고 저장합니다.

1. 확인 아이콘![확인 아이콘](./media/storsimple-manage-service/HCS_CheckIcon.png)을 클릭하고 이 대화 상자를 닫습니다.


## 다음 단계

- [StorSimple 배포 프로세스](storsimple-deployment-walkthrough.md)에 대해 자세히 알아봅니다.

- [StorSimple 저장소 계정 관리](storsimple-manage-storage-accounts.md)에 대해 자세히 알아봅니다.

- [StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 자세히 알아봅니다.

 

<!---HONumber=AcomDC_0504_2016-->