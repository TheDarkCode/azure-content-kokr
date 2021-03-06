<properties
	pageTitle="Azure Active Directory에 사용자 지정 도메인 이름 추가 | Microsoft Azure"
	description="Azure Active Directory에 회사의 도메인 이름을 추가하는 방법 및 도메인 이름을 확인하는 방법."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/18/2016"
	ms.author="curtand;jeffsta"/>

# Azure Active Directory에 사용자 지정 도메인 이름 추가

조직이 비즈니스를 수행하기 위해 사용하는 하나 이상의 도메인 이름이 있다면 조직의 사용자는 회사 도메인 이름을 사용하여 회사 네트워크에 로그인합니다. 이제 Azure Active Directory(Azure AD)를 사용 중이므로 Azure AD에도 회사 도메인 이름을 추가할 수 있습니다. 이렇게 하면 사용자에게 친숙한 ‘alice@contoso.com’과 같은 사용자 이름을 디렉터리에 할당할 수 있습니다. 프로세스는 간단합니다.

1. 디렉터리에 사용자 지정 도메인 이름 추가
2. 도메인 이름 등록 기관의 도메인 이름에 대한 DNS 항목 추가
3. Azure AD에서 사용자 지정 도메인 이름 확인

> [AZURE.NOTE] 회사 네트워크에서 AD FS(Active Directory Federation Services) 또는 다른 STS(보안 토큰 서비스)와 함께 사용할 사용자 지정 도메인 이름을 구성하려면 [Azure Active Directory로 페더레이션에 대한 도메인 추가 및 구성](active-directory-add-domain-federated.md)의 지침을 따릅니다. 회사 디렉터리에서 Azure AD에 사용자를 동기화하고 [암호 해시 동기화](active-directory-aadconnectsync-implement-password-synchronization.md)가 요구 사항을 충족하지 않는 경우에 유용합니다.

## 디렉터리에 사용자 지정 도메인 이름 추가

