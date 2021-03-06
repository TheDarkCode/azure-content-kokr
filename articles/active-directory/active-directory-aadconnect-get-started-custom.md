<properties
	pageTitle="Azure AD Connect: 사용자 지정 설치 | Microsoft Azure"
	description="이 문서는 Azure AD Connect에 대한 사용자 지정 설치 옵션에 대해 자세히 설명합니다. Azure AD Connect를 통해 Active Directory를 설치하려면 다음 지침을 사용합니다."
	services="active-directory"
    keywords="Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/13/2016"
	ms.author="billmath;andkjell"/>

# Azure AD Connect의 사용자 지정 설치
설치에 더 많은 옵션이 필요한 경우 Azure AD Connect **사용자 지정 설정**을 사용합니다. 여러 포리스트가 있는 경우 또한 빠른 설치에서 다루지 않는 선택적 기능을 구성하려는 경우에 사용합니다. [**빠른 설치**](active-directory-aadconnect-get-started-express.md)가 배포 또는 토폴로지 옵션을 충족하지 않는 경우에 사용합니다.

Azure AD Connect 설치를 시작하기 전에 [Azure AD Connect를 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771)하고 [Azure AD Connect: 하드웨어 및 필수 구성 요소](active-directory-aadconnect-prerequisites.md)의 필수 구성 요소 단계를 완료하도록 합니다. 또한 [Azure AD Connect 계정 및 사용 권한](active-directory-aadconnect-accounts-permissions.md)에 설명된 대로 사용할 수 있는 계정이 있어야 합니다.

