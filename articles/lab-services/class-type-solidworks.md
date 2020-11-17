---
title: Azure Lab Services를 사용 하 여 엔지니어링을 위한 SOLIDWORKS lab 설정 Microsoft Docs
description: SOLIDWORKS를 사용 하 여 엔지니어링 과정을 위한 랩을 설정 하는 방법에 대해 알아봅니다.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: dc6fdadbdfdbdd1d32f640e356a67841187a83c9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651807"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>SOLIDWORKS를 사용 하 여 엔지니어링 클래스에 대 한 랩 설정

[SOLIDWORKS](https://www.solidworks.com/) 는 단색 개체를 모델링 하기 위한 3d CAD (컴퓨터 사용 디자인) 환경을 제공 하며 다양 한 종류의 엔지니어링 필드에 사용 됩니다.  SOLIDWORKS를 사용 하면 엔지니어가 디자인을 쉽게 만들고, 시각화 하 고, 시뮬레이션 하 고, 문서화할 수 있습니다.

일반적으로 대학에서 사용 되는 라이선스 옵션은 SOLIDWORKS ' 네트워크 라이선스 '입니다.   이 옵션을 사용 하는 경우 사용자는 라이선스 서버에서 관리 하는 라이선스 풀을 공유 합니다.  동시 사용자 수에 대해 충분 한 라이선스가 있어야 하므로 이러한 종류의 라이선스를 "부동" 라이선스 라고도 합니다.  사용자가 SOLIDWORKS를 사용 하 여 완료 되 면 해당 라이선스는 다른 사용자가 다시 사용할 수 있도록 중앙에서 관리 되는 라이선스 풀로 다시 이동 합니다.

이 문서에서는 SOLIDWORKS 2019 및 네트워크 라이선스를 사용 하는 클래스를 설정 하는 방법을 보여 줍니다.

## <a name="license-server"></a>라이선스 서버

SOLIDWORKS 네트워크 라이선스를 사용 하려면 라이선스 서버에 SolidNetWork 라이선스 관리자를 설치 하 고 활성화 해야 합니다.  이 라이선스 서버는 일반적으로 온-프레미스 네트워크 또는 Azure 내의 개인 네트워크에 있습니다.  서버에서 SolidNetWork 라이선스 관리자를 설정 하는 방법에 대 한 자세한 내용은 SOLIDWORKS 설치 가이드에서 [라이선스 관리자 설치 및 활성화](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) 를 참조 하십시오.  이 설정을 사용 하는 경우 이후 단계에서 필요 하므로 사용 되는 **포트 번호** 와 [**일련 번호**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) 를 잊지 마십시오.

라이선스 서버를 설정한 후에는 [가상 네트워크 (VNet)](./how-to-connect-peer-virtual-network.md) 를 [랩 계정](./tutorial-setup-lab-account.md)에 피어 링 해야 합니다.  랩 가상 머신에서 라이선스 서버에 액세스할 수 있고 다른 방법으로는 랩을 만들기 전에 네트워크 피어 링을 수행 해야 합니다.

> [!NOTE]
> 랩 가상 머신과 라이선스 서버 간의 통신을 허용 하도록 방화벽에서 적절 한 포트가 열려 있는지 확인 해야 합니다.  예를 들어 라이선스 서버의 방화벽에 인바운드 및 아웃 바운드 규칙을 추가 하는 방법을 보여 주는 [Windows 방화벽의 라이선스 관리자 컴퓨터 포트를 수정](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) 하는 방법에 대 한 지침을 참조 하세요.  랩 가상 컴퓨터에 대 한 포트를 열어야 할 수도 있습니다.  랩의 공용 IP 주소를 가져오는 방법을 비롯 하 여이에 대 한 자세한 내용은 랩 [에 대 한 방화벽 설정](./how-to-configure-firewall-settings.md) 문서의 단계를 따르세요.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정 하려면 시작 하려면 Azure 구독 및 랩 계정이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 방법에 대 한 자세한 내용은 [랩 계정을 설정 하는 방법](./tutorial-setup-lab-account.md)에 대 한 자습서를 참조 하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명 된 설정을 사용 하도록 설정 합니다. Marketplace 이미지를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [랩 작성자가 사용할 수 있는 marketplace 이미지를 지정 하는 방법](./specify-marketplace-images.md)에 대 한 문서를 참조 하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|Marketplace 이미지| 랩 계정 내에서 사용할 Windows 10 Pro 이미지를 사용 하도록 설정 합니다.|

> [!NOTE]
> Windows 10 외에도 SOLIDWORKS는 다른 버전의 Windows를 지원 합니다.  자세한 내용은 [SOLIDWORKS 시스템 요구 사항](https://www.solidworks.com/sw/support/SystemRequirements.html) 을 참조 하세요.

### <a name="lab-settings"></a>랩 설정

클래스 룸 랩을 설정 하는 경우 아래 표의 설정을 사용 합니다. 교실 랩을 만드는 방법에 대 한 자세한 내용은 교실 랩 설정 자습서를 참조 하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| **작은 GPU (시각화)**.  이 VM은 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합 합니다.|  
|가상 머신 이미지| Windows 10 Pro|

> [!NOTE]
> **소규모 GPU (시각화)** 가상 머신 크기는 고성능 그래픽 환경을 사용 하도록 구성 됩니다.  이 가상 컴퓨터 크기에 대 한 자세한 내용은 Gpu를 사용 하 [여 랩을 설정 하는 방법](./how-to-setup-lab-gpu.md)에 대 한 문서를 참조 하세요.

> [!WARNING]
> 랩을 만들기 **전에** 랩 계정에 대 한 [가상 네트워크](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) 를 라이선스 서버의 가상 네트워크에 피어 링 해야 합니다.

## <a name="template-virtual-machine-configuration"></a>템플릿 가상 머신 구성

이 섹션의 단계에서는 SOLIDWORKS 설치 파일을 다운로드 하 고 클라이언트 소프트웨어를 설치 하 여 템플릿 가상 머신을 설정 하는 방법을 보여 줍니다.

1. 템플릿 가상 머신을 시작 하 고 RDP를 사용 하 여 컴퓨터에 연결 합니다.

1. SOLIDWORKS client 소프트웨어용 설치 파일을 다운로드 합니다. 다운로드 하는 두 가지 옵션은 다음과 같습니다.
   - [SOLIDWORKS 고객 포털](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F)에서 다운로드 합니다.
   - 서버에 있는 디렉터리에서 다운로드 합니다.  이 옵션을 사용 하는 경우 템플릿 가상 머신에서 서버에 액세스할 수 있는지 확인 해야 합니다.  예를 들어이 서버는 랩 계정과 피어 링 되는 동일한 가상 네트워크에 있을 수 있습니다.
  
    자세한 내용은 SOLIDWORKS의 SOLIDWORKS 설치 가이드 [에서 개별 컴퓨터에 설치](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) 를 참조 하세요.

1. 설치 파일을 다운로드 한 후 SOLIDWORKS 설치 관리자를 사용 하 여 클라이언트 소프트웨어를 설치 합니다. SOLIDWORKS 설치 가이드에서 [라이선스 클라이언트를 설치 하는](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) 방법에 대 한 세부 정보를 참조 하세요.

    > [!NOTE]
    > **서버 추가** 대화 상자에 라이선스 서버에 사용 되는 **포트 번호** 와 라이선스 서버의 이름 또는 IP 주소를 입력 하 라는 메시지가 표시 됩니다.

## <a name="cost"></a>비용

이 클래스에 대해 가능한 예상 비용을 살펴보겠습니다. 이러한 예상치는 라이선스 서버를 실행 하는 비용을 포함 하지 않습니다. 25 명의 학생 클래스를 사용 합니다. 예약 된 클래스 시간은 20 시간입니다. 또한 각 학생은 예약 된 클래스 시간 외에도 과제 또는 배정에 대해 10 시간 할당량을 얻습니다. 선택한 가상 컴퓨터 크기는 **작은 GPU (시각화)** 이며 랩 단위는 160입니다.

25 개 학생 \* (20 개의 예약 된 시간 + 10 할당량 시간) \* 160 랩 단위 * 0.01 usd/시간 = 1200.00 USD

>[!IMPORTANT]
> 비용 예측은 예를 들어 목적 으로만 사용 됩니다.  가격 책정에 대 한 최신 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.  

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)