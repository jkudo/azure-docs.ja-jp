---
title: PIM で Azure AD ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールを割り当てる方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 09/17/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7cbb8c07b75509825795da45d8352140afd0864
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024230"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールを割り当てる

全体管理者は、Azure Active Directory (Azure AD) を使用して、**永続的**な Azure AD 管理者ロールの割り当てを行うことができます。 こうしたロールの割り当ては、[Azure portal](../users-groups-roles/directory-assign-admin-roles.md) または [PowerShell のコマンド](/powershell/module/azuread#directory_roles) を使用して作成できます。

永続的な管理ロールの割り当ては、特権ロール管理者が Azure AD Privileged Identity Management (PIM) サービスを使用して行うこともできます。 さらに、特権ロール管理者は、ユーザーを Azure AD 管理者ロールの**候補**にすることもできます。 管理者候補は必要なときにロールをアクティブ化できます。作業が完了すると、そのアクセス許可は期限切れになります。

## <a name="determine-your-version-of-pim"></a>PIM のバージョンを判断する

2019 年 11 月以降、Privileged Identity Management の Azure AD ロール部分は、Azure リソース ロールのエクスペリエンスと一致する新しいバージョンに更新されます。 これによって機能が追加され、[既存の API の変更](azure-ad-roles-features.md#api-changes)が行われます。 新しいバージョンのロールアウト中、この記事で実行する手順は、現在お使いになっている Privileged Identity Management のバージョンによって異なります。 このセクションの手順に従って、お使いになっている Privileged Identity Management のバージョンを確認してください。 Privileged Identity Management のバージョンを確認したら、この記事に記載されている手順のうち、そのバージョンに一致するものを選択することができます。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)のロールであるユーザーで [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。 概要ページの上部にバナーが表示されている場合は、この記事の **[新しいバージョン]** タブの指示に従ってください。 それ以外の場合は、 **[以前のバージョン]** タブの指示に従ってください。

    ![Azure AD ロールの新しいバージョン](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="new-versiontabnew"></a>[新しいバージョン](#tab/new)

## <a name="assign-a-role"></a>ロールの割り当て

ユーザーを Azure AD 管理者ロールの候補にするには、次の手順を実行します。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

    Privileged Identity Management を管理するためのアクセス権を別の管理者に付与する方法については、「[Privileged Identity Management を管理する他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)」を参照してください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[ロール]** を選択して、Azure AD のアクセス許可のロール一覧を表示します。

    ![Azure AD ロール](./media/pim-how-to-add-role-to-user/roles-list.png)

1. **[メンバーの追加]** を選択して **[新しい割り当て]** ページを開きます。

1. **[ロールを選択]** を選択して [ロールを選択] ページを開きます。

    ![[新しい割り当て] ウィンドウ](./media/pim-how-to-add-role-to-user/select-role.png)

1. 割り当てるロールを選択し、 **[選択]** をクリックします。

    **[メンバーまたはグループの選択]** ページが開きます。

1. ロールに割り当てるメンバーまたはグループを選択し、 **[選択]** を選択します。

    ![[メンバーまたはグループの選択] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    [メンバーシップ設定] ウィンドウが開きます。

1. **[割り当ての種類]** リストで **[対象]** または **[アクティブ]** を選択します。

    ![[メンバーシップ設定] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure リソース向けの Privileged Identity Management には、2 つの明確な割り当ての種類があります。

    - **[対象]** 割り当ての場合、このロールのメンバーは、ロールを使用するにはアクションを実行する必要があります。 要求されるアクションには、多要素認証 (MFA) チェックの実行、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。

    - **[アクティブ]** 割り当ての場合、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブ割り当てされたメンバーは、ロールによって提供される特権を常に所有します。

1. 割り当てを永続的 (永続的に対象または永続的に割り当て済み) にする必要がある場合は、 **[Permanently]\(永続的\)** チェック ボックスをオンにします。

    ロールの設定によっては、チェック ボックスが表示されない場合や、変更できない場合があります。

1. 特定の割り当て期間を指定するには、チェック ボックスの選択を解除して、開始または終了日時フィールドを変更します。

    ![メンバー シップ設定 - 日付と時刻](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. 終わったら、 **[Done]\(完了\)** を選択します。

    ![新しい割り当て - 追加](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. 新しいロールの割り当てを作成するには、 **[追加]** を選択します。 状態の通知が表示されます。

    ![新しい割り当て - 通知](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>既存のロールの割り当てを更新または削除する

既存のロールの割り当てを更新または削除するには、次の手順を実行します。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. サブスクリプションや管理グループなど、管理するリソースを選択します。

1. [管理] で **[ロール]** を選択して、Azure リソースのロール一覧を表示します。

1. 更新または削除するロールを選択します。

1. **[資格のあるロール]** タブまたは **[アクティブなロール]** タブでロールの割り当てを見つけます。

    ![ロールの割り当ての更新または削除](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. **[更新]** または **[削除]** を選択して、ロールの割り当てを更新または削除します。

    ロールの割り当てを延長する方法については、[Privileged Identity Management で Azure リソース ロールを延長または更新する方法](pim-resource-roles-renew-extend.md)に関するページを参照してください。

# <a name="previous-versiontabprevious"></a>[以前のバージョン](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>ユーザーをロールの候補にする

ユーザーを Azure AD 管理者ロールの候補にするには、次の手順を実行します。

1. **[ロール]** または **[メンバー]** をクリックします。

    ![Azure AD ロール](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **[メンバーの追加]** を選択して **[管理されるメンバーの追加]** を開きます。

1. **[Select a role]\(ロールを選択する\)** をクリックして、管理するロールをクリックし、 **[選択]** をクリックします。

    ![ロールを選択する](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **[メンバーの選択]** を選択してロールに割り当てるユーザーを選択し、 **[選択]** を選択します。

    ![ロールを選択する](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. **[管理されるメンバーの追加]** で **[OK]** を選択し、ロールにユーザーを追加します。

1. ロールの一覧で、割り当てたロールを選択してメンバーの一覧を表示します。

     ロールが割り当てられると、選択したユーザーが、ロールの**候補**としてメンバー リストに表示されます。

    ![ロールの候補となるユーザー](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. これで、そのユーザーがロールの対象となりました。ユーザーに、[Privileged Identity Management での自分の Azure AD ロールのアクティブ化](pim-how-to-activate-role.md)に関するページの手順に従ってロールをアクティブ化できることを知らせてください。

    管理者候補は、アクティブ化時に Azure Multi-Factor Authentication (MFA) への登録が求められます。 ユーザーが MFA に登録できない場合、または Microsoft アカウント (@outlook.com など) を使用している場合は、すべてのロールでこのユーザーを永続化する必要があります。

## <a name="make-a-role-assignment-permanent"></a>ロールの割り当てを永続化する

既定では、新しいユーザーは、あくまで Azure AD 管理者ロールの*候補*でしかありません。 ロールの割り当てを永続化する場合は、次の手順に従ってください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[メンバー]** を選択します。

    ![メンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 永続化する**候補**ロールを選択します。

1. **[その他]** を選択し、 **[永続化]** を選択します。

    ![ロールの割り当てを永続化する](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    ロールが**永続**として表示されています。

    ![永続化されたメンバーの一覧が変化](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>ユーザーをロールから削除する

ロールの割り当てからユーザーを削除することはできますが、必ず、永続的な全体管理者が少なくとも 1 人は常に存在するようにしてください。 どのユーザーに引き続きロールの割り当てが必要であるかわからない場合は、[ロールのアクセス レビューを開始](pim-how-to-start-security-review.md)してください。

特定のユーザーを Azure AD 管理者ロールから削除するには、次の手順に従います。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[メンバー]** を選択します。

    ![メンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 削除するロールの割り当てを選択します。

1. **[その他]** を選択し、 **[削除]** を選択します。

    ![ロールの削除](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 確認を求めるメッセージで **[はい]** を選択します。

    ![ロールの削除](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    ロールの割り当てが削除されました。

## <a name="authorization-error-when-assigning-roles"></a>ロールの割り当て時の承認エラー

サブスクリプション用に Privileged Identity Management を最近有効にしており、ユーザーを Azure AD 管理者ロールの対象にしようとすると承認エラーが表示される場合は、MS-PIM サービス プリンシパルがまだ適切なアクセス許可を持っていないためである可能性があります。 MS PIM サービス プリンシパルには、他のユーザーにロールを割り当てるための[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールが必要です。 MS PIM にユーザー アクセス管理者ロールが割り当てられるまで待つ代わりに、それを手動で割り当てることができます。

以下の手順に従って、MS PIM サービス プリンシパルにサブスクリプション用のユーザー アクセス管理者ロールを割り当てます。

1. Azure Portal にグローバル管理者としてサインインします。

1. **[すべてのサービス]** 、 **[サブスクリプション]** の順に選択します。

1. サブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** を選択します。

1. **[ロールの割り当て]** を選択すると、サブスクリプションをスコープとするロールの割り当ての現在の一覧が表示されます。

   ![サブスクリプションの [アクセス制御 (IAM)] ブレード](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. **MS PIM**サービス プリンシパルに**ユーザー アクセス管理者**ロールが割り当てられているかどうかを確認します。

1. そうなっていない場合、 **[ロールの割り当ての追加]** を選択して、 **[ロールの割り当ての追加]** ウィンドウを開きます。

1. **[ロール]** ドロップダウン リストで、 **[ユーザー アクセス管理者]** ロールを選択します。

1. **[選択]** リストで、 **[MS PIM]** サービス プリンシパルを見つけて選択します。

   ![[ロールの割り当ての追加] ウィンドウ - MS-PIM サービス プリンシパルにアクセス許可を追加する](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. **[保存]** を選択して、ロールを割り当てます。

   しばらくすると、MS PIM サービス プリンシパルに、サブスクリプション スコープでユーザー アクセス管理者ロールが割り当てられます。

   ![MS-PIM サービス プリンシパルに対するユーザー アクセス管理者ロールの割り当てを示すアクセス制御ページ](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD 管理者ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [Privileged Identity Management で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)