DirSync를 업그레이드하는 등 사용자 지정된 설정이 토폴로지와 일치하지 않는 경우 다른 시나리오는 [관련 설명서](#related-documentation)를 참조하세요.

## Azure AD Connect의 사용자 지정 설정 설치

### Express 설정
이 페이지에서 **사용자 지정**을 클릭하여 사용자 지정된 설정을 설치하기 시작합니다.

### 필요한 구성 요소 설치
동기화 서비스를 설치한 경우 선택적 구성 섹션을 선택하지 않은 채로 두고 Azure AD Connect가 모든 내용을 자동으로 설정하도록 할 수 있습니다. 즉, SQL Server 2012 Express LocalDB 인스턴스를 설정하고 적절한 그룹을 만들고 사용 권한을 할당합니다. 기본값을 변경하려면 다음 테이블을 사용하여 사용 가능한 선택적 구성 옵션을 숙지할 수 있습니다.

![필수 구성 요소](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

선택적 구성 | 설명
------------- | -------------
기존 SQL Server 사용 | SQL Server 이름 및 인스턴스 이름을 지정할 수 있습니다. 사용하려는 데이터베이스 서버가 이미 있는 경우 이 옵션을 선택합니다. SQL Server에서 찾아보기를 사용하도록 설정하지 않은 경우 **인스턴스 이름**에 인스턴스 이름, 쉼표 및 포트 번호를 차례로 입력합니다.
기존 서비스 계정 사용 | 기본적으로 Azure AD Connect에서는 사용할 동기화 서비스에 대한 로컬 서비스 계정을 만듭니다. 암호가 자동으로 생성되어 Azure AD Connect를 설치하는 사람이 암호를 알 수 없습니다. 원격 SQL Server를 사용하거나 인증이 필요한 프록시를 사용하는 경우 도메인에 서비스 계정이 필요하며 암호를 알고 있어야 합니다. 이러한 경우에 사용할 서비스 계정을 입력합니다. 설치를 실행하는 사용자가 SQL에서 SA이므로 서비스 계정에 대한 로그인을 만들 수 있도록 합니다. [Azure AD Connect 계정 및 사용 권한](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation)을 참조하세요.
사용자 지정 동기화 그룹 지정 | 기본적으로 Azure AD Connect에서는 동기화 서비스를 설치할 때 서버에 로컬 그룹 4개를 만듭니다. 이 그룹은 Administrators 그룹, Operators 그룹, Browse 그룹, Password Reset 그룹입니다. 여기서 사용자의 고유한 그룹을 지정할 수 있습니다. 그룹은 서버에서 로컬이어야 하며 도메인에서 찾을 수 없습니다.

### 사용자 로그인
필수 구성 요소를 설치한 후 사용자가 Single Sign-On 방법을 선택하라는 메시지가 표시됩니다. 다음 테이블에서 사용 가능한 옵션에 대한 간략한 설명을 제공합니다. 로그인 메서드에 대한 전체 설명은 [사용자 로그인](active-directory-aadconnect-user-signin.md)을 참조하세요.

![사용자 로그인](./media/active-directory-aadconnect-get-started-custom/usersignin.png)

SSO(Single Sign-On) 옵션 | 설명
------------- | -------------
암호 동기화 | 사용자는 자신의 온-프레미스 네트워크에서 사용하는 것과 동일한 암호를 사용하여 Office 365와 같은 Microsoft 클라우드 서비스에 로그인할 수 있습니다. 사용자 암호는 암호 해시로 Azure AD에 동기화되며 클라우드에서 인증이 이루어집니다. 자세한 내용은 [암호 동기화](active-directory-aadconnectsync-implement-password-synchronization.md)를 참조하세요.
AD FS로 페더레이션 | 사용자는 자신의 온-프레미스 네트워크에서 사용하는 것과 동일한 암호를 사용하여 Office 365와 같은 Microsoft 클라우드 서비스에 로그인할 수 있습니다. 사용자는 로그인하기 위해 자신의 온-프레미스 AD FS 인스턴스로 리디렉션되며 온-프레미스로 인증이 이루어집니다.
구성하지 않음 | 기능이 설치 및 구성되지 않았습니다. 이미 타사 페더레이션 서버 또는 다른 기존 솔루션이 있는 경우 이 옵션을 선택합니다.

### Azure에 연결
Azure AD에 연결 화면에서, 전역 관리자 계정 및 암호를 입력합니다. 이전 페이지에서 **AD FS로 페더레이션**을 선택한 경우 페더레이션을 사용하도록 설정하려는 도메인의 계정으로 로그인하지 마십시오. Azure AD 디렉터리와 함께 제공되는 기본 **onmicrosoft.com** 도메인에서 계정을 사용하는 것이 좋습니다.

이 계정은 Azure AD에서 서비스 계정을 만드는 데에만 사용되며 마법사를 완료한 후에는 사용되지 않습니다. ![사용자 로그인](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

전역 관리자 계정이 MFA를 사용하도록 설정된 경우 로그인 팝업에서 암호를 다시 제공하고 MFA 인증을 완료해야 합니다. 과제에서는 확인 코드 또는 전화 통화를 제공할 수 있습니다. ![사용자 로그인 MFA](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

또한 전역 관리자 계정은 [Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)를 사용하도록 설정할 수 있습니다.

오류가 발생하고 연결에 문제가 있는 경우 [연결 문제 해결](active-directory-aadconnect-troubleshoot-connectivity.md)을 참조하세요.

## 섹션 동기화의 페이지

### 디렉터리에 연결
Active Directory 도메인 서비스에 연결하려면, Azure AD Connect는 충분한 권한이 있는 계정의 자격 증명이 필요합니다. NetBios 또는 FQDN 형식으로 도메인 부분을 입력할 수 있습니다(예: FABRIKAM\\syncuser 또는 fabrikam.com\\syncuser). 기본 읽기 권한만 필요하기 때문에 이 계정은 일반 사용자 계정일 수 있습니다. 그러나 시나리오에 따라 더 많은 사용 권한이 할 수 있습니다. 자세한 내용은 [Azure AD Connect 계정 및 사용 권한](active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account) 참조

![연결 디렉터리](./media/active-directory-aadconnect-get-started-custom/connectdir.png)

### Azure AD 로그인 구성
이 페이지를 사용하면 온-프레미스 AD DS에 있는 UPN 도메인을 검토하고 이는 Azure AD에서 확인됩니다. 또한 이 페이지를 사용하면 userPrincipalName에 사용할 특성을 구성할 수 있습니다.

![확인되지 않은 도메인](./media/active-directory-aadconnect-get-started-custom/aadsigninconfig.png) **추가되지 않음** 및 **확인되지 않음**으로 표시된 모든 도메인을 검토합니다. 사용한 해당 도메인을 Azure AD에서 확인하도록 합니다. 도메인을 확인한 경우 새로 고침 기호를 클릭합니다. 자세한 내용은 [도메인 추가 및 확인](active-directory-add-domain.md)을 참조하세요.

**UserPrincipalName** - 특성 userPrincipalName은 사용자가 Azure AD 및 Office 365에 로그인할 때 사용하는 특성입니다. UPN-접미사로 알려진 사용된 도메인은 사용자가 동기화되기 전에 Azure AD에서 확인해야 합니다. Microsoft에서는 기본 특성 userPrincipalName을 유지하는 것을 권장합니다. 이 특성이 라우팅할 수 없고 확인할 수 없는 경우 다른 특성을 선택할 수 있습니다. 예를 들어 로그인 ID를 보관하는 특성으로 전자 메일을 선택할 수 있습니다. userPrincipalName 이외의 다른 특성을 사용하는 것을 **대체 ID**라고 합니다. 대체 ID 특성 값은 RFC822 표준을 따라야 합니다. 대체 ID는 암호 동기화 및 페더레이션과 함께 사용할 수 있습니다.

>[AZURE.WARNING]
대체 ID를 사용하면 모든 Office 365 워크로드 부하와 호환되지 않습니다. 자세한 내용은 [대체 로그인 ID 구성](https://technet.microsoft.com/library/dn659436.aspx)을 참조하세요.

### 도메인 및 OU 필터링
기본적으로 모든 도메인 및 OU가 동기화됩니다. Azure AD로 동기화하지 않으려는 일부 도메인 또는 OU가 있는 경우 이러한 도메인 및 OU의 선택을 취소할 수 있습니다. ![DomainOU 필터링](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png) 마법사의 이 페이지에서는 도메인 기반 필터링을 구성합니다. 자세한 내용은 [도메인 기반 필터링](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering)을 참조하세요.

일부 도메인은 방화벽 제한으로 인해 연결할 수 없을 수도 있습니다. 이러한 도메인은 기본적으로 선택 취소되며 경고가 표시됩니다. ![연결할 수 없는 도메인](./media/active-directory-aadconnect-get-started-custom/unreachable.png) 이 경고가 표시된 경우 이러한 도메인에 실제로 연결할 수 없는지 그리고 경고가 예상되는지 확인합니다.

### 사용자를 고유하게 식별
포리스트 기능 간에 일치를 사용하여 AD DS 포리스트의 사용자가 Azure AD에서 표현되는 방법을 정의할 수 있습니다. 사용자는 포리스트 전반에 걸쳐 한번만 표시할 수 있거나 활성화된 계정과 비활성화된 계정의 조합으로 이루어집니다. 사용자가 일부 포리스트 내에서 연락처로 표시될 수 있습니다.

![고유한](./media/active-directory-aadconnect-get-started-custom/unique.png)

설정 | 설명
------------- | -------------
[사용자는 모든 포리스트에 걸쳐 한번만 표시됩니다](active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | 모든 사용자가 Azure AD에 개별 개체로 만들어집니다. 개체는 메타 버스에 연결되지 않습니다.
[Mail 특성](active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | 메일 특성에 다른 포리스트의 동일한 값이 있는 경우 이 옵션은 사용자 및 연락처를 연결합니다. 연락처가 GALSync를 사용하여 생성 된 경우 이 옵션을 사용합니다.
[ObjectSID 및 msExchangeMasterAccountSID/ msRTCSIP-OriginatorSid](active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest) | 이 옵션은 계정 포리스트에서 활성화된 사용자를 리소스 포리스트에서 비활성화된 사용자와 조인합니다. Exchange의 경우 이 구성을 연결된 된 사서함이라고 합니다. 이 옵션은 Lync만 사용하며 Exchange는 리소스 포리스트에 없는 경우에도 사용할 수 있습니다.
sAMAccountName 및 MailNickName | 이 옵션은 사용자에 대한 로그인 ID를 찾을 수 있을 것으로 예상되는 특성에 조인합니다.
특정 특성 | 이 옵션을 사용하면 고유한 특성을 선택할 수 있습니다. **제한:** 메타버스에서 이미 찾을 수 있는 특성을 선택해야 합니다. 사용자 지정 특성(메타버스에 없는)을 선택하면 마법사를 완료할 수 없습니다.

**원본 앵커** - 특성 sourceAnchor는 사용자 개체의 수명 동안 변경할 수 없는 특성입니다. Azure AD에서 사용자와 온-프레미스 사용자를 연결하는 기본 키입니다. 특성을 변경할 수 없으므로, 좋은 특성을 사용해야 합니다. 좋은 후보는 objectGUID입니다. 사용자 계정이 포리스트/도메인 간에 이동하지 않은 한 이 특성이 변경되지 않습니다. 포리스트 간에 계정을 이동하는 다중 포리스트 환경에서 employeeID가 있는 특성과 같은 다른 특성이 사용되어야 합니다. 결혼을 하거나 할당이 변경될 때 바뀔 수 있는 특성을 피하십시오. @ 기호와 함께 특성을 사용할 수 없으므로 메일 및 userPrincipalName을 사용할 수 없습니다. 또한 이 특성은 대소문자를 구분하므로 포리스트 간에 개체를 이동하는 경우 대/소문자를 유지해야 합니다. 이진 특성은 Base64로 인코딩되지만 다른 특성 유형은 인코딩되지 않은 상태로 남아 있습니다. 페더레이션 시나리오 및 일부 Azure AD 인터페이스에서는 이 특성을 immutableID라고도 합니다. 원본 앵커에 대한 자세한 정보는 [디자인 개념](active-directory-aadconnect-design-concepts.md#sourceAnchor)에서 찾을 수 있습니다.

### 그룹에 따라 동기화 필터링
그룹 기능에 대해 필터링하면 파일럿을 위해 개체의 작은 하위 집합만 동기화할 수 있습니다. 이 기능을 사용하려면 온-프레미스 Active Directory에서 이 목적을 위한 그룹을 만듭니다. 그런 다음 Azure AD에 직접 구성원으로 동기화해야 하는 사용자와 그룹을 추가합니다. 나중에 사용자를 이 그룹에 추가하고 제거하여 Azure AD에 있어야 하는 개체의 목록을 유지할 수 있습니다. 동기화하려는 모든 개체는 그룹의 직접 구성원이어야 합니다. 사용자, 그룹, 연락처 및 컴퓨터/장치는 모두 직접 구성원이어야 합니다. 중첩된 그룹 구성원은 확인되지 않습니다. 그룹을 구성원으로 추가하는 경우 해당 그룹 자체만 추가되며 그룹의 구성원은 추가되지 않습니다.

![동기화 필터링](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING]
이 기능은 파일럿 배포만 지원하기 위한 것입니다. 본격적인 프로덕션 배포에 사용하지 마십시오.

본격적인 프로덕션 배포에서는 동기화할 모든 개체를 가진 단일 그룹을 유지하기 어렵습니다. 대신에 [구성 필터링](active-directory-aadconnectsync-configure-filtering.md)의 방법 중 하나를 사용해야 합니다.

### 선택적 기능
이 화면을 사용하면 특정 시나리오에 대 한 선택적 기능을 선택할 수 있습니다.

![선택적 기능](./media/active-directory-aadconnect-get-started-custom/optional.png)

>[AZURE.WARNING]
현재 디렉터리 동기화 또는 Azure AD Sync가 활성화되어 있는 경우 Azure AD Connect에서 쓰기 저장 기능을 활성화하지 마세요.

선택적 기능 | 설명
------------------- | -------------
Exchange 하이브리드 배포 | Exchange 하이브리드 배포 기능을 통해 온-프레미스 및 Office 365에서 모두 Exchange 사서함을 동시에 존재하게 할 수 있습니다. Azure AD Connect에서는 [특성](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback)의 특정 집합을 Azure AD에서 온-프레미스 디렉터리로 다시 동기화합니다.
Azure AD 앱 및 특성 필터링 | Azure AD 앱 및 특성 필터링을 사용하여 동기화된 특성 집합을 사용자 지정할 수 있습니다. 이 옵션은 마법사에 구성 페이지를 두 개 더 추가합니다. 자세한 내용은 [Azure AD 앱 및 특성 필터링](#azure-ad-app-and-attribute-filtering)을 참조하세요.
암호 동기화 | 페더레이션을 로그인 솔루션으로 선택한 경우 이 옵션을 사용하도록 설정할 수 있습니다. 암호 동기화는 백업 옵션으로 사용할 수 있습니다. 자세한 내용은 [암호 동기화](active-directory-aadconnectsync-implement-password-synchronization.md)를 참조하세요.
비밀번호 쓰기 저장 | 비밀번호 쓰기 저장을 사용하도록 설정하면 Azure AD에서 이루어지는 암호 변경 사항이 온-프레미스 디렉터리에 다시 기록됩니다. 자세한 내용은 [암호 관리 시작](active-directory-passwords-getting-started.md)을 참조하세요.
그룹 쓰기 저장 | **Office 365 그룹** 기능을 사용하는 경우 이 그룹을 온-프레미스 Active Directory에 표시할 수 있습니다. 이 옵션은 Exchange가 온-프레미스 Active Directory에 있는 경우 사용할 수 있습니다. 자세한 내용은 [그룹 쓰기 저장](active-directory-aadconnect-feature-preview.md#group-writeback)을 참조하세요.
장치 쓰기 저장 | Azure AD의 장치 개체를 조건부 액세스 시나리오에 대한 온-프레미스 Active Directory에 쓰기 저장할 수 있습니다. 자세한 내용은 [Azure AD Connect에서 장치 쓰기 저장 사용](active-directory-aadconnect-feature-device-writeback.md)을 참조하세요.
디렉터리 확장 특성 동기화 | 디렉터리 확장 특성 동기화를 사용하도록 설정하면 지정된 특성이 Azure AD에 동기화됩니다. 자세한 내용은 [디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md)을 참조하세요.

### Azure AD 앱 및 특성 필터링
Azure AD에 동기화되는 특성을 제한 하려면 먼저 사용 중인 서비스를 선택합니다. 이 페이지에서 구성을 변경하는 경우 설치 마법사를 다시 실행하여 새 서비스를 명시적으로 선택해야 합니다.

![앱의 선택적 기능](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

이전 단계에서 선택한 서비스에 따라 이 페이지는 동기화되는 모든 특성을 표시합니다. 이 목록은 동기화된 모든 개체 형식의 조합입니다. 동기화하지 않아야 하는 몇 가지 특정 특성이 있는 경우 해당 특성을 선택 취소할 수 있습니다.

![특성의 선택적 기능](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

>[AZURE.WARNING]
특성을 제거하면 기능에 영향을 줄 수 있습니다. 모범 사례 및 권장 사항은 [동기화 되는 특성](active-directory-aadconnectsync-attributes-synchronized.md#attributes-to-synchronize)을 참조하세요.

### 디렉터리 확장 특성 동기화
Azure AD에서 조직이 추가한 사용자 지정 특성 또는 Active Directory의 다른 특성으로 스키마를 확장할 수 있습니다. 이 기능을 사용하려면, **선택적 기능** 페이지에서 **디렉터리 확장 특성 동기화**를 선택합니다. 이 페이지에서 동기화할 더 많은 특성을 선택할 수 있습니다.

![디렉터리 확장](./media/active-directory-aadconnect-get-started-custom/extension2.png)

자세한 내용은 [디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md)을 참조하세요.

## AD FS로 페더레이션 구성
Azure AD Connect를 사용하여 AD FS를 구성하는 것은 단 몇 번의 클릭으로 간단합니다. 구성하기 전에 다음 사항이 필요합니다.

- 원격 관리가 사용 가능한 페더레이션 서버용 Windows Server 2012 R2 서버
- 원격 관리가 사용 가능한 웹 응용 프로그램 프록시 서버용 Windows Server 2012 R2 서버
- 사용할 페더레이션 서비스 이름에 대한 SSL 인증서(예: sts.contoso.com)

### AD FS 구성 필수 조건
Azure AD Connect를 사용하여 AD FS 팜을 구성하려면, 원격 서버에서 WinRM이 활성화되도록 합니다. [테이블 3 - Azure AD Connect 및 페더레이션 서버/WAP](active-directory-aadconnect-ports.md#table-3---azure-ad-connect-and-federation-serverswap)에 나열된 포트 요구 사항을 살펴봅니다.

### 새 AD FS 팜을 만들거나 기존 AD FS 팜 사용
기존 AD FS 팜을 사용하거나 새 AD FS 팜을 만들도록 선택할 수 있습니다. 새로 만들기를 선택하는 경우 SSL 인증서를 제공해야 합니다. SSL 인증서가 암호로 보호된 경우 암호를 묻는 메시지가 나타납니다.

![AD FS 팜](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

기존 AD FS 팜을 사용하도록 선택한 경우 AD FS와 Azure AD 간의 트러스트 관계를 구성하는 화면으로 이동합니다.

### AD FS 서버 지정
AD FS를 설치하려는 서버를 입력합니다. 용량 계획 요구 사항에 따라 하나 이상의 서버를 추가할 수 있습니다. 이 구성을 수행하기 전에 모든 서버를 Active Directory에 조인합니다. Microsoft에서는 테스트 및 파일럿 배포를 위해 단일 AD FS 서버를 설치하는 것이 좋습니다. 그런 다음 초기 구성 후 Azure AD Connect를 다시 실행하여 규모 필요에 맞게 더 많은 서버를 추가 및 배포합니다.

>[AZURE.NOTE]
이 구성을 수행하기 전에 모든 서버가 AD 도메인에 조인되었는지 확인 하십시오.

![AD FS 서버](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### 웹 응용 프로그램 프록시 서버 지정
웹 응용 프로그램 프록시 서버로 사용할 서버를 입력합니다. 웹 응용 프로그램 프록시 서버는 DMZ(엑스트라넷 연결)에 배포되며 엑스트라넷에서 인증 요청을 지원합니다. 용량 계획 요구 사항에 따라 하나 이상의 서버를 추가할 수 있습니다. Microsoft에서는 테스트 및 파일럿 배포를 위해 단일 웹 응용 프로그램 프록시 서버를 설치하는 것이 좋습니다. 그런 다음 초기 구성 후 Azure AD Connect를 다시 실행하여 규모 필요에 맞게 더 많은 서버를 추가 및 배포합니다. 인트라넷에서 인증을 충족하기 위해 동일한 수의 프록시 서버를 두는 것이 좋습니다.

>[AZURE.NOTE]
<li> 사용할 계정이 AD FS 서버의 로컬 관리자가 아닌 경우 관리자 자격 증명에 대한 메시지가 표시됩니다.</li>
<li> 이 단계를 실행하기 전에 Azure AD Connect 서버와 웹 응용 프로그램 프록시 서버 간에 HTTP/HTTPS 연결이 되어 있는지 확인합니다.</li>
<li> 인증 요청이 진행될 수 있도록 웹 응용 프로그램 서버와 AD FS 서버 간에 HTTP/HTTPS 연결이 되어 있는지 확인합니다.</li>

![웹앱](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

웹 응용 프로그램 서버가 AD FS 서버에 보안 연결을 설정할 수 있도록 자격 증명을 입력하라는 메시지가 표시됩니다. 이러한 자격 증명에서 로컬 관리자는 AD FS 서버에 있어야 합니다.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### AD FS 서비스에 대한 서비스 계정 지정
AD FS 서비스가 Active Directory에서 사용자를 인증하고 사용자 정보를 검색하는데 도메인 서비스 계정이 필요합니다. 두 종류의 서비스 계정을 지원할 수 있습니다.

- **그룹 관리 서비스 계정** -Windows Server 2012에서 Active Directory 도메인 서비스에 도입되었습니다. 이 유형의 계정은 계정 암호를 정기적으로 업데이트할 필요 없이 AD FS와 같은 단일 계정에 서비스를 제공합니다. AD FS 서버가 속해 있는 도메인에 Windows Server 2012 도메인 컨트롤러가 이미 있는 경우 이 옵션을 사용합니다.
- **도메인 사용자 계정** - 이 유형의 계정을 사용하려면 암호를 입력하고 암호가 변경되거나 만료될 때 암호를 정기적으로 업데이트해야 합니다. AD FS 서버가 속해 있는 도메인에 Windows Server 2012 도메인 컨트롤러가 없는 경우에만 이 옵션을 사용합니다.

그룹 관리 서비스 계정 선택했는데 Active Directory에서 이 기능을 사용한 적이 없는 경우 엔터프라이즈 관리자 자격 증명에 대한 메시지가 표시됩니다. 이러한 자격 증명은 키 저장소를 시작하고 Active Directory에서 기능을 사용하도록 설정하는 데 사용됩니다.

![AD FS 서비스 계정](./media/active-directory-aadconnect-get-started-custom/adfs5.png)

### 페더레이션하려는 Azure AD 도메인을 선택합니다.
이 구성은 AD FS와 Azure AD 간의 페더레이션 관계를 설정하는데 사용됩니다. Azure AD에 보안 토큰을 발급하도록 AD FS를 구성하고 이 특정 AD FS 인스턴스에서 토큰을 신뢰하도록 Azure AD를 구성합니다. 이 페이지에서는 초기 설치에서 단일 도메인만 구성할 수만있습니다. 나중에 Azure AD Connect를 다시 실행하여 더 많은 도메인을 구성할 수 있습니다.

![Azure AD 도메인](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

### 페더레이션에 선택한 Azure AD 도메인 확인
페더레이션할 도메인을 선택하면 Azure AD Connect가 확인되지 않은 도메인을 확인하는 데 필요한 정보를 제공합니다. 이 정보를 사용하는 방법은 [도메인 추가 및 확인](active-directory-add-domain.md)을 참조하세요.

![Azure AD 도메인](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

>[AZURE.NOTE]
AD Connect는 구성 단계에서 도메인을 확인하려고 합니다. 필요한 DNS 기록을 추가하지 않고 계속 구성하는 경우 마법사가 구성을 완료할 수 없습니다.

## 페이지 구성 및 확인
이 페이지에서 구성이 이루어집니다.

>[AZURE.NOTE]
설치를 계속하기 전에 페더레이션을 구성한 경우 [페더레이션 서버에 대 한 이름 확인](active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers)을 구성했는지 확인합니다.

![구성할 준비 완료](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### 스테이징 모드
준비 모드와 병렬로 새 동기화 서버를 설정할 수 있습니다. 클라우드에서 하나의 디렉터리에 내보낸 하나의 동기화 서버에만 지원됩니다. 하지만 DirSync를 실행하는 서버와 같은 다른 서버에서 이동하려는 경우 스테이징 모드에서 Azure AD Connect를 사용할 수 있습니다. 사용하도록 설정한 경우 동기화 엔진이 평시와 같이 데이터를 가져오고 동기화하지만 아무 것도 Azure AD 또는 AD로 내보내지 않습니다. 준비 모드에서는 암호 동기화 및 비밀번호 쓰기 저장 기능을 사용할 수 없습니다.

![스테이징 모드](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

스테이징 모드에 있는 동안, 동기화 엔진에 필요한 변경 내용을 작성하고 내보낼 내용을 검토합니다. 구성마음에 드는 경우, 설치 마법사를 다시 실행하고 스테이징 모드를 사용하지 않도록 설정합니다. 이제 데이터를 이 서버에서 Azure AD로 내보냅니다. 한 서버만이 내보낼 수 있으므로 동시에 다른 서버를 사용하지 않도록 설정했는지 확인합니다.

자세한 내용은 [준비 모드](active-directory-aadconnectsync-operations.md#staging-mode)를 참조하세요.

### 페더레이션 구성 확인
확인 단추를 클릭하면 Azure AD Connect가 DNS 설정을 확인합니다.

![완료](./media/active-directory-aadconnect-get-started-custom/completed.png)

![Verify](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

또한 다음 확인 단계를 수행합니다.

- 인트라넷의 도메인 가입된 컴퓨터에서, 브라우저에서 로그인할 수 있는지 유효성 검사: https://myapps.microsoft.com에 연결하고 로그인된 계정으로 로그인을 확인합니다. 기본 제공 AD DS 관리자 계정은 동기화되지 않으며 확인을 위해 사용할 수 없습니다.
- 엑스트라넷에서, 장치에서 로그인 할 수 있는지 유효성을 검사합니다. 홈 컴퓨터 또는 모바일 장치에서 https://myapps.microsoft.com에 연결하고 자격 증명을 제공합니다.
- 리치 클라이언트 로그인 유효성을 검사합니다. https://testconnectivity.microsoft.com에 연결하고 **Office 365** 탭 및 **Office 365 Single Sign-on 테스트**를 선택합니다.

## 다음 단계
설치가 완료된 후 로그아웃하고 동기화 서비스 관리자 또는 동기화 규칙 편집기를 사용하기 전에 Windows에 다시 로그인합니다.

Azure AD Connect를 설치했으므로 [설치를 확인하고 라이선스를 할당](active-directory-aadconnect-whats-next.md)할 수 있습니다.

[실수로 인한 삭제 방지](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) 및 [Azure AD Connect Health](active-directory-aadconnect-health-sync.md)를 설치하여 사용할 수 있는 이러한 기능에 대해 자세히 알아봅니다.

[스케줄러 및 동기화를 트리거하는 방법](active-directory-aadconnectsync-feature-scheduler.md)에서 공통 항목에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

## 관련 설명서

항목 |  
--------- | ---------
Azure AD Connect 개요 | [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
Express 설정을 사용하여 설치 | [Azure AD Connect의 빠른 설치](active-directory-aadconnect-get-started-express.md)
DirSync에서 업그레이드 | [Azure AD Sync 도구(DirSync)에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md)
설치에 사용되는 계정 | [Azure AD Connect 계정 및 사용 권한에 대한 추가 정보](active-directory-aadconnect-accounts-permissions.md)

<!---HONumber=AcomDC_0914_2016-->