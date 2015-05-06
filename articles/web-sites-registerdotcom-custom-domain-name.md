﻿<properties 
	pageTitle="Azure の Web サイトの Register.com ドメイン名の構成" 
	description="Azure の Web サイトの Register.com ドメイン名を使用する方法を説明します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth"/>

#Azure Website のカスタム ドメイン名の構成 (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">ネットワーク ソリューション</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">モニカー</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Traffic Manager を使用する Web サイト</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[Register.com](https://www.register.com) から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて](#understanding-records)
-   [Web サイトの基本、共有、または標準モード用の構成](#bkmk_configsharedmode)
-   [カスタム ドメインの DNS レコードの追加](#bkmk_configurecname)
-   [Web サイトでのドメインの有効化](#enabledomain)

<h2><a name="understanding-records"></a>DNS レコードについて</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Web サイトの基本、共有、または標準モード用の構成</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>カスタム ドメインの DNS レコードの追加</h2>

カスタム ドメインを Azure Web サイトに関連付けるには、Register.com のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DNS ツールを見つけて利用します。

1. Register.com のアカウントにログオンし、右上にある **[Your Account]** を選択してドメインを表示したら、カスタム ドメイン名を選択します

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. **[Advanced Technical Settings]** ページが表示されるまでページを下にスクロールします。このセクションのリンクからドメインのレコードを管理できます。

	*  A レコードの場合、**[Edit IP Address Records]** リンクを使用します。
	* CNAME レコードの場合、**[Edit Domain Aliases Records]** リンクを使用します。

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. **[Edit]** をクリックすると、既存のレコードの変更や新しいレコードの追加に使用できるフォームが表示されます。このフォームは CNAME レコードと A レコードとで似ています。

	* CNAME レコードを追加するときは、**[.mydomainname.com]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[points to]** 値として、Azure Website の **.azurewebsites.net** ドメイン名を選択する必要があります。たとえば **contoso.azurwebsites.net** にします。**[Refers to Host Name]** は **[Select]** のままにします。Azure の Web サイトで使用する CNAME レコードを作成するとき、このフィールドは不要なためです。
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [AZURE.NOTE] また、A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
		> 
		> * **[Alias]** 値が **www**、**[Other host]** 値が **&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> または
		> 
		> * **[Alias]** 値が **awverify.www**、**[Other host]** 値が **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

	* A レコードを追加するときは、**[.mydomainname.com]** フィールドを、使用するサブドメイン (**www** など) に設定する必要があります。ルート ドメインを設定するには、このフィールドを空白のままにするか、**\** を使用してワイルドカード マッピングを作成します。**[points to]** フィールドを、Azure Website の IP アドレスに設定する必要があります。

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. レコードの追加または変更が完了したら、**[Continue]** をクリックして変更を確認します。**[Continue]** を再びクリックして変更を保存します。

<h2><a name="enabledomain"></a>Web サイトでのドメイン名の有効化</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]



<!--HONumber=42-->