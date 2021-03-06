<properties
	pageTitle="PowerApps에서 Azure Active Directory 도메인의 백 엔드 시스템에 연결하도록 API 구성 | Microsoft Azure"
	description="PowerApps에서 AAD 보호 백 엔드에 연결하도록 API 구성"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/02/2016"
   ms.author="guayan"/>

# API를 Azure Active Directory 도메인의 백 엔드 리소스에 연결하도록 구성

> [AZURE.IMPORTANT] 이 항목은 보관되고 곧 제거될 예정입니다. 새 [PowerApps](https://powerapps.microsoft.com)의 새로운 내용을 살펴보세요.
> 
> - PowerApps에 대해 자세히 알아보고 시작하려면 [PowerApps](https://powerapps.microsoft.com)로 이동합니다.  
> - PowerApps에서 사용자 지정 API에 대해 자세히 알아보려면 [사용자 지정 API란?](https://powerapps.microsoft.com/tutorials/register-custom-api/)으로 이동합니다. 

<!--Archived
As more users are creating domains on Azure Active Directory (AAD), backend resources are also being added to these AAD domains. You can create and configure APIs to connect to these backend resources. 

#### Prerequisites to get started

- Sign up for [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Create an [app service environment](powerapps-get-started-azure-portal.md).
- Install [Azure PowerShell][11] 1.0 Preview or above.
- Register an API in your [app service environment](powerapps-register-api-hosted-in-app-service.md).

## Step 1: Create an Active Directory application and give it permissions

To access the backend system on an AAD domain, create an AAD application, and give it the proper permissions to your existing backend (which is also an AAD application). Steps:

1. In the [Azure classic portal][13], go to your Azure Active Directory, open your tenant (or directory), and select the **applications** tab:  
![][14]
2. Select the **Add** button at the bottom. Then:  

	a) Choose **Add an application my organization is developing**.  
	b) Enter a name for your application and select **Web application and/or web API**.  
	c) In **Sign-on URL** and **App ID URI**, enter unique URLs within your AAD and URLs that make sense to your organization. For example, you can enter http://powerappssignon.contoso.com or http://powerappsappid.contoso.com.  We recommend using a URL within your organization's AAD domain. The URLs are used as identifiers and there is no requirement that they need to exist. No one is going to browse the URLs you enter. You can enter HTTP or HTTPS.  

3. In the newly created AAD application page, go to the **Configure** tab:  
![][15]
4. In the **keys** section, use the drop-down list to select a duration. Note that the key displays after you select **Save**:  
![][16]
5. In **single sign-on**, add ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` as a **reply URL**.
6. In **permissions to other applications**:  

	1. Select **Add application**. In the pop-up window, choose the AAD application securing your existing backend:  
	![][17]  

	2. Use the drop-down list to add the permissions:  
	![][18]

7. Select **Save** at the bottom. 
8. Copy the **client ID** and **key** and store them. The key isn't shown again after you close Azure portal. 

See [Integrating Applications with Azure Active Directory](../active-directory/active-directory-integrating-applications.md) to learn more about  AAD applications. 

## Step 2: Configure your API using Azure PowerShell

At this point, there isn't any Azure portal support to initialize the configuration needed for your API. To configure the API in the Azure portal, use the following Auzre PowerShell script: 

> [AZURE.TIP] To learn how to install, configure, and run Azure PowerShell, see [How to install and configure Azure PowerShell][11]. The following script works with Azure PowerShell 1.0 preview or above.

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**Notice** that the **token** connection parameter name is important. You can pick your own name as long as it's camel case. You'll use this name later in your backend code or API policy.

Next, go to [Azure portal][19], and go to the **General** settings blade of your API. You should see the additional configuration options:  
![][21]


## Try it out

Open an app in PowerApps. In **Available connections**, your new API is listed. When you select **Connect**, it displays an AAD sign-in window. Enter your organization's AAD account details and your connection is created.

Now when a runtime call is made from your app to the API using this connection, your backend receives the user's AAD token in the **x-ms-apim-tokens** HTTP header in the following [Base64 encoding][20] format:  

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**Notice** that the property name **token** matches the connection parameter name you use when configuring the setting.

Your backend code can then get the AAD token from the **AccessToken** property and use it, if needed. The app service environment automatically refreshes the token.

## Configure the API policy

Optionally, you can also use API policy to set the AAD token into the standard HTTP **Authorization** header. This way, if your backend code needs to use the AAD token, you can get it in a standard way rather than looking into a custom HTTP header and perform Base64 decoding. To do this, go to the Azure portal, go to the **Policy** blade of your API, and set the following policy:  

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

Looking at this policy, it basically lets you reference the values in the **x-ms-apim-tokens** header as a decoded JObject using a **tokens** variable. Then you can use the **set-header** policy to get the actual AAD token and set it to the **Authorization** header. This is the same policy used by [Azure API Management](https://azure.microsoft.com/services/api-management/). To learn more, see [Policies in Azure API Management](../api-management/api-management-howto-policies.md).

**Notice** that the property name **token** matches the connection parameter name you used when configuring the setting.

## Summary and next steps

In this topic, you've seen how to configure an API to connect (and authenticate) to a backend resource on an Azure Active Directory domain. Here are some related topics and resources for learning more about PowerApps.

- [Develop an API for PowerApps](powerapps-develop-api.md)
-->

<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_0504_2016-->