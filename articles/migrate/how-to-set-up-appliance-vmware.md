---
title: VMware Vm에 대 한 Azure Migrate 서버 평가/마이그레이션에 대 한 어플라이언스 설정 | Microsoft Docs
description: Azure Migrate Server 평가/마이그레이션을 사용 하 여 VMware Vm의 검색, 평가 및 에이전트 없는 마이그레이션을 위해 어플라이언스를 설정 하는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/10/2019
ms.author: raynew
ms.openlocfilehash: 77bf9a0f73519aa979da49614475daf70f582a9e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467132"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>VMware Vm에 대 한 어플라이언스 설정

이 문서에서는 Azure Migrate Server 평가 도구를 사용 하 여 VMware Vm을 평가 하거나 Azure Migrate 서버 마이그레이션 도구를 사용 하 여 VMware Vm을 에이전트 없이 마이그레이션할 때 Azure로 마이그레이션하는 경우 Azure Migrate 어플라이언스를 설정 하는 방법을 설명 합니다.

VMware VM 어플라이언스는 다음을 수행 하기 위해 Azure Migrate 서버 평가/마이그레이션에 사용 되는 경량 어플라이언스입니다.

- 온-프레미스 VMware VM을 검색합니다.
- 검색 된 Vm에 대 한 메타 데이터 및 성능 데이터를 Azure Migrate 서버 평가/마이그레이션으로 보냅니다.

Azure Migrate 어플라이언스에 [대해 자세히 알아보세요](migrate-appliance.md) .


## <a name="appliance-deployment-steps"></a>어플라이언스 배포 단계

어플라이언스를 설정하려면 다음을 수행합니다.
- OVA 템플릿 파일을 다운로드하여 vCenter Server로 가져옵니다.
- 어플라이언스를 만들고, Azure Migrate 서버 평가에 연결할 수 있는지 확인합니다.
- 어플라이언스를 처음으로 구성하고, Azure Migrate 프로젝트에 등록합니다.

## <a name="download-the-ova-template"></a>OVA 템플릿 다운로드

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 클릭 합니다.
2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, VMWare vSphere 하이퍼바이저 사용**을 클릭합니다.
3. **다운로드**를 클릭하여 .OVA 템플릿 파일을 다운로드합니다.



### <a name="verify-security"></a>보안 확인

배포하기 전에 OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행 하 여 OVA에 대 한 해시를 생성 합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 어플라이언스 버전 1.0.0.5의 경우 생성 된 해시가 이러한 설정과 일치 해야 합니다.

  **알고리즘** | **해시 값**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>어플라이언스 VM 만들기

다운로드한 파일을 가져오고 VM을 만듭니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.
2. OVF 템플릿 배포 마법사 > **원본**에서 OVA 파일의 위치를 지정합니다.
3. **이름** 및 **위치**에서 친숙한 VM 이름을 지정합니다. VM을 호스팅할 인벤토리 개체를 선택합니다.
5. **호스트/클러스터**에서 VM이 실행될 호스트 또는 클러스터를 지정합니다.
6. **스토리지**에서 VM에 대한 스토리지 대상을 지정합니다.
7. **디스크 형식**에서 디스크 유형 및 크기를 지정합니다.
8. **네트워크 매핑**에서 VM이 연결할 네트워크를 지정합니다. 메타데이터를 Azure Migrate 서버 평가에 보내려면 네트워크에 인터넷 연결이 필요합니다.
9. 설정을 검토 및 확인한 후 **마침**을 클릭합니다.


### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스 VM에서 [Azure URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements)에 연결할 수 있는지 확인합니다.


## <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

