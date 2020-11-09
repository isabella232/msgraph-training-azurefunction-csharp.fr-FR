---
ms.openlocfilehash: 728138b5d298f9469a566defed6ee2e457896c0b
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822643"
---
<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez terminer l’implémentation de la fonction Azure `GetMyNewestMessage` et mettre à jour le client test pour appeler la fonction.

La fonction Azure utilise le [flux de la part de](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). L’ordre de base des événements dans ce flux est le suivant :

- L’application de test utilise un flux d’authentification interactif pour permettre à l’utilisateur de se connecter et d’accorder son consentement. Il renvoie un jeton dont l’étendue est limitée à la fonction Azure. Le jeton ne contient **pas** d’étendues Microsoft Graph.
- L’application de test appelle la fonction Azure, en envoyant son jeton d’accès dans l' `Authorization` en-tête.
- La fonction Azure valide le jeton, puis échange ce jeton pour un deuxième jeton d’accès qui contient les étendues de Microsoft Graph.
- La fonction Azure appelle Microsoft Graph pour le compte de l’utilisateur à l’aide du deuxième jeton d’accès.

> [!IMPORTANT]
> Pour éviter le stockage de l’ID et de la clé secrète de l’application dans la source, utilisez le [Gestionnaire de secret .net](https://docs.microsoft.com/aspnet/core/security/app-secrets) pour stocker ces valeurs. Le gestionnaire de secrets est destiné uniquement à des fins de développement, les applications de production doivent utiliser un gestionnaire de secret approuvé pour le stockage de secrets.

## <a name="add-authentication-to-the-single-page-application"></a>Ajouter l’authentification à l’application à une seule page

Commencez par ajouter l’authentification au SPA. Cela permet à l’application d’obtenir un jeton d’accès accordant l’accès pour appeler la fonction Azure. Étant donné qu’il s’agit d’un SPA, il utilisera le [flux de code d’autorisation avec PKCE](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

1. Créez un fichier dans le répertoire **TestClient** nommé **config.js** et ajoutez le code suivant.

    :::code language="javascript" source="../demo/TestClient/config.example.js" id="msalConfigSnippet":::

    Remplacez `YOUR_TEST_APP_APP_ID_HERE` par l’ID d’application que vous avez créé dans le portail Azure pour l’application de test de la **fonction Azure Graph**. Remplacez `YOUR_TENANT_ID_HERE` par la valeur d' **ID de répertoire (locataire)** que vous avez copiée à partir du portail Azure. Remplacez `YOUR_AZURE_FUNCTION_APP_ID_HERE` par l’ID de l’application pour la **fonction Azure Graph**.

    > [!IMPORTANT]
    > Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le **config.js** fichier du contrôle de code source afin d’éviter une fuite accidentelle des ID d’application et de l’ID de client.

1. Créez un fichier dans le répertoire **TestClient** nommé **auth.js** et ajoutez le code suivant.

    :::code language="javascript" source="../demo/TestClient/auth.js" id="signInSignOutSnippet":::

    Examinez ce que fait ce code.

    - Il Initialise une `PublicClientApplication` à l’aide des valeurs stockées dans **config.js**.
    - Il utilise `loginPopup` pour signer l’utilisateur dans, à l’aide de l’étendue d’autorisation pour la fonction Azure.
    - Il stocke le nom d’utilisateur de l’utilisateur dans la session.

    > [!IMPORTANT]
    > Dans la mesure où l’application utilise `loginPopup` , il se peut que vous deviez modifier le bloqueur de fenêtres publicitaires intempestives de votre navigateur pour autoriser les fenêtres publicitaires intempestives `http://localhost:8080` .

1. Actualisez la page, puis connectez-vous. La page doit être mise à jour avec le nom d’utilisateur, ce qui indique que la connexion a réussi.

> [!TIP]
> Vous pouvez analyser le jeton d’accès à l’adresse [https://jwt.ms](https://jwt.ms) et confirmer que la `aud` revendication est l’ID de l’application pour la fonction Azure et que la `scp` revendication contient l’étendue d’autorisation de la fonction Azure, et non Microsoft Graph.

## <a name="add-authentication-to-the-azure-function"></a>Ajouter l’authentification à la fonction Azure

Dans cette section, vous allez implémenter le flux de la part de dans la `GetMyNewestMessage` fonction Azure pour obtenir un jeton d’accès compatible avec Microsoft Graph.

1. Initialisez le magasin de secrets de développement .NET en ouvrant votre interface CLI dans le répertoire qui contient **GraphTutorial. csproj** et en exécutant la commande suivante.

    ```Shell
    dotnet user-secrets init
    ```

1. Ajoutez l’ID de votre application, votre code secret et votre ID de client au magasin secret à l’aide des commandes suivantes. Remplacez `YOUR_API_FUNCTION_APP_ID_HERE` par l’ID de l’application pour la **fonction Azure Graph**. Remplacez `YOUR_API_FUNCTION_APP_SECRET_HERE` par le secret d’application que vous avez créé dans le portail Azure pour la **fonction Azure Graph**. Remplacez `YOUR_TENANT_ID_HERE` par la valeur d' **ID de répertoire (locataire)** que vous avez copiée à partir du portail Azure.

    ```Shell
    dotnet user-secrets set apiFunctionId "YOUR_API_FUNCTION_APP_ID_HERE"
    dotnet user-secrets set apiFunctionSecret "YOUR_API_FUNCTION_APP_SECRET_HERE"
    dotnet user-secrets set tenantId "YOUR_TENANT_ID_HERE"
    ```

### <a name="process-the-incoming-bearer-token"></a>Traiter le jeton de porteur entrant

Dans cette section, vous allez implémenter une classe pour valider et traiter le jeton du porteur envoyé du SPA à la fonction Azure.

1. Créez un répertoire dans le répertoire **GraphTutorial** nommé **Authentication**.

1. Créez un fichier nommé **TokenValidationResult.cs** dans le dossier **./GraphTutorial/Authentication** et ajoutez le code suivant.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/TokenValidationResult.cs" id="TokenValidationResultSnippet":::

1. Créez un fichier nommé **TokenValidation.cs** dans le dossier **./GraphTutorial/Authentication** et ajoutez le code suivant.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/TokenValidation.cs" id="TokenValidationSnippet":::

Examinez ce que fait ce code.

- Elle garantit qu’il existe un jeton de porteur dans l' `Authorization` en-tête.
- Il vérifie la signature et l’émetteur à partir de la configuration OpenID publiée d’Azure.
- Il vérifie que l’audience ( `aud` revendication) correspond à l’ID de l’application de la fonction Azure.
- Il analyse le jeton et génère un ID de compte MSAL, qui sera nécessaire pour tirer parti de la mise en cache de jetons.

### <a name="create-an-on-behalf-of-authentication-provider"></a>Créer un fournisseur d’authentification de la part de

1. Créez un fichier dans le répertoire **d’authentification** nommé **OnBehalfOfAuthProvider.cs** et ajoutez le code suivant à ce fichier.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/OnBehalfOfAuthProvider.cs" id="AuthProviderSnippet":::

Prenez le temps de prendre en compte le rôle du code dans **OnBehalfOfAuthProvider.cs** .

- Dans la `GetAccessToken` fonction, il tente d’abord d’obtenir un jeton utilisateur à partir du cache de jetons à l’aide de `AcquireTokenSilent` . Si cette opération échoue, elle utilise le jeton de porteur envoyé par l’application de test à la fonction Azure pour générer une assertion utilisateur. Il utilise ensuite cette assertion utilisateur pour obtenir un jeton compatible graphique à l’aide de `AcquireTokenOnBehalfOf` .
- Elle implémente l' `Microsoft.Graph.IAuthenticationProvider` interface, ce qui permet de transmettre cette classe dans le constructeur du `GraphServiceClient` pour authentifier les demandes sortantes.

### <a name="implement-a-graph-client-service"></a>Implémenter un service client Graph

Dans cette section, vous allez implémenter un service qui peut être inscrit pour l' [injection de dépendance](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection). Le service est utilisé pour obtenir un client Graph authentifié.

1. Créez un répertoire dans le répertoire **GraphTutorial** nommé **services**.

1. Créez un fichier dans le répertoire **services** nommé **IGraphClientService.cs** et ajoutez le code suivant à ce fichier.

    :::code language="csharp" source="../demo/GraphTutorial/Services/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. Créez un fichier dans le répertoire **services** nommé **GraphClientService.cs** et ajoutez le code suivant à ce fichier.

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

1. Ajoutez les propriétés suivantes à la `GraphClientService` classe.

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="UserGraphClientMembers":::

1. Ajoutez les fonctions suivantes à la `GraphClientService` classe.

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="UserGraphClientFunctions":::

1. Ajoutez une implémentation d’espace réservé pour la `GetAppGraphClient` fonction. Vous allez l’implémenter dans les sections suivantes.

    ```csharp
    public GraphServiceClient GetAppGraphClient()
    {
        throw new System.NotImplementedException();
    }
    ```

    La `GetUserGraphClient` fonction prend les résultats de la validation du jeton et génère une authentification `GraphServiceClient` pour l’utilisateur.

1. Créez un fichier dans le répertoire **GraphTutorial** nommé **Startup.cs** et ajoutez le code suivant à ce fichier.

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="StartupSnippet":::

    Ce code active l' [injection de dépendance](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) dans vos fonctions Azure, exposant l' `IConfiguration` objet et le `GraphClientService` service.

### <a name="implement-getmynewestmessage-function"></a>Implémenter la fonction GetMyNewestMessage

1. Ouvrez **./GraphTutorial/GetMyNewestMessage.cs** et remplacez l’intégralité de son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GetMyNewestMessage.cs" id="GetMyNewestMessageSnippet":::

#### <a name="review-the-code-in-getmynewestmessagecs"></a>Examinez le code dans GetMyNewestMessage.cs

Prenez le temps de prendre en compte le rôle du code dans **GetMyNewestMessage.cs** .

- Dans le constructeur, il enregistre l' `IConfiguration` objet passé via l’injection de dépendance.
- Dans la `Run` fonction, il effectue les opérations suivantes :
  - Valide les valeurs de configuration requises présentes dans l' `IConfiguration` objet.
  - Valide le jeton du porteur et renvoie un `401` code d’État si le jeton n’est pas valide.
  - Obtient un client Graph de l' `GraphClientService` utilisateur qui a effectué cette demande.
  - Utilise le kit de développement logiciel (SDK) Microsoft Graph pour obtenir le dernier message à partir de la boîte de réception de l’utilisateur et le renvoie en tant que corps JSON dans la réponse.

## <a name="call-the-azure-function-from-the-test-app"></a>Appeler la fonction Azure à partir de l’application de test

1. Ouvrez **auth.js** et ajoutez la fonction suivante pour obtenir un jeton d’accès.

    :::code language="javascript" source="../demo/TestClient/auth.js" id="getTokenSnippet":::

    Examinez ce que fait ce code.

    - Il tente tout d’abord d’obtenir un jeton d’accès en mode silencieux, sans intervention de l’utilisateur. Étant donné que l’utilisateur doit déjà être connecté, MSAL doit avoir des jetons pour l’utilisateur dans son cache.
    - Si cela échoue avec une erreur indiquant que l’utilisateur doit interagir, il tente d’obtenir un jeton de façon interactive.

1. Créez un fichier dans le répertoire **TestClient** nommé **azurefunctions.js** et ajoutez le code suivant.

    :::code language="javascript" source="../demo/TestClient/azurefunctions.js" id="getLatestMessageSnippet":::

1. Remplacez le répertoire actuel de votre interface CLI par le répertoire **./GraphTutorial** et exécutez la commande suivante pour démarrer la fonction Azure localement.

    ```Shell
    func start
    ```

1. Si vous n’avez pas déjà servi le SPA, ouvrez une deuxième fenêtre CLI et changez le répertoire actif en répertoire **./TestClient** . Exécutez la commande suivante pour exécuter l’application de test.

    ```Shell
    dotnet serve -h "Cache-Control: no-cache, no-store, must-revalidate"
    ```

1. Ouvrez votre navigateur et accédez à `http://localhost:8080`. Connectez-vous et sélectionnez le dernier élément de navigation de **message** . L’application affiche des informations sur le dernier message dans la boîte de réception de l’utilisateur.
