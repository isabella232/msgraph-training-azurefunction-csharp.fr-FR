---
ms.openlocfilehash: 728138b5d298f9469a566defed6ee2e457896c0b
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822643"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0be0f-101">Dans cet exercice, vous allez terminer l’implémentation de la fonction Azure `GetMyNewestMessage` et mettre à jour le client test pour appeler la fonction.</span><span class="sxs-lookup"><span data-stu-id="0be0f-101">In this exercise you will finish implementing the Azure Function `GetMyNewestMessage` and update the test client to call the function.</span></span>

<span data-ttu-id="0be0f-102">La fonction Azure utilise le [flux de la part de](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).</span><span class="sxs-lookup"><span data-stu-id="0be0f-102">The Azure Function uses the [on-behalf-of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).</span></span> <span data-ttu-id="0be0f-103">L’ordre de base des événements dans ce flux est le suivant :</span><span class="sxs-lookup"><span data-stu-id="0be0f-103">The basic order of events in this flow are:</span></span>

- <span data-ttu-id="0be0f-104">L’application de test utilise un flux d’authentification interactif pour permettre à l’utilisateur de se connecter et d’accorder son consentement.</span><span class="sxs-lookup"><span data-stu-id="0be0f-104">The test application uses an interactive auth flow to allow the user to sign in and grant consent.</span></span> <span data-ttu-id="0be0f-105">Il renvoie un jeton dont l’étendue est limitée à la fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="0be0f-105">It gets back a token that is scoped to the Azure Function.</span></span> <span data-ttu-id="0be0f-106">Le jeton ne contient **pas** d’étendues Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0be0f-106">The token does **NOT** contain any Microsoft Graph scopes.</span></span>
- <span data-ttu-id="0be0f-107">L’application de test appelle la fonction Azure, en envoyant son jeton d’accès dans l' `Authorization` en-tête.</span><span class="sxs-lookup"><span data-stu-id="0be0f-107">The test application invokes the Azure Function, sending its access token in the `Authorization` header.</span></span>
- <span data-ttu-id="0be0f-108">La fonction Azure valide le jeton, puis échange ce jeton pour un deuxième jeton d’accès qui contient les étendues de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0be0f-108">The Azure Function validates the token, then exchanges that token for a second access token that contains Microsoft Graph scopes.</span></span>
- <span data-ttu-id="0be0f-109">La fonction Azure appelle Microsoft Graph pour le compte de l’utilisateur à l’aide du deuxième jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="0be0f-109">The Azure Function calls Microsoft Graph on the user's behalf using the second access token.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0be0f-110">Pour éviter le stockage de l’ID et de la clé secrète de l’application dans la source, utilisez le [Gestionnaire de secret .net](https://docs.microsoft.com/aspnet/core/security/app-secrets) pour stocker ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="0be0f-110">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="0be0f-111">Le gestionnaire de secrets est destiné uniquement à des fins de développement, les applications de production doivent utiliser un gestionnaire de secret approuvé pour le stockage de secrets.</span><span class="sxs-lookup"><span data-stu-id="0be0f-111">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

## <a name="add-authentication-to-the-single-page-application"></a><span data-ttu-id="0be0f-112">Ajouter l’authentification à l’application à une seule page</span><span class="sxs-lookup"><span data-stu-id="0be0f-112">Add authentication to the single page application</span></span>

<span data-ttu-id="0be0f-113">Commencez par ajouter l’authentification au SPA.</span><span class="sxs-lookup"><span data-stu-id="0be0f-113">Start by adding authentication to the SPA.</span></span> <span data-ttu-id="0be0f-114">Cela permet à l’application d’obtenir un jeton d’accès accordant l’accès pour appeler la fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="0be0f-114">This will allow the application to get an access token granting access to call the Azure Function.</span></span> <span data-ttu-id="0be0f-115">Étant donné qu’il s’agit d’un SPA, il utilisera le [flux de code d’autorisation avec PKCE](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).</span><span class="sxs-lookup"><span data-stu-id="0be0f-115">Because this is a SPA, it will use the [authorization code flow with PKCE](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).</span></span>

