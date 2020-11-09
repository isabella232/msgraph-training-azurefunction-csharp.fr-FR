---
ms.openlocfilehash: 0c9c6703a54e50f576a171a4a161305c9b9ae6ff
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822639"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="75000-101">Dans cet exercice, vous allez créer trois nouvelles applications Azure AD à l’aide du centre d’administration Azure Active Directory :</span><span class="sxs-lookup"><span data-stu-id="75000-101">In this exercise you will create three new Azure AD applications using the Azure Active Directory admin center:</span></span>

- <span data-ttu-id="75000-102">Inscription d’application pour l’application à page unique afin qu’elle puisse se connecter aux utilisateurs et obtenir des jetons permettant à l’application d’appeler la fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="75000-102">An app registration for the single-page application so that it can sign in users and get tokens allowing the application to call the Azure Function.</span></span>
- <span data-ttu-id="75000-103">Inscription de l’application pour la fonction Azure qui lui permet d’utiliser le flux de la [part de](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) pour échanger le jeton envoyé par le spa pour un jeton qui lui permettra d’appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="75000-103">An app registration for the Azure Function that allows it to use the [on-behalf-of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) to exchange the token sent by the SPA for a token that will allow it to call Microsoft Graph.</span></span>
- <span data-ttu-id="75000-104">Inscription d’une application pour le webhook de fonction Azure qui lui permet d’utiliser le [flux d’informations d’identification du client](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) pour appeler Microsoft Graph sans utilisateur.</span><span class="sxs-lookup"><span data-stu-id="75000-104">An app registration for the Azure Function webhook that allows it to use the [client credential flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) to call Microsoft Graph without a user.</span></span>

> [!NOTE]
> <span data-ttu-id="75000-105">Cet exemple nécessite trois inscriptions d’application, car il implémente à la fois le flux de la part de et le flux d’informations d’identification du client.</span><span class="sxs-lookup"><span data-stu-id="75000-105">This example requires three app registrations because it is implementing both the on-behalf-of flow and the client credential flow.</span></span> <span data-ttu-id="75000-106">Si votre fonction Azure utilise uniquement l’un de ces flux, il vous suffit de créer les inscriptions d’application qui correspondent à ce flux.</span><span class="sxs-lookup"><span data-stu-id="75000-106">If your Azure Function only uses one of these flows, you would only need to create the app registrations that correspond to that flow.</span></span>

1. <span data-ttu-id="75000-107">Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous à l’aide d’un administrateur de l’organisation client Microsoft 365.</span><span class="sxs-lookup"><span data-stu-id="75000-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using an Microsoft 365 tenant organization admin.</span></span>

1. <span data-ttu-id="75000-108">Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="75000-108">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="75000-109">Une capture d’écran des inscriptions d’applications</span><span class="sxs-lookup"><span data-stu-id="75000-109">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

## <a name="register-an-app-for-the-single-page-application"></a><span data-ttu-id="75000-110">Inscrire une application pour l’application à page unique</span><span class="sxs-lookup"><span data-stu-id="75000-110">Register an app for the single-page application</span></span>

1. <span data-ttu-id="75000-111">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="75000-111">Select **New registration**.</span></span> <span data-ttu-id="75000-112">Sur la page **Inscrire une application** , définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="75000-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="75000-113">Définissez le **Nom** sur `Graph Azure Function Test App`.</span><span class="sxs-lookup"><span data-stu-id="75000-113">Set **Name** to `Graph Azure Function Test App`.</span></span>
    - <span data-ttu-id="75000-114">Définissez les types de comptes **pris en charge** sur les **comptes dans ce répertoire d’organisation uniquement**.</span><span class="sxs-lookup"><span data-stu-id="75000-114">Set **Supported account types** to **Accounts in this organizational directory only**.</span></span>
    - <span data-ttu-id="75000-115">Sous **URI de redirection** , définissez la liste déroulante sur **une application à page unique (Spa)** et définissez la valeur sur `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="75000-115">Under **Redirect URI** , change the dropdown to **Single-page application (SPA)** and set the value to `http://localhost:8080`.</span></span>

    ![Capture d’écran de la page Inscrire une application](./images/register-command-line-app.png)