1. vSphere Client 콘솔에서 VM > **Open Console**을 마우스 오른쪽 단추로 클릭합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 제공합니다.
3. VM에 연결할 수 있는 모든 컴퓨터에서 브라우저를 열고 어플라이언스 웹 앱의 URL ( **https://*어플라이언스 이름 또는 IP 주소*: 44368**)을 엽니다.

   또는 앱 바로 가기를 클릭하여 어플라이언스 데스크톱에서 앱을 열 수 있습니다.
4. 웹앱 > **필수 구성 요소 설정**에서 다음을 수행합니다.
    - **라이선스**: 사용 조건에 동의 하 고 타사 정보를 읽습니다.
    - **연결**: 앱에서 VM이 인터넷에 연결 되어 있는지 확인 합니다. VM에서 프록시를 사용하는 경우:
        - **프록시 설정**을 클릭하고, 프록시 주소와 수신 포트를 http://ProxyIPAddress 또는 http://ProxyFQDN 형식으로 지정합니다.
        - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
        - HTTP 프록시만 지원됩니다.
    - **시간 동기화**: 시간이 확인 됩니다. 검색을 제대로 작동 하려면 어플라이언스의 시간이 인터넷 시간과 동기화 되어야 합니다.
    - **업데이트 설치**: Azure Migrate 최신 어플라이언스 업데이트가 설치 되어 있는지 확인 합니다.
    - **INSTALL VDDK**: Azure Migrate VMWare VSPHERE VDDK (가상 디스크 개발 키트)가 설치 되어 있는지 확인 합니다.
        - Azure migration은 VDDK를 사용 하 여 Azure로 마이그레이션하는 동안 컴퓨터를 복제 합니다.
        - VMware에서 VDDK 6.7을 다운로드하고, 다운로드한 zip 콘텐츠를 어플라이언스의 지정된 위치에 추출합니다.

## <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. **로그인**을 클릭합니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
2. 새로 만들기 탭에서 Azure 자격 증명을 사용하여 로그인합니다.
    - 사용자 이름과 암호를 사용하여 로그인합니다.
    - PIN을 사용한 로그인은 지원되지 않습니다.
3. 성공적으로 로그인하면 웹앱으로 돌아갑니다.
2. Azure Migrate 프로젝트가 만들어진 구독을 선택합니다. 그런 다음, 해당 프로젝트를 선택합니다.
3. 어플라이언스의 이름을 지정합니다. 이름은 14자 이하의 영숫자여야 합니다.
4. **등록**을 클릭합니다.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>vCenter Server 및 VM 자격 증명을 제공 하 여 연속 검색 시작

어플라이언스는 Vm의 구성 및 성능 데이터를 검색 하기 위해 vCenter Server에 연결 해야 합니다.

### <a name="specify-vcenter-server-details"></a>vCenter Server 세부 정보 지정
1. **vCenter Server 세부 정보 지정**에서 vCenter Server의 이름(FQDN) 또는 IP 주소를 지정합니다. 기본 포트를 그대로 유지하거나 vCenter Server에서 수신하는 사용자 지정 포트를 지정할 수 있습니다.
2. **사용자 이름** 및 **암호**에서 어플라이언스가 vCenter Server에서 VM을 검색하는 데 사용할 읽기 전용 계정 자격 증명을 지정합니다. 계정에 [검색에 필요한 권한이](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions)있는지 확인 합니다. 이에 따라 vCenter 계정에 대 한 액세스를 제한 하 여 검색 범위를 지정할 수 있습니다. 범위 검색에 대 한 자세한 내용은 [여기](tutorial-assess-vmware.md#scoping-discovery)를 참조 하세요.
3. **연결 유효성 검사**를 클릭하여 어플라이언스에서 vCenter Server에 연결할 수 있는지 확인합니다.

### <a name="specify-vm-credentials"></a>VM 자격 증명 지정
응용 프로그램, 역할 및 기능을 검색 하 고 Vm의 종속성을 시각화 하는 데 VMware Vm에 대 한 액세스 권한이 있는 VM 자격 증명을 제공할 수 있습니다. Windows Vm에 대해 하나의 자격 증명과 Linux Vm에 대 한 자격 증명을 추가할 수 있습니다. 필요한 액세스 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) .

> [!NOTE]
> 이 입력은 선택 사항이 며 응용 프로그램 검색 및 에이전트 없는 종속성 시각화를 사용 하도록 설정 하는 데 필요 합니다.

1. **Vm에 대 한 응용 프로그램 및 종속성 검색**에서 **자격 증명 추가**를 클릭 합니다.
2. **운영 체제**를 선택 합니다.
3. 자격 증명에 대 한 친숙 한 이름을 제공 합니다.
4. **사용자 이름** 및 **암호**에서 vm에 대 한 게스트 액세스 권한이 있는 계정을 지정 합니다.
5. **추가**를 클릭합니다.

VCenter Server 및 VM 자격 증명 (선택 사항)을 지정한 후에는 **검색 저장 및 시작** 을 클릭 하 여 온-프레미스 환경의 검색을 시작 합니다.

검색된 VM의 메타데이터가 포털에 표시되는 데 약 15분이 걸립니다. 설치 된 응용 프로그램, 역할 및 기능을 검색 하는 데 다소 시간이 소요 되는 경우 기간은 검색 되는 Vm의 수에 따라 달라 집니다. 500 Vm의 경우 응용 프로그램 인벤토리가 Azure Migrate 포털에 표시 되는 데 약 1 시간이 걸립니다.

## <a name="next-steps"></a>다음 단계

[VMware 평가](tutorial-assess-vmware.md) 및 [에이전트 없는 마이그레이션](tutorial-migrate-vmware.md)에 대 한 자습서를 검토 합니다.