1. <span data-ttu-id="0be0f-116">Créez un fichier dans le répertoire **TestClient** nommé **config.js** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0be0f-116">Create a new file in the **TestClient** directory named **config.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/TestClient/config.example.js" id="msalConfigSnippet":::

    <span data-ttu-id="0be0f-117">Remplacez `YOUR_TEST_APP_APP_ID_HERE` par l’ID d’application que vous avez créé dans le portail Azure pour l’application de test de la **fonction Azure Graph**.</span><span class="sxs-lookup"><span data-stu-id="0be0f-117">Replace `YOUR_TEST_APP_APP_ID_HERE` with the application ID you created in the Azure portal for the **Graph Azure Function Test App**.</span></span> <span data-ttu-id="0be0f-118">Remplacez `YOUR_TENANT_ID_HERE` par la valeur d' **ID de répertoire (locataire)** que vous avez copiée à partir du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="0be0f-118">Replace `YOUR_TENANT_ID_HERE` with the **Directory (tenant) ID** value you copied from the Azure portal.</span></span> <span data-ttu-id="0be0f-119">Remplacez `YOUR_AZURE_FUNCTION_APP_ID_HERE` par l’ID de l’application pour la **fonction Azure Graph**.</span><span class="sxs-lookup"><span data-stu-id="0be0f-119">Replace `YOUR_AZURE_FUNCTION_APP_ID_HERE` with the application ID for the **Graph Azure Function**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="0be0f-120">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le **config.js** fichier du contrôle de code source afin d’éviter une fuite accidentelle des ID d’application et de l’ID de client.</span><span class="sxs-lookup"><span data-stu-id="0be0f-120">If you're using source control such as git, now would be a good time to exclude the **config.js** file from source control to avoid inadvertently leaking your app IDs and tenant ID.</span></span>

1. <span data-ttu-id="0be0f-121">Créez un fichier dans le répertoire **TestClient** nommé **auth.js** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0be0f-121">Create a new file in the **TestClient** directory named **auth.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/TestClient/auth.js" id="signInSignOutSnippet":::

    <span data-ttu-id="0be0f-122">Examinez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="0be0f-122">Consider what this code does.</span></span>

    - <span data-ttu-id="0be0f-123">Il Initialise une `PublicClientApplication` à l’aide des valeurs stockées dans **config.js**.</span><span class="sxs-lookup"><span data-stu-id="0be0f-123">It initializes a `PublicClientApplication` using the values stored in **config.js**.</span></span>
    - <span data-ttu-id="0be0f-124">Il utilise `loginPopup` pour signer l’utilisateur dans, à l’aide de l’étendue d’autorisation pour la fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="0be0f-124">It uses `loginPopup` to sign the user in, using the permission scope for the Azure Function.</span></span>
    - <span data-ttu-id="0be0f-125">Il stocke le nom d’utilisateur de l’utilisateur dans la session.</span><span class="sxs-lookup"><span data-stu-id="0be0f-125">It stores the user's username in the session.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="0be0f-126">Dans la mesure où l’application utilise `loginPopup` , il se peut que vous deviez modifier le bloqueur de fenêtres publicitaires intempestives de votre navigateur pour autoriser les fenêtres publicitaires intempestives `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="0be0f-126">Since the app uses `loginPopup`, you may need to change your browser's pop-up blocker to allow pop-ups from `http://localhost:8080`.</span></span>

1. <span data-ttu-id="0be0f-127">Actualisez la page, puis connectez-vous.</span><span class="sxs-lookup"><span data-stu-id="0be0f-127">Refresh the page and sign in.</span></span> <span data-ttu-id="0be0f-128">La page doit être mise à jour avec le nom d’utilisateur, ce qui indique que la connexion a réussi.</span><span class="sxs-lookup"><span data-stu-id="0be0f-128">The page should update with the user name, indicating that the sign in was successful.</span></span>