1. <span data-ttu-id="75000-117">Sélectionner **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="75000-117">Select **Register**.</span></span> <span data-ttu-id="75000-118">Sur la page **application de test de fonction Azure Graph** , copiez les valeurs de l’ID d' **application (client)** et de l' **ID de répertoire (client)** , puis enregistrez-les dans les étapes ultérieures.</span><span class="sxs-lookup"><span data-stu-id="75000-118">On the **Graph Azure Function Test App** page, copy the values of the **Application (client) ID** and **Directory (tenant) ID** and save them, you will need them in the later steps.</span></span>

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](./images/aad-application-id.png)

## <a name="register-an-app-for-the-azure-function"></a><span data-ttu-id="75000-120">Inscrire une application pour la fonction Azure</span><span class="sxs-lookup"><span data-stu-id="75000-120">Register an app for the Azure Function</span></span>

1. <span data-ttu-id="75000-121">Revenez aux **inscriptions de l’application** et sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="75000-121">Return to **App Registrations** , and select **New registration**.</span></span> <span data-ttu-id="75000-122">Sur la page **Inscrire une application** , définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="75000-122">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="75000-123">Définissez le **Nom** sur `Graph Azure Function`.</span><span class="sxs-lookup"><span data-stu-id="75000-123">Set **Name** to `Graph Azure Function`.</span></span>
    - <span data-ttu-id="75000-124">Définissez les types de comptes **pris en charge** sur les **comptes dans ce répertoire d’organisation uniquement**.</span><span class="sxs-lookup"><span data-stu-id="75000-124">Set **Supported account types** to **Accounts in this organizational directory only**.</span></span>
    - <span data-ttu-id="75000-125">Laissez l' **URI de redirection** vide.</span><span class="sxs-lookup"><span data-stu-id="75000-125">Leave **Redirect URI** blank.</span></span>

1. <span data-ttu-id="75000-126">Sélectionnez **Inscrire**.</span><span class="sxs-lookup"><span data-stu-id="75000-126">Select **Register**.</span></span> <span data-ttu-id="75000-127">Sur la page de la **fonction Graph Azure** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="75000-127">On the **Graph Azure Function** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

1. <span data-ttu-id="75000-128">Sélectionnez **Certificats et secrets** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="75000-128">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="75000-129">Sélectionnez le bouton **Nouveau secret client**.</span><span class="sxs-lookup"><span data-stu-id="75000-129">Select the **New client secret** button.</span></span> <span data-ttu-id="75000-130">Entrez une valeur dans **Description** , sélectionnez une des options pour **Expire le** , puis sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="75000-130">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![Une capture d’écran de la boîte de dialogue Ajouter une clé secrète client](./images/aad-new-client-secret.png)

1. <span data-ttu-id="75000-132">Copiez la valeur due la clé secrète client avant de quitter cette page.</span><span class="sxs-lookup"><span data-stu-id="75000-132">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="75000-133">Vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="75000-133">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="75000-134">Ce secret client n’apparaîtra plus jamais, aussi veillez à le copier maintenant.</span><span class="sxs-lookup"><span data-stu-id="75000-134">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Une capture d’écran de la clé secrète client nouvellement ajoutée](./images/aad-copy-client-secret.png)

1. <span data-ttu-id="75000-136">Sélectionnez **autorisations d’API** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="75000-136">Select **API Permissions** under **Manage**.</span></span> <span data-ttu-id="75000-137">Choisissez **Ajouter une autorisation**.</span><span class="sxs-lookup"><span data-stu-id="75000-137">Choose **Add a permission**.</span></span>