1. Azure AD 디렉터리의 전역 관리자인 사용자 계정으로 [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.

2. **Active Directory**에서 디렉터리를 열고 **도메인** 탭을 선택합니다.

3. 명령 모음에서 **추가**를 선택합니다. 사용자 지정 도메인의 이름을 입력합니다(예: ‘contoso.com’). .com, .net 또는 기타 최상위 확장명을 포함하고 "Single Sign-On"(페더레이션)에 대한 확인란의 선택을 취소한 상태로 두어야 합니다.

4. **추가**를 선택합니다.

5. 도메인 추가 마법사의 두 번째 페이지에서 Azure AD는 조직이 사용자 지정 도메인 이름을 소유하는지 확인하기 위해 사용하는 DNS 항목을 가져옵니다.

이제 도메인 이름을 추가했으므로 Azure AD에서 조직이 해당 도메인 이름을 소유하고 있는지 확인해야 합니다. Azure AD에서 이러한 확인을 수행하기 전에 도메인 이름으로 DNS 영역 파일에 포함된 DNS 항목을 추가해야 합니다. 이 작업은 도메인 이름을 위한 도메인 이름 등록 기관의 웹 사이트에서 수행됩니다.

## 도메인에 대한 도메인 이름 등록 기관에 DNS 항목을 추가합니다.

사용자 지정 도메인 이름을 Azure AD로 사용하는 다음 단계에서는 도메인에 대한 DNS 영역 파일을 업데이트합니다. 이를 통해 Azure AD에서는 조직이 사용자 지정 도메인 이름을 소유하는지를 확인합니다.

1.  도메인에 대한 도메인 이름 등록 기관에 로그인합니다. DNS 항목을 업데이트하기 위해 액세스할 수 없는 경우 엑세스 권한이 있는 사용자 또는 팀에게 2단계를 완료하고 완료될 때를 알려주도록 요청합니다.

2.  Azure AD에서 제공되는 DNS 항목을 추가하여 도메인에 대한 DNS 영역 파일을 업데이트합니다. 이러한 DNS 항목을 통해 Azure AD에서 도메인에 대한 소유권을 확인할 수 있도록 합니다. DNS 항목은 메일 라우팅이나 웹 호스팅 같은 어떠한 동작도 변경하지 않습니다.

DNS 항목을 추가하는 방법에 대한 도움말은 [널리 사용되는 DNS 등록 기관에서 DNS 항목을 추가하기 위한 지침](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)을 참고합니다.

## Azure AD에서 도메인 이름 확인

DNS 항목을 추가하고 나면, Azure AD에서 도메인 이름을 확인할 준비가 되었습니다.

**도메인 추가** 마법사가 여전히 열려있으면, 마법사의 세 번째 페이지에서 **확인**을 선택합니다. **확인**을 선택하면 Azure AD에서는 도메인에 대한 DNS 영역 파일에서 DNS 항목을 찾습니다. DNS 레코드가 전파된 후에 Azure AD에서는 도메인 이름을 확인할 수 있습니다. 이 전파는 보통 몇 초 밖에 걸리지 않지만 한 시간 이상이 걸릴 경우도 있습니다. 확인이 처음에 작동하지 않으면 나중에 다시 시도하세요.

**도메인 추가** 마법사가 아직 열려있지 않으면, [Azure 클래식 포털](https://manage.windowsazure.com/)에서 도메인을 확인할 수 있습니다.

1.  Azure AD 디렉터리의 전역 관리자인 사용자 계정으로 로그인합니다.

2.  디렉터리를 열고 **도메인** 탭을 선택합니다.

3.  명령 모음에서 **확인**을 확인하고 선택하려는 도메인 이름을 선택합니다.

4. 대화 상자에서 **확인**을 선택하여 확인을 완료합니다.

이제 [사용자 지정 도메인 이름을 포함하는 사용자 이름을 할당](active-directory-add-domain-add-users.md)할 수 있습니다.

## 문제 해결

사용자 지정 도메인 이름을 확인할 수 없는 경우 다음을 시도하세요. 가장 일반적인 것부터 시작하여, 가장 드문 것까지 알아보겠습니다.

1.	**한 시간을 대기합니다**. Azure AD가 도메인을 확인할 수 있기 전에 DNS 레코드를 전파해야 합니다. 이 작업은 한 시간 이상 걸릴 수 있습니다.

2.	**DNS 레코드를 입력하고 정확한지 확인합니다**. 도메인에 대한 도메인 이름 등록 기관의 웹 사이트에서 이 단계를 완료합니다. DNS 항목을 DNS 영역 파일에 나타나지 않는 경우 또는 Azure AD에서 제공한 DNS 항목과 정확하게 일치하지 않는 경우 Azure AD는 도메인 이름을 확인할 수 없습니다. 도메인 이름 등록 기관에서 도메인에 대한 DNS 레코드를 업데이트하기 위한 액세스 권한이 없는 경우 조직에서 이 액세스 권한이 있는 사람이나 팀과 DNS 항목을 공유하고 DNS 항목을 추가하도록 요청합니다.

3.	**Azure AD에서 도메인 이름을 다른 디렉터리에서 삭제합니다**. 단일 디렉터리에서 도메인 이름을 확인할 수 있습니다. 다른 디렉터리에서 이전에 도메인 이름을 확인한 경우 새 디렉터리에서 확인할 수 있게 되기 전에 삭제해야 합니다. 도메인 이름을 삭제하는 방법에 대해 자세히 알아보려면 [사용자 지정 도메인 이름 관리](active-directory-add-manage-domain-names.md)를 참고합니다.


## 사용자 지정 도메인 이름 더 추가하기

조직에서 사용자 지정 도메인 이름을 여러 개 사용하는 경우(예: ‘contoso.com’ 및 ‘contosobank.com’), 최대 900개의 도메인에 해당 이름을 추가할 수 있습니다. 이 문서의 동일한 단계를 사용하여 각 도메인 이름을 추가합니다.

## 다음 단계

-   [사용자 지정 도메인 이름을 포함하는 사용자 이름 할당](active-directory-add-domain-add-users.md)
-   [사용자 지정 도메인 이름 관리](active-directory-add-manage-domain-names.md)
-   [Azure AD에서 도메인 관리 개념 알아보기](active-directory-add-domain-concepts.md)
-   [사용자 로그인 시 회사의 브랜딩 표시](active-directory-add-company-branding.md)
-   [PowerShell을 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0720_2016-->