> [!TIP]
> <span data-ttu-id="0be0f-129">Vous pouvez analyser le jeton d’accès à l’adresse [https://jwt.ms](https://jwt.ms) et confirmer que la `aud` revendication est l’ID de l’application pour la fonction Azure et que la `scp` revendication contient l’étendue d’autorisation de la fonction Azure, et non Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0be0f-129">You can parse the access token at [https://jwt.ms](https://jwt.ms) and confirm that the `aud` claim is the app ID for the Azure Function, and that the `scp` claim contains the Azure Function's permission scope, not Microsoft Graph.</span></span>

## <a name="add-authentication-to-the-azure-function"></a><span data-ttu-id="0be0f-130">Ajouter l’authentification à la fonction Azure</span><span class="sxs-lookup"><span data-stu-id="0be0f-130">Add authentication to the Azure Function</span></span>

<span data-ttu-id="0be0f-131">Dans cette section, vous allez implémenter le flux de la part de dans la `GetMyNewestMessage` fonction Azure pour obtenir un jeton d’accès compatible avec Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0be0f-131">In this section you'll implement the on-behalf-of flow in the `GetMyNewestMessage` Azure Function to get an access token compatible with Microsoft Graph.</span></span>

1. <span data-ttu-id="0be0f-132">Initialisez le magasin de secrets de développement .NET en ouvrant votre interface CLI dans le répertoire qui contient **GraphTutorial. csproj** et en exécutant la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="0be0f-132">Initialize the .NET development secret store by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="0be0f-133">Ajoutez l’ID de votre application, votre code secret et votre ID de client au magasin secret à l’aide des commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="0be0f-133">Add your application ID, secret, and tenant ID to the secret store using the following commands.</span></span> <span data-ttu-id="0be0f-134">Remplacez `YOUR_API_FUNCTION_APP_ID_HERE` par l’ID de l’application pour la **fonction Azure Graph**.</span><span class="sxs-lookup"><span data-stu-id="0be0f-134">Replace `YOUR_API_FUNCTION_APP_ID_HERE` with the application ID for the **Graph Azure Function**.</span></span> <span data-ttu-id="0be0f-135">Remplacez `YOUR_API_FUNCTION_APP_SECRET_HERE` par le secret d’application que vous avez créé dans le portail Azure pour la **fonction Azure Graph**.</span><span class="sxs-lookup"><span data-stu-id="0be0f-135">Replace `YOUR_API_FUNCTION_APP_SECRET_HERE` with the application secret you created in the Azure portal for the **Graph Azure Function**.</span></span> <span data-ttu-id="0be0f-136">Remplacez `YOUR_TENANT_ID_HERE` par la valeur d' **ID de répertoire (locataire)** que vous avez copiée à partir du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="0be0f-136">Replace `YOUR_TENANT_ID_HERE` with the **Directory (tenant) ID** value you copied from the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set apiFunctionId "YOUR_API_FUNCTION_APP_ID_HERE"
    dotnet user-secrets set apiFunctionSecret "YOUR_API_FUNCTION_APP_SECRET_HERE"
    dotnet user-secrets set tenantId "YOUR_TENANT_ID_HERE"
    ```

### <a name="process-the-incoming-bearer-token"></a><span data-ttu-id="0be0f-137">Traiter le jeton de porteur entrant</span><span class="sxs-lookup"><span data-stu-id="0be0f-137">Process the incoming bearer token</span></span>

<span data-ttu-id="0be0f-138">Dans cette section, vous allez implémenter une classe pour valider et traiter le jeton du porteur envoyé du SPA à la fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="0be0f-138">In this section you'll implement a class to validate and process the bearer token sent from the SPA to the Azure Function.</span></span>

1. <span data-ttu-id="0be0f-139">Créez un répertoire dans le répertoire **GraphTutorial** nommé **Authentication**.</span><span class="sxs-lookup"><span data-stu-id="0be0f-139">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>

1. <span data-ttu-id="0be0f-140">Créez un fichier nommé **TokenValidationResult.cs** dans le dossier **./GraphTutorial/Authentication** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0be0f-140">Create a new file named **TokenValidationResult.cs** in the **./GraphTutorial/Authentication** folder, and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/TokenValidationResult.cs" id="TokenValidationResultSnippet":::

1. <span data-ttu-id="0be0f-141">Créez un fichier nommé **TokenValidation.cs** dans le dossier **./GraphTutorial/Authentication** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0be0f-141">Create a new file named **TokenValidation.cs** in the **./GraphTutorial/Authentication** folder, and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/TokenValidation.cs" id="TokenValidationSnippet":::

<span data-ttu-id="0be0f-142">Examinez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="0be0f-142">Consider what this code does.</span></span>

- <span data-ttu-id="0be0f-143">Elle garantit qu’il existe un jeton de porteur dans l' `Authorization` en-tête.</span><span class="sxs-lookup"><span data-stu-id="0be0f-143">It ensure there is a bearer token in the `Authorization` header.</span></span>
- <span data-ttu-id="0be0f-144">Il vérifie la signature et l’émetteur à partir de la configuration OpenID publiée d’Azure.</span><span class="sxs-lookup"><span data-stu-id="0be0f-144">It verifies the signature and issuer from Azure's published OpenID configuration.</span></span>
- <span data-ttu-id="0be0f-145">Il vérifie que l’audience ( `aud` revendication) correspond à l’ID de l’application de la fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="0be0f-145">It verifies that the audience (`aud` claim) matches the Azure Function's application ID.</span></span>
- <span data-ttu-id="0be0f-146">Il analyse le jeton et génère un ID de compte MSAL, qui sera nécessaire pour tirer parti de la mise en cache de jetons.</span><span class="sxs-lookup"><span data-stu-id="0be0f-146">It parses the token and generates an MSAL account ID, which will be needed to take advantage of token caching.</span></span>

### <a name="create-an-on-behalf-of-authentication-provider"></a><span data-ttu-id="0be0f-147">Créer un fournisseur d’authentification de la part de</span><span class="sxs-lookup"><span data-stu-id="0be0f-147">Create an on-behalf-of authentication provider</span></span>

1. <span data-ttu-id="0be0f-148">Créez un fichier dans le répertoire **d’authentification** nommé **OnBehalfOfAuthProvider.cs** et ajoutez le code suivant à ce fichier.</span><span class="sxs-lookup"><span data-stu-id="0be0f-148">Create a new file in the **Authentication** directory named **OnBehalfOfAuthProvider.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/OnBehalfOfAuthProvider.cs" id="AuthProviderSnippet":::

<span data-ttu-id="0be0f-149">Prenez le temps de prendre en compte le rôle du code dans **OnBehalfOfAuthProvider.cs** .</span><span class="sxs-lookup"><span data-stu-id="0be0f-149">Take a moment to consider what the code in **OnBehalfOfAuthProvider.cs** does.</span></span>

- <span data-ttu-id="0be0f-150">Dans la `GetAccessToken` fonction, il tente d’abord d’obtenir un jeton utilisateur à partir du cache de jetons à l’aide de `AcquireTokenSilent` .</span><span class="sxs-lookup"><span data-stu-id="0be0f-150">In the `GetAccessToken` function, it first attempts to get a user token from the token cache using `AcquireTokenSilent`.</span></span> <span data-ttu-id="0be0f-151">Si cette opération échoue, elle utilise le jeton de porteur envoyé par l’application de test à la fonction Azure pour générer une assertion utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0be0f-151">If this fails, it uses the bearer token sent by the test app to the Azure Function to generate a user assertion.</span></span> <span data-ttu-id="0be0f-152">Il utilise ensuite cette assertion utilisateur pour obtenir un jeton compatible graphique à l’aide de `AcquireTokenOnBehalfOf` .</span><span class="sxs-lookup"><span data-stu-id="0be0f-152">It then uses that user assertion to get a Graph-compatible token using `AcquireTokenOnBehalfOf`.</span></span>
- <span data-ttu-id="0be0f-153">Elle implémente l' `Microsoft.Graph.IAuthenticationProvider` interface, ce qui permet de transmettre cette classe dans le constructeur du `GraphServiceClient` pour authentifier les demandes sortantes.</span><span class="sxs-lookup"><span data-stu-id="0be0f-153">It implements the `Microsoft.Graph.IAuthenticationProvider` interface, allowing this class to be passed in the constructor of the `GraphServiceClient` to authenticate outgoing requests.</span></span>

### <a name="implement-a-graph-client-service"></a><span data-ttu-id="0be0f-154">Implémenter un service client Graph</span><span class="sxs-lookup"><span data-stu-id="0be0f-154">Implement a Graph client service</span></span>

<span data-ttu-id="0be0f-155">Dans cette section, vous allez implémenter un service qui peut être inscrit pour l' [injection de dépendance](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0be0f-155">In this section you'll implement a service that can be registered for [dependency injection](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection).</span></span> <span data-ttu-id="0be0f-156">Le service est utilisé pour obtenir un client Graph authentifié.</span><span class="sxs-lookup"><span data-stu-id="0be0f-156">The service will be used to get an authenticated Graph client.</span></span>

1. <span data-ttu-id="0be0f-157">Créez un répertoire dans le répertoire **GraphTutorial** nommé **services**.</span><span class="sxs-lookup"><span data-stu-id="0be0f-157">Create a new directory in the **GraphTutorial** directory named **Services**.</span></span>

1. <span data-ttu-id="0be0f-158">Créez un fichier dans le répertoire **services** nommé **IGraphClientService.cs** et ajoutez le code suivant à ce fichier.</span><span class="sxs-lookup"><span data-stu-id="0be0f-158">Create a new file in the **Services** directory named **IGraphClientService.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Services/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. <span data-ttu-id="0be0f-159">Créez un fichier dans le répertoire **services** nommé **GraphClientService.cs** et ajoutez le code suivant à ce fichier.</span><span class="sxs-lookup"><span data-stu-id="0be0f-159">Create a new file in the **Services** directory named **GraphClientService.cs** and add the following code to that file.</span></span>

    ```csharp
    using GraphTutorial.Authentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;

    namespace GraphTutorial.Services
    {
        // Service added via dependency injection
        // Used to get an authenticated Graph client
        public class GraphClientService : IGraphClientService
        {
        }
    }
    ```

1. <span data-ttu-id="0be0f-160">Ajoutez les propriétés suivantes à la `GraphClientService` classe.</span><span class="sxs-lookup"><span data-stu-id="0be0f-160">Add the following properties to the `GraphClientService` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="UserGraphClientMembers":::

1. <span data-ttu-id="0be0f-161">Ajoutez les fonctions suivantes à la `GraphClientService` classe.</span><span class="sxs-lookup"><span data-stu-id="0be0f-161">Add the following functions to the `GraphClientService` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="UserGraphClientFunctions":::

1. <span data-ttu-id="0be0f-162">Ajoutez une implémentation d’espace réservé pour la `GetAppGraphClient` fonction.</span><span class="sxs-lookup"><span data-stu-id="0be0f-162">Add a placeholder implementation for the `GetAppGraphClient` function.</span></span> <span data-ttu-id="0be0f-163">Vous allez l’implémenter dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="0be0f-163">You will implement that in later sections.</span></span>

    ```csharp
    public GraphServiceClient GetAppGraphClient()
    {
        throw new System.NotImplementedException();
    }
    ```

    <span data-ttu-id="0be0f-164">La `GetUserGraphClient` fonction prend les résultats de la validation du jeton et génère une authentification `GraphServiceClient` pour l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0be0f-164">The `GetUserGraphClient` function takes the results of token validation and builds an authenticated `GraphServiceClient` for the user.</span></span>

1. <span data-ttu-id="0be0f-165">Créez un fichier dans le répertoire **GraphTutorial** nommé **Startup.cs** et ajoutez le code suivant à ce fichier.</span><span class="sxs-lookup"><span data-stu-id="0be0f-165">Create a new file in the **GraphTutorial** directory named **Startup.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="StartupSnippet":::

    <span data-ttu-id="0be0f-166">Ce code active l' [injection de dépendance](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) dans vos fonctions Azure, exposant l' `IConfiguration` objet et le `GraphClientService` service.</span><span class="sxs-lookup"><span data-stu-id="0be0f-166">This code will enable [dependency injection](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) in your Azure Functions, exposing the `IConfiguration` object and the `GraphClientService` service.</span></span>

### <a name="implement-getmynewestmessage-function"></a><span data-ttu-id="0be0f-167">Implémenter la fonction GetMyNewestMessage</span><span class="sxs-lookup"><span data-stu-id="0be0f-167">Implement GetMyNewestMessage function</span></span>

1. <span data-ttu-id="0be0f-168">Ouvrez **./GraphTutorial/GetMyNewestMessage.cs** et remplacez l’intégralité de son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="0be0f-168">Open **./GraphTutorial/GetMyNewestMessage.cs** and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GetMyNewestMessage.cs" id="GetMyNewestMessageSnippet":::

#### <a name="review-the-code-in-getmynewestmessagecs"></a><span data-ttu-id="0be0f-169">Examinez le code dans GetMyNewestMessage.cs</span><span class="sxs-lookup"><span data-stu-id="0be0f-169">Review the code in GetMyNewestMessage.cs</span></span>

<span data-ttu-id="0be0f-170">Prenez le temps de prendre en compte le rôle du code dans **GetMyNewestMessage.cs** .</span><span class="sxs-lookup"><span data-stu-id="0be0f-170">Take a moment to consider what the code in **GetMyNewestMessage.cs** does.</span></span>

- <span data-ttu-id="0be0f-171">Dans le constructeur, il enregistre l' `IConfiguration` objet passé via l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="0be0f-171">In the constructor, it saves the `IConfiguration` object passed in via dependency injection.</span></span>
- <span data-ttu-id="0be0f-172">Dans la `Run` fonction, il effectue les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="0be0f-172">In the `Run` function, it does the following:</span></span>
  - <span data-ttu-id="0be0f-173">Valide les valeurs de configuration requises présentes dans l' `IConfiguration` objet.</span><span class="sxs-lookup"><span data-stu-id="0be0f-173">Validates the required configuration values are present in the `IConfiguration` object.</span></span>
  - <span data-ttu-id="0be0f-174">Valide le jeton du porteur et renvoie un `401` code d’État si le jeton n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="0be0f-174">Validates the bearer token and returns a `401` status code if the token is invalid.</span></span>
  - <span data-ttu-id="0be0f-175">Obtient un client Graph de l' `GraphClientService` utilisateur qui a effectué cette demande.</span><span class="sxs-lookup"><span data-stu-id="0be0f-175">Gets a Graph client from the `GraphClientService` for the user that made this request.</span></span>
  - <span data-ttu-id="0be0f-176">Utilise le kit de développement logiciel (SDK) Microsoft Graph pour obtenir le dernier message à partir de la boîte de réception de l’utilisateur et le renvoie en tant que corps JSON dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="0be0f-176">Uses the Microsoft Graph SDK to get the newest message from the user's inbox and returns it as a JSON body in the response.</span></span>

## <a name="call-the-azure-function-from-the-test-app"></a><span data-ttu-id="0be0f-177">Appeler la fonction Azure à partir de l’application de test</span><span class="sxs-lookup"><span data-stu-id="0be0f-177">Call the Azure Function from the test app</span></span>

1. <span data-ttu-id="0be0f-178">Ouvrez **auth.js** et ajoutez la fonction suivante pour obtenir un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="0be0f-178">Open **auth.js** and add the following function to get an access token.</span></span>

    :::code language="javascript" source="../demo/TestClient/auth.js" id="getTokenSnippet":::

    <span data-ttu-id="0be0f-179">Examinez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="0be0f-179">Consider what this code does.</span></span>

    - <span data-ttu-id="0be0f-180">Il tente tout d’abord d’obtenir un jeton d’accès en mode silencieux, sans intervention de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0be0f-180">It first attempts to get an access token silently, without user interaction.</span></span> <span data-ttu-id="0be0f-181">Étant donné que l’utilisateur doit déjà être connecté, MSAL doit avoir des jetons pour l’utilisateur dans son cache.</span><span class="sxs-lookup"><span data-stu-id="0be0f-181">Since the user should already be signed in, MSAL should have tokens for the user in its cache.</span></span>
    - <span data-ttu-id="0be0f-182">Si cela échoue avec une erreur indiquant que l’utilisateur doit interagir, il tente d’obtenir un jeton de façon interactive.</span><span class="sxs-lookup"><span data-stu-id="0be0f-182">If that fails with an error that indicates the user needs to interact, it attempts to get a token interactively.</span></span>

1. <span data-ttu-id="0be0f-183">Créez un fichier dans le répertoire **TestClient** nommé **azurefunctions.js** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="0be0f-183">Create a new file in the **TestClient** directory named **azurefunctions.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/TestClient/azurefunctions.js" id="getLatestMessageSnippet":::

1. <span data-ttu-id="0be0f-184">Remplacez le répertoire actuel de votre interface CLI par le répertoire **./GraphTutorial** et exécutez la commande suivante pour démarrer la fonction Azure localement.</span><span class="sxs-lookup"><span data-stu-id="0be0f-184">Change the current directory in your CLI to the **./GraphTutorial** directory and run the following command to start the Azure Function locally.</span></span>

    ```Shell
    func start
    ```

1. <span data-ttu-id="0be0f-185">Si vous n’avez pas déjà servi le SPA, ouvrez une deuxième fenêtre CLI et changez le répertoire actif en répertoire **./TestClient** .</span><span class="sxs-lookup"><span data-stu-id="0be0f-185">If not already serving the SPA, open a second CLI window and change the current directory to the **./TestClient** directory.</span></span> <span data-ttu-id="0be0f-186">Exécutez la commande suivante pour exécuter l’application de test.</span><span class="sxs-lookup"><span data-stu-id="0be0f-186">Run the following command to run the test application.</span></span>

    ```Shell
    dotnet serve -h "Cache-Control: no-cache, no-store, must-revalidate"
    ```

1. <span data-ttu-id="0be0f-187">Ouvrez votre navigateur et accédez à `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="0be0f-187">Open your browser and navigate to `http://localhost:8080`.</span></span> <span data-ttu-id="0be0f-188">Connectez-vous et sélectionnez le dernier élément de navigation de **message** .</span><span class="sxs-lookup"><span data-stu-id="0be0f-188">Sign in and select the **Latest Message** navigation item.</span></span> <span data-ttu-id="0be0f-189">L’application affiche des informations sur le dernier message dans la boîte de réception de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0be0f-189">The app displays information about the newest message in the user's inbox.</span></span>
