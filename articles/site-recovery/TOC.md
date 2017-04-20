# 개요
## [Site Recovery란?](site-recovery-overview.md)
## [Site Recovery가 작동하는 방식](site-recovery-components.md)
## [Azure에 대한 Hyper-V 복제가 작동하는 방식](site-recovery-hyper-v-azure-architecture.md)
## [어떤 워크로드를 보호할 수 있나요?](site-recovery-workload.md)
## [Site Recovery 지원 매트릭스](site-recovery-support-matrix-to-azure.md)
## [FAQ](site-recovery-faq.md)
## [소개 영상 보기](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# 시작
## [VMware VM을 Azure에 복제](site-recovery-vmware-to-azure.md)
## [다중 테넌트 배포(CSP)에서 VMware VM을 Azure에 복제](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [Hyper-V VM을 Azure에 복제(VMM 있음)](site-recovery-vmm-to-azure.md)
## [Hyper-V VM을 Azure에 복제](site-recovery-hyper-v-site-to-azure.md)
## [VMware VM 및 물리적 서버를 보조 사이트에 복제](site-recovery-vmware-to-vmware.md)
## [Hyper-V VM을 보조 사이트에 복제(VMM 있음)](site-recovery-vmm-to-vmm.md)

# 방법
## 계획
### [배포 필수 조건](site-recovery-prereq.md)
### [네트워크 인프라 고려 사항](site-recovery-network-design.md)
### [용량을 계획하고 VMware 복제를 Azure에 확장](site-recovery-plan-capacity-vmware.md)
### [Azure로 VMware 복제를 위한 Deployment Planner](site-recovery-deployment-planner.md)
### [Hyper-V 복제를 위한 Capacity Planner](site-recovery-capacity-planner.md)

## 구성
### [원본 환경 설정](site-recovery-set-up-vmware-to-azure.md)
### [대상 환경 설정](site-recovery-prepare-target-vmware-to-azure.md)
### [복제 설정 구성](site-recovery-setup-replication-settings-vmware.md)
### [VMware 복제를 위해 모바일 서비스 배포](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [System Center Configuration Manager를 사용하여 모바일 서비스 배포](site-recovery-install-mobility-service-using-sccm.md)
#### [Azure Automation DSC를 사용하여 모바일 서비스 배포](site-recovery-automate-mobility-service-install.md)
### [복제 활성화](site-recovery-replicate-vmware-to-azure.md)
## 장애 조치 및 장애 복구
### [보호된 컴퓨터 장애 조치](site-recovery-failover.md)
### [복구 계획 설정](site-recovery-create-recovery-plans.md)
#### [Azure Runbook을 복구 계획에 추가](site-recovery-runbook-automation.md)
### [테스트 장애 조치 실행](site-recovery-test-failover-to-azure.md)
### [장애 조치 후 컴퓨터 다시 보호](site-recovery-how-to-reprotect.md)
### [Azure에서 장애 복구](site-recovery-failback-azure-to-vmware.md)

## 마이그레이션
### [Azure로 마이그레이션](site-recovery-migrate-to-azure.md)
### [Azure 지역 간 마이그레이션](site-recovery-migrate-azure-to-azure.md)
### [AWS Windows 인스턴스를 Azure로 마이그레이션](site-recovery-migrate-aws-to-azure.md)
## 워크로드
### [Active Directory 및 DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [Azure IoT Hub](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [기타 워크로드](site-recovery-workload.md#workload-summary)
## 복제 자동화
### [Azure에 Hyper-V 복제 자동화(VMM 없음)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Azure에 Hyper-V 복제 자동화(VMM 있음)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [보조 사이트에 Hyper-V 복제 자동화(VMM 있음)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## 관리
### [복제 설정 편집](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy.md)
### [Azure에서 프로세스 서버 관리](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [구성 서버 관리](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [확장 프로세스 서버 관리](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [vCenter 서버 관리](site-recovery-vmware-to-azure-manage-vCenter.md)
### [서버 제거 및 보호 사용 안 함](site-recovery-manage-registration-and-protection.md)
## [모니터링 및 문제 해결](site-recovery-monitoring-and-troubleshooting.md)

# 참조
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [PowerShell 클래식](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST (영문)](https://msdn.microsoft.com/en-us/library/mt750497)

# 관련 항목
## [Azure Automation](/azure/automation/)

# 리소스
## [학습 경로](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [블로그](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [가격 책정](https://azure.microsoft.com/pricing/details/site-recovery/)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=site-recovery)