1. <span data-ttu-id="75000-138">Sélectionnez **Microsoft Graph** , puis **autorisations déléguées**.</span><span class="sxs-lookup"><span data-stu-id="75000-138">Select **Microsoft Graph** , then **Delegated Permissions**.</span></span> <span data-ttu-id="75000-139">Ajoutez **mail. Read** et sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="75000-139">Add **Mail.Read** and select **Add permissions**.</span></span>

    ![Capture d’écran des autorisations configurées pour l’inscription de l’application de fonction Azure](./images/web-api-configured-permissions.png)

1. <span data-ttu-id="75000-141">Sélectionnez **exposer une API** sous **gérer** , puis choisissez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="75000-141">Select **Expose an API** under **Manage** , then choose **Add a scope**.</span></span>

1. <span data-ttu-id="75000-142">Acceptez l' **URI d’ID d’application** par défaut et choisissez **enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="75000-142">Accept the default **Application ID URI** and choose **Save and continue**.</span></span>

1. <span data-ttu-id="75000-143">Renseignez le formulaire **Ajouter un étendue** comme suit :</span><span class="sxs-lookup"><span data-stu-id="75000-143">Fill in the **Add a scope** form as follows:</span></span>

    - <span data-ttu-id="75000-144">**Nom de l’étendue :** Mail. Read</span><span class="sxs-lookup"><span data-stu-id="75000-144">**Scope name:** Mail.Read</span></span>
    - <span data-ttu-id="75000-145">**Qui peut consentir ?:** Administrateurs et utilisateurs</span><span class="sxs-lookup"><span data-stu-id="75000-145">**Who can consent?:** Admins and users</span></span>
    - <span data-ttu-id="75000-146">**Nom d’affichage du consentement de l’administrateur :** Lire les boîtes de réception de tous les utilisateurs</span><span class="sxs-lookup"><span data-stu-id="75000-146">**Admin consent display name:** Read all users' inboxes</span></span>
    - <span data-ttu-id="75000-147">**Description du consentement administratif :** Permet à l’application de lire la boîte de réception de tous les utilisateurs</span><span class="sxs-lookup"><span data-stu-id="75000-147">**Admin consent description:** Allows the app to read all users' inboxes</span></span>
    - <span data-ttu-id="75000-148">**Nom d’affichage du consentement de l’utilisateur :** Lire votre boîte de réception</span><span class="sxs-lookup"><span data-stu-id="75000-148">**User consent display name:** Read your inbox</span></span>
    - <span data-ttu-id="75000-149">**Description du consentement de l’utilisateur :** Permet à l’application de lire votre boîte de réception</span><span class="sxs-lookup"><span data-stu-id="75000-149">**User consent description:** Allows the app to read your inbox</span></span>
    - <span data-ttu-id="75000-150">**État :** Activé</span><span class="sxs-lookup"><span data-stu-id="75000-150">**State:** Enabled</span></span>

1. <span data-ttu-id="75000-151">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="75000-151">Select **Add scope**.</span></span>

1. <span data-ttu-id="75000-152">Copiez la nouvelle étendue, vous en aurez besoin plus tard.</span><span class="sxs-lookup"><span data-stu-id="75000-152">Copy the new scope, you'll need it in later steps.</span></span>

    ![Capture d’écran des étendues définies pour l’inscription de l’application de fonction Azure](./images/web-api-defined-scopes.png)

1. <span data-ttu-id="75000-154">Sélectionnez **manifeste** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="75000-154">Select **Manifest** under **Manage**.</span></span>

1. <span data-ttu-id="75000-155">Recherchez `knownClientApplications` dans le manifeste et remplacez sa valeur actuelle `[]` par `[TEST_APP_ID]` , où `TEST_APP_ID` est l’ID d’application de l’inscription de l’application de test de la **fonction Azure de graphique** .</span><span class="sxs-lookup"><span data-stu-id="75000-155">Locate `knownClientApplications` in the manifest, and replace it's current value of `[]` with `[TEST_APP_ID]`, where `TEST_APP_ID` is the application ID of the **Graph Azure Function Test App** app registration.</span></span> <span data-ttu-id="75000-156">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="75000-156">Select **Save**.</span></span>

