---
title: "Azure의 Linux VM에서 SAP 시작 | Microsoft Docs"
description: "Microsoft Azure의 VM(가상 컴퓨터)에서 실행되는 SAP 솔루션에 대한 자세한 정보"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 9e64fc5425db5ffde6af493e961edfcac527b6df
ms.lasthandoff: 03/03/2017


---
# <a name="using-sap-on-azure-linux-virtual-machines-vms"></a>Azure Linux VM(가상 컴퓨터)에서 SAP 사용
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md(Linux VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드) [dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f(VM 및 VHD에 대한 캐싱) [dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91(소프트웨어 RAID) [dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152(Microsoft Azure Storage) [dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64(RDBMS 배포 구조) [dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3(Azure VM을 사용한 고가용성 및 재해 복구) [dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268(SQL Server 2012 SP1 CU4 이상) [dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b(SQL Server 2012 SP1 CU3 및 이전 릴리스) [dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8(Microsoft Azure Marketplace에서 SQL Server 이미지 사용) [dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30(Azure의 SAP용 SQL Server에 대한 일반적 요약) [dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737(SQL Server RDBMS에 대한 고유 정보) [dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573(저장소 구성) [dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d(백업 및 복원) [dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c(백업 및 복원에 대한 성능 고려 사항) [dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818(기타) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md(Linux VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드) [deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94(SAP 리소스) [deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab(사용자 지정 이미지를 사용하여 VM 배포) [deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281(시나리오 1: SAP용 Azure 마켓플레이스에서 VM 배포) [deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2(시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포) [deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1(시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동) [deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e(Microsoft Azure에서의 SAP용 VM 배포 시나리오) [deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7(Azure PowerShell cmdlet 배포) [deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e(SAP 관련 PowerShell cmdlet 다운로드 및 가져오기) [deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc(온-프레미스 도메인에 VM 가입 - Windows에만 해당) [deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542(Linux) [deployment-guide-4.4]:virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d(Azure VM 에이전트 다운로드, 설치 및 사용) [deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4(Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f(Azure CLI) [deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca(SAP용 Azure 고급 모니터링 확장 구성) [deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2(SAP용 Azure 고급 모니터링에 대한 준비 검사) [deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1(Azure 모니터링 인프라 구성에 대한 상태 검사) [deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8(SAP용 Azure 모니터링 인프라의 추가 문제 해결)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (Linux VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (리소스) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP 응용 프로그램 서버에 대한 고가용성) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (SAP 인스턴스에 대해 자동 시작 사용) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure 지역) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (장애 도메인) [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (업그레이드 도메인) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure 가용성 집합) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (The Microsoft Azure Virtual Machine 개념) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 프리미엄 저장소) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (고객별 이미지를 사용하여 VM 배포) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로의 VM 이동 준비) [planning-guide-5.2.2]:virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (SAP용 고객별 이미지를 사용하여 VM 배포 준비) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Azure용 SAP로 VM 준비) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure 디스크와 Azure 이미지 간 차이점) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (온-프레미스에서 Azure로 VHD 업로드) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Azure 저장소 계정 간 디스크 복사) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP 배포를 위한 VM/VHD 구조) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (연결된 디스크에 대한 자동 탑재 설정) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (SAP 인스턴스의 클라우드 전용 배포 개념) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (SAP용 Azure 모니터링 솔루션) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 프리미엄 저장소)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../azure-resource-manager/resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:windows/classic/ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:windows/classic/ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

SAP 준비 클라우드 파트너로 Microsoft Azure를 선택하여 중요 업무용 SAP 워크로드를 확장 가능하고 규정을 준수하는 엔터프라이즈 입증 플랫폼에서 안정적으로 실행할 수 있습니다.  Azure의 확장성, 유연성 및 비용 절감을 활용하세요. Microsoft와 SAP 사이의 확장된 파트너 관계 덕분에 Azure에서 개발/테스트 및 프로덕션 시나리오 전체에서 SAP 응용 프로그램을 실행할 수 있으며 완전한 지원을 받을 수 있습니다. SAP NetWeaver에서 SAP S4/HANA로, Linux에서 Windows로, SAP HANA에서 SQL로, 모두 가능합니다. 

Microsoft Azure 가상 컴퓨터 서비스 및 Azure 큰 인스턴스의 SAP HANA와 함께 Microsoft는 포괄적인 IaaS(Infrastructure as a Service) 플랫폼을 제공합니다. Azure에서는 다양한 SAP 솔루션이 지원되므로 이 "시작 문서"는 최신 SAP 문서 모음에 대한 목차 역할을 합니다. 더 많은 제목이 문서 라이브러리에 추가되면 여기에서 확인할 수 있습니다. 

## <a name="sap-hana-certifications-on-microsoft-azure"></a>Microsoft Azure의 SAP HANA 인증
| SAP 제품 | 지원되는 OS | Azure 제품 |
| --- | --- | --- |
| SAP HANA Developer Edition(SQLODBC, ODBO(Windows 전용), ODBC, JDBC 드라이버로 구성된 HANA Client 소프트웨어, HANA Studio, HANA Database 포함) |Red Hat Enterprise Linux, SUSE Linux Enterprise |A7, A8 |
| HANA One |Red Hat Enterprise Linux, SUSE Linux Enterprise |DS14_v2(일반 공급 시) |
| SAP S/4HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5의 제어되는 가용성, Azure(큰 인스턴스)에서 SAP HANA 사용 |
| Suite on HANA, OLTP |Red Hat Enterprise Linux, SUSE Linux Enterprise |Azure(큰 인스턴스)에서 SAP HANA 사용 |
| HANA Enterprise for BW, OLAP |Red Hat Enterprise Linux, SUSE Linux Enterprise |단일 노드 배포의 GS5, Azure(큰 인스턴스)에서 SAP HANA 사용 |
| SAP BW/4HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |단일 노드 배포의 GS5, Azure(큰 인스턴스)에서 SAP HANA 사용 |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 인증
Microsoft Azure는 다음과 같은 SAP 제품에서 인증되었고 Microsoft와 SAP의 전폭적인 지원을 받고 있습니다.

| SAP 제품 | 게스트 OS | RDBMS | 가상 컴퓨터 유형 |
| --- | --- | --- | --- |
| SAP Business Suite 소프트웨어 |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle(Windows만 해당), DB2, SAP ASE |A5~A11, D11~D14, DS11~DS14, GS1~GS5 |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle(Windows만 해당), DB2, SAP ASE |A5~A11, D11~D14, DS11~DS14, GS1~GS5 |
| SAP BusinessObjects BI |Windows |해당 없음 |A5~A11, D11~D14, DS11~DS14, GS1~GS5 |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle(Windows만 해당), DB2, SAP ASE |A5~A11, D11~D14, DS11~DS14, GS1~GS5 |

## <a name="getting-started-with-sap-hana-on-azure"></a>Azure에서 SAP HANA 시작
제목: Azure VM에서 SAP HANA 수동 설치에 대한 빠른 시작 가이드

요약: 이 빠른 시작 가이드를 사용하면 SAP NetWeaver 7.5 및 SAP HANA SP12를 수동으로 설치하여 Azure VM에서 단일 인스턴스 SAP HANA 프로토타입/데모 시스템을 설정할 수 있습니다. 이 가이드는 독자가 JSON 템플릿 사용 옵션을 포함하여 Azure Portal 또는 Powershell/CLI를 통한 가상 컴퓨터나 가상 네트워크 배포 방법과 같은 Azure IaaS 기본 사항에 대해 잘 알고 있다는 것을 전제로 합니다. 또한 독자가 SAP HANA, SAP NetWeaver 및 온-프레미스 설치 방법에 익숙하고

업데이트한 날짜: 2016년 12월

[이 가이드는 여기서 확인할 수 있습니다.](virtual-machines-linux-sap-hana-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="overview-and-architecture-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA의 개요 및 아키텍처
제목: Azure(큰 인스턴스)에서 SAP HANA의 개요 및 아키텍처

요약: 이 아키텍처 및 기술적 배포 가이드는 Azure(큰 인스턴스)에서 새 SAP HANA에 SAP를 배포하는 데 도움이 되는 정보를 제공합니다. SAP 솔루션의 특정 설치를 설명하는 포괄적인 가이드라기 보다는 초기 배포 및 진행 중인 작업에 대한 유용한 정보를 제공합니다. SAP HANA 설치와 관련된 SAP 설명서(또는 해당 항목을 다루는 여러 SAP 지원 참고 사항) 대신 이 문서를 사용하지 마세요. 이 가이드는 개요를 제공하고 Azure(큰 인스턴스)에서 SAP HANA를 설치하는 방법에 대한 추가 정보를 제공합니다.

업데이트한 날짜: 2016년 12월

[이 가이드는 여기서 확인할 수 있습니다.](./linux/sap-hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA에 대한 인프라 및 연결
제목: Azure(큰 인스턴스)의 SAP HANA에 대한 인프라 및 연결

요약: 사용자와 Microsoft 엔터프라이즈 계정 팀 간에 Azure(큰 인스턴스)의 SAP HANA 구매가 완료된 후에 적절한 연결을 위해 다양한 네트워크 구성이 필요합니다.  이 문서에서는 다음 정보와 함께 공유되어야 하는 정보를 간단히 설명합니다. 또한 수집해야 하는 정보와 실행해야 하는 구성 스크립트에 대해서도 설명합니다. 

업데이트한 날짜: 2016년 12월

[이 가이드는 여기서 확인할 수 있습니다.](./linux/sap-hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="install-sap-hana-on-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA 설치
제목: Azure(큰 인스턴스)에서 SAP HANA 설치

요약: 이 문서에서는 Azure 큰 인스턴스에서 SAP HANA를 설치하기 위한 설치 절차를 설명합니다.

업데이트한 날짜: 2016년 12월

[이 가이드는 여기서 확인할 수 있습니다.](./linux/sap-hana-overview-sap-hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성 및 재해 복구
제목: Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성 및 재해 복구

요약: Azure(큰 인스턴스) 서버에서 업무상 중요한 SAP HANA를 실행할 때 HA(고가용성) 및 DR(재해 복구)은 매우 중요한 측면입니다. SAP, 시스템 통합업체 및/또는 Microsoft와 협의하여 적합한 HA/DR 전략을 설계하고 구현하는 것이 중요합니다. 사용자 환경별로 RPO(복구 지점 목표) 및 RTO(복구 시간 목표)와 같은 중요한 사항으로 고려해야 합니다.  이 문서에서는 기본 수준의 HA 및 DR을 사용하도록 설정하기 위한 옵션에 대해 설명합니다.

업데이트한 날짜: 2016년 12월

[이 문서는 여기에서 찾을 수 있습니다.](./linux/sap-hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="troubleshooting-and-monitoring-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA 문제 해결 및 모니터링
제목: Azure(큰 인스턴스)의 SAP HANA 문제 해결 및 모니터링

요약: 이 가이드에서는 Azure 환경에서 SAP HANA의 모니터링 설정 시 유용한 정보와 추가적인 문제 해결 정보를 제공합니다. 

업데이트한 날짜: 2016년 12월

[이 문서는 여기에서 찾을 수 있습니다.](./linux/sap-hana-overview-troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quickstart-guide-for-netweaver-on-suse-linux-on-azure"></a>Azure의 SUSE Linux NetWeaver에 대한 빠른 시작 가이드
제목: Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트 

요약: 이 문서에서는 Microsoft Azure SUSE Linux VM(가상 컴퓨터)에서 SAP NetWeaver를 실행할 때 고려해야 할 다양한 항목을 설명합니다. 2016년 5월 19일을 기준으로 SAP NetWeaver는 Azure의 SUSE Linux VM에서 공식적으로 지원됩니다. Linux 버전, SAP 커널 버전 등과 관련된 모든 세부 정보는 SAP 정보 1928533, "Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형"에서 찾을 수 있습니다.

업데이트 날짜: 2016년 9월

[이 가이드는 여기서 확인할 수 있습니다.](virtual-machines-linux-sap-on-suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="3da0389e-708b-4e82-b2a2-e92f132df89c"></a>계획 및 구현
제목: Linux VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드

요약: Azure 가상 컴퓨터에서 SAP NetWeaver를 실행하는 것을 고려하는 경우 시작하기 위한 문서입니다. 이 계획 및 구현 가이드를 사용하면 기존 또는 계획된 SAP NetWeaver 기반 시스템을 Azure 가상 컴퓨터 환경에 배포할 수 있는지 여부를 평가할 수 있습니다. 여러 SAP NetWeaver 배포 시나리오를 다루며 Azure에 고유한 SAP 구성을 포함합니다. 문서는 SAP/Azure 측면에서 하이브리드 SAP 환경을 실행하는 데 필요한 모든 구성 정보를 나열하고 설명합니다. IaaS에서 SAP NetWeaver 기반 시스템의 고가용성을 보장하기 위해 수행할 수 있는 측정값에 대해서도 다룹니다.

업데이트: 2016년 3월

[이 가이드는 여기서 확인할 수 있습니다.][planning-guide]

## <a name="6aadadd2-76b5-46d8-8713-e8d63630e955"></a>배포
제목: Linux VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드

요약: 이 문서에서는 Azure의 가상 컴퓨터에 SAP NetWeaver 소프트웨어를 배포하기 위한 절차 지침을 제공합니다. 이 문서에서는 SAP용 Azure 모니터링 확장에 대한 권장 문제 해결 방법을 비롯한 SAP용 Azure 모니터링 확장 사용을 중점적으로 세 가지 특정 배포 시나리오에 중점을 둡니다. 이 문서에서는 계획 및 구현 가이드를 읽은 것을 가정합니다.

업데이트: 2016년 3월

[이 가이드는 여기서 확인할 수 있습니다.][deployment-guide]

## <a name="1343ffe1-8021-4ce6-a08d-3a1553a4db82"></a>DBMS 배포 가이드
제목: Linux VM(가상 컴퓨터)에서 SAP NetWeaver – DBMS 배포 가이드

요약: 이 문서에서는 SAP와 함께 실행해야 하는 DBMS 시스템에 대한 계획 및 구현 고려 사항을 살펴봅니다. 첫 번째 부분에서는 일반적인 고려 사항이 나열되고 표시됩니다. 문서의 다음 부분은 SAP에서 지원되는 Azure의 여러 DBMS 배포와 관련되어 있습니다. 제공되는 DBMS은 SQL Server, SAP ASE 및 Oracle입니다. 해당 특정 부분 고려 사항에서 설명된 DBMS와 함께 Azure에서 SAP를 실행 중인 경우에 대해 설명해야 합니다. Azure의 각 DMS에서 지원되는 백업 및 고가용성 방법과 같은 주제가 SAP 응용 프로그램과 함께 사용하기 위해 표시됩니다.

업데이트: 2016년 3월

[이 가이드는 여기서 확인할 수 있습니다.][dbms-guide]


