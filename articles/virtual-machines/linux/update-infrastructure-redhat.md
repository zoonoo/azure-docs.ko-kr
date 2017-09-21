---
title: RHUI(Red Hat Update Infrastructure) | Microsoft Docs
description: "Microsoft Azure의 주문형 Red Hat Enterprise Linux 인스턴스에 대한 RHUI(Red Hat Update Infrastructure)에 대해 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 43d9095bcbd490abc6d01c214a17b8586f92ed1e
ms.contentlocale: ko-kr
ms.lasthandoff: 09/15/2017

---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure의 주문형 Red Hat Enterprise Linux Vm에 대한 RHUI(Red Hat Update Infrastructure)
 [Red Hat 업데이트 인프라](https://access.redhat.com/products/red-hat-update-infrastructure)를 사용하면 클라우드 공급자(예: Azure)가 Red Hat 호스트 리포지토리 콘텐츠를 미러링하고, Azure 관련 콘텐츠를 포함한 사용자 지정 저장소를 만들고, 최종 사용자 VM에 사용할 수 있도록 합니다.

RHEL(Red Hat Enterprise Linux) 종량제(PAYG) 이미지는 Azure RHUI에 액세스하도록 미리 구성됩니다. 따라서 추가 구성을 할 필요가 없습니다. RHEL 인스턴스가 최신 업데이트를 적용할 준비가 되면 `sudo yum update`를 실행합니다. 이 서비스는 RHEL PAYG 소프트웨어 요금의 일부로 포함됩니다.

## <a name="important-information-about-azure-rhui"></a>Azure RHUI에 대한 중요한 정보

1. 현재 RHUI는 지정된 RHEL 제품군(RHEL6 또는 RHEL7)의 사소한 최신 릴리스를 지원합니다. `sudo yum update`를 실행할 때 RHUI에 연결된 RHEL VM 인스턴스는 사소한 최신 버전으로 업그레이드됩니다. 

    예를 들어 RHEL 7.2 PAYG 이미지에서 VM을 프로비전하고 `sudo yum update`를 실행하는 경우 RHEL 7.4 VM(RHEL7 제품군에서 현재 사소한 최신 버전)이 설치됩니다.

    이 동작을 방지하려면에 [Azure용 Red Hat 기반 가상 컴퓨터 만들기 및 업로드](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서에 설명된 대로 고유한 이미지를 빌드하고 다른 업데이트 인프라([Red Hat 콘텐츠 전달 서버에 직접](https://access.redhat.com/solutions/253273) 또는 Red Hat 위성 서버)에 연결해야 합니다.

2. Azure 호스티드 RHUI에 대한 액세스는 RHEL PAYG 이미지 가격에 포함됩니다. Azure 호스티드 RHUI에서 PAYG RHEL VM의 등록을 취소하더라도 가상 컴퓨터를 사용자 라이선스 필요(BYOL) 형식 VM으로 변환하지 않습니다. 업데이트의 다른 원본에 동일한 VM을 등록하는 경우 Azure RHEL 소프트웨어 요금에 한 번, 이전에 지불된 Red Hat 구독에 대한 한 번 등 두 배의 _간접_ 요금이 부과될 수 있습니다. Azure 호스티드 RHUI 이외의 업데이트 인프라를 지속적으로 사용해야 한다면 [Azure용 Red Hat 기반 가상 컴퓨터 만들기 및 업데이트](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 설명된 대로 사용자 고유의 (BYOL 유형) 이미지를 만들어 배포하도록 고려합니다.

3. Azure에서 RHEL PAYG 이미지의 두 가지 클래스(RHEL for SAP HANA, RHEL for SAP Business Applications)는 SAP 인증에 필요한 대로 특정 RHEL 사소한 버전에 남아 있는 전용 RHUI 채널에 연결됩니다. 

4. Azure 호스티드 RHUI에 대한 액세스는 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)내의 Vm에 제한됩니다. 온-프레미스 네트워크 인프라를 통해 모든 VM 트래픽을 프록시로 사용하는 경우 Azure RHUI에 액세스하도록 RHEL PAYG VM에 대한 사용자 정의 경로를 설정해야 할 수 있습니다.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI 콘텐츠 배달 서버에 대한 IP
RHUI는 RHEL 주문형 이미지를 사용할 수 있는 모든 지역에서 제공됩니다. RHUI는 현재 [Azure 상태 대시보드](https://azure.microsoft.com/status/) 페이지, Azure US Government 및 Azure 독일 지역에 나열된 모든 공용 지역을 포함합니다. 

네트워크 구성을 사용하여 RHEL PAYG VM에서 추가 액세스를 제한하려는 경우 사용자의 환경에 따라 작동하도록 다음 IP를 `yum update`에 대해 허용해야 합니다. 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure 인프라 업데이트

2016년 9월에 업데이트된 Azure RHUI를 배포했고 2017년 4월에 기존 Azure RHUI를 종료했습니다. 2016년 9월부터 RHEL PAYG 이미지(또는 해당 스냅숏)를 사용한 경우 새 Azure RHUI에 자동으로 연결되어 있습니다. 그러나 이전 스냅숏/VM이 설치되어 있다면 해당 구성은 아래에 설명한 대로 Azure RHUI에 액세스하도록 수동으로 업데이트되어야 합니다.

모든 VM에서 하위 지역에 관계없이 단일 끝점(rhui-1.micrsoft.com)을 사용할 수 있도록 새 Azure RHUI 서버가 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)를 통해 배포됩니다. 

### <a name="troubleshooting-connection-problems-to-azure-rhui"></a>Azure RHUI에 대한 연결 문제 해결
Azure RHEL PAYG VM에서 Azure RHUI에 연결할 때 문제가 발생하는 경우 다음 단계를 수행합니다.
1. Azure RHUI 끝점에 대한 VM 구성 검사

    `/etc/yum.repos.d/rh-cloud.repo` 파일이 해당 파일에 있는 `[rhui-microsoft-azure-rhel*]` 섹션의 baseurl에서 `rhui-[1-3].microsoft.com`에 대한 참조를 포함하는지 확인합니다. 그렇다면 새 Azure RHUI를 사용 중입니다.

    다음 `mirrorlist.*cds[1-4].cloudapp.net` 패턴으로 위치를 가리키는 경우 구성 업데이트가 필요합니다. 이전 VM 스냅숏을 사용하는 경우 새 Azure RHUI를 가리키도록 업데이트합니다.

2. Azure 호스티드 RHUI에 대한 액세스는 [Microsoft Azure 데이터 센터 IP 범위] (https://www.microsoft.com/download/details.aspx?id=41653)내의 VM으로 제한됩니다.
 
3. 새 구성을 사용하고 VM이 Azure IP 범위에서 연결된다고 확인되며 Azure RHUI에 연결할 수 없으면 Microsoft 또는 Red Hat을 사용하여 지원 사례를 정리합니다.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Azure RHUI 서버를 사용하기 위한 수동 업데이트 절차
이 절차는 참조용으로만 제공되고 RHEL PAYG 이미지는 이미 Azure RHUI에 연결하는 올바른 구성을 제공합니다.

공개 키 서명 다운로드(curl을 통해)

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

다운로드한 키 확인

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

출력을 확인하고 `keyid` 및 `user ID packet` 확인

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

공개 키 설치

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

클라이언트 RPM 다운로드, 확인 및 설치 

> [!NOTE]
> 패키지 버전이 변경되고 Azure RHUI에 수동으로 연결하는 경우 갤러리에서 최신 이미지를 프로비전하여 각 RHEL 제품군에 대한 최신 버전의 클라이언트 패키지를 찾을 수 있습니다.
> 

다운로드: RHEL 6의 경우

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
```

RHEL 7의 경우

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
```

다음 확인

```bash
rpm -Kv azureclient.rpm
```

출력에서 패키지 서명이 정상인지 확인

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

RPM 설치

```bash
sudo rpm -U azureclient.rpm
```

완료되면 VM에서 Azure RHUI에 액세스할 수 있는지 확인

## <a name="next-steps"></a>다음 단계
Azure Marketplace 종량제 이미지에서 Red Hat Enterprise Linux VM을 만들고 Azure 호스티드 RHUI를 활용하려면 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)로 갑니다. 