> [!NOTE]
> <span data-ttu-id="75000-157">L’ajout de l’ID d’application de l’application de test à la `knownClientApplications` propriété dans le manifeste de la fonction Azure permet à l’application de test de déclencher un [flux de consentement combiné](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow#default-and-combined-consent).</span><span class="sxs-lookup"><span data-stu-id="75000-157">Adding the test application's app ID to the `knownClientApplications` property in the Azure Function's manifest allows the test application to trigger a [combined consent flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow#default-and-combined-consent).</span></span> <span data-ttu-id="75000-158">Cette opération est nécessaire au bon fonctionnement du flux de la part de.</span><span class="sxs-lookup"><span data-stu-id="75000-158">This is necessary for the on-behalf-of flow to work.</span></span>

## <a name="add-azure-function-scope-to-test-application-registration"></a><span data-ttu-id="75000-159">Ajouter une étendue de fonction Azure pour tester l’inscription de l’application</span><span class="sxs-lookup"><span data-stu-id="75000-159">Add Azure Function scope to test application registration</span></span>

1. <span data-ttu-id="75000-160">Revenez à la **fonction Azure de graphique test** de l’inscription de l’application, puis sélectionnez **autorisations d’API** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="75000-160">Return to the **Graph Azure Function Test App** registration, and select **API Permissions** under **Manage**.</span></span> <span data-ttu-id="75000-161">Sélectionnez **Ajouter une autorisation**.</span><span class="sxs-lookup"><span data-stu-id="75000-161">Select **Add a permission**.</span></span>

1. <span data-ttu-id="75000-162">Sélectionnez **mes API** , puis **charger plus**.</span><span class="sxs-lookup"><span data-stu-id="75000-162">Select **My APIs** , then select **Load more**.</span></span> <span data-ttu-id="75000-163">Sélectionnez **fonction Azure Graph**.</span><span class="sxs-lookup"><span data-stu-id="75000-163">Select **Graph Azure Function**.</span></span>

    ![Capture d’écran de la boîte de dialogue des autorisations d’API de demande](./images/test-app-add-permissions.png)

1. <span data-ttu-id="75000-165">Sélectionnez l’autorisation **mail. Read** , puis sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="75000-165">Select the **Mail.Read** permission, then select **Add permissions**.</span></span>

1. <span data-ttu-id="75000-166">Dans les **autorisations configurées** , supprimez l’autorisation **User. Read** sous **Microsoft Graph** en sélectionnant le **...** à droite de l’autorisation et en sélectionnant **Supprimer l’autorisation**.</span><span class="sxs-lookup"><span data-stu-id="75000-166">In the **Configured permissions** , remove the **User.Read** permission under **Microsoft Graph** by selecting the **...** to the right of the permission and selecting **Remove permission**.</span></span> <span data-ttu-id="75000-167">Sélectionnez **Oui, supprimer** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="75000-167">Select **Yes, remove** to confirm.</span></span>

    ![Capture d’écran des autorisations configurées pour l’inscription de l’application de test](./images/test-app-configured-permissions.png)

## <a name="register-an-app-for-the-azure-function-webhook"></a><span data-ttu-id="75000-169">Inscrire une application pour le webhook de fonction Azure</span><span class="sxs-lookup"><span data-stu-id="75000-169">Register an app for the Azure Function webhook</span></span>

1. <span data-ttu-id="75000-170">Revenez aux **inscriptions de l’application** et sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="75000-170">Return to **App Registrations** , and select **New registration**.</span></span> <span data-ttu-id="75000-171">Sur la page **Inscrire une application** , définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="75000-171">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="75000-172">Définissez le **Nom** sur `Graph Azure Function Webhook`.</span><span class="sxs-lookup"><span data-stu-id="75000-172">Set **Name** to `Graph Azure Function Webhook`.</span></span>
    - <span data-ttu-id="75000-173">Définissez les types de comptes **pris en charge** sur les **comptes dans ce répertoire d’organisation uniquement**.</span><span class="sxs-lookup"><span data-stu-id="75000-173">Set **Supported account types** to **Accounts in this organizational directory only**.</span></span>
    - <span data-ttu-id="75000-174">Laissez l' **URI de redirection** vide.</span><span class="sxs-lookup"><span data-stu-id="75000-174">Leave **Redirect URI** blank.</span></span>

1. <span data-ttu-id="75000-175">Sélectionnez **Inscrire**.</span><span class="sxs-lookup"><span data-stu-id="75000-175">Select **Register**.</span></span> <span data-ttu-id="75000-176">Sur la page **Graph de fonction Azure webhook** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="75000-176">On the **Graph Azure Function webhook** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

1. <span data-ttu-id="75000-177">Sélectionnez **Certificats et secrets** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="75000-177">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="75000-178">Sélectionnez le bouton **Nouveau secret client**.</span><span class="sxs-lookup"><span data-stu-id="75000-178">Select the **New client secret** button.</span></span> <span data-ttu-id="75000-179">Entrez une valeur dans **Description** , sélectionnez une des options pour **Expire le** , puis sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="75000-179">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="75000-180">Copiez la valeur du secret client avant de quitter cette page.</span><span class="sxs-lookup"><span data-stu-id="75000-180">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="75000-181">Vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="75000-181">You will need it in the next step.</span></span>

1. <span data-ttu-id="75000-182">Sélectionnez **autorisations d’API** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="75000-182">Select **API Permissions** under **Manage**.</span></span> <span data-ttu-id="75000-183">Choisissez **Ajouter une autorisation**.</span><span class="sxs-lookup"><span data-stu-id="75000-183">Choose **Add a permission**.</span></span>

1. <span data-ttu-id="75000-184">Sélectionnez **Microsoft Graph** , puis **autorisations d’application**.</span><span class="sxs-lookup"><span data-stu-id="75000-184">Select **Microsoft Graph** , then **Application Permissions**.</span></span> <span data-ttu-id="75000-185">Ajoutez **User. Read. All** et **mail. Read** , puis sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="75000-185">Add **User.Read.All** and **Mail.Read** , then select **Add permissions**.</span></span>

1. <span data-ttu-id="75000-186">Dans les **autorisations configurées** , supprimez l’autorisation **utilisateur délégué. lecture** sous **Microsoft Graph** en sélectionnant le **...** à droite de l’autorisation et en sélectionnant **Supprimer l’autorisation**.</span><span class="sxs-lookup"><span data-stu-id="75000-186">In the **Configured permissions** , remove the delegated **User.Read** permission under **Microsoft Graph** by selecting the **...** to the right of the permission and selecting **Remove permission**.</span></span> <span data-ttu-id="75000-187">Sélectionnez **Oui, supprimer** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="75000-187">Select **Yes, remove** to confirm.</span></span>

1. <span data-ttu-id="75000-188">Sélectionnez le bouton **accorder le consentement de l’administrateur pour...** , puis sélectionnez **Oui** pour accorder le consentement de l’administrateur pour les autorisations d’application configurées.</span><span class="sxs-lookup"><span data-stu-id="75000-188">Select the **Grant admin consent for...** button, then select **Yes** to grant admin consent for the configured application permissions.</span></span> <span data-ttu-id="75000-189">La colonne **État** du tableau **autorisations configurées** passe à **accordé pour...**.</span><span class="sxs-lookup"><span data-stu-id="75000-189">The **Status** column in the **Configured permissions** table changes to **Granted for ...**.</span></span>

    ![Capture d’écran des autorisations configurées pour le webhook avec le consentement de l’administrateur accordé](./images/webhook-configured-permissions.png)
