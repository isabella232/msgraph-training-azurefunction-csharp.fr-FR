---
ms.openlocfilehash: fbd43b4708cd1aeaad93b70fcf151869bd9e543e
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822627"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="52cb1-101">Dans cet exercice, vous découvrirez les modifications nécessaires à l’exemple de fonction Azure pour préparer la [publication à une application de fonctions Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local#publish).</span><span class="sxs-lookup"><span data-stu-id="52cb1-101">In this exercise you'll learn about what changes are needed to the sample Azure Function to prepare for [publishing to an Azure Functions app](https://docs.microsoft.com/azure/azure-functions/functions-run-local#publish).</span></span>

## <a name="update-code"></a><span data-ttu-id="52cb1-102">Mise à jour du code</span><span class="sxs-lookup"><span data-stu-id="52cb1-102">Update code</span></span>

<span data-ttu-id="52cb1-103">La configuration est lue à partir du magasin secret d’utilisateur, qui s’applique uniquement à votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="52cb1-103">Configuration is read from the user secret store, which only applies to your development machine.</span></span> <span data-ttu-id="52cb1-104">Avant de publier sur Azure, vous devez modifier l’emplacement de stockage de votre configuration et mettre à jour le code dans **Startup.cs** en conséquence.</span><span class="sxs-lookup"><span data-stu-id="52cb1-104">Before you publish to Azure, you'll need to change where you store your configuration, and update the code in **Startup.cs** accordingly.</span></span>

<span data-ttu-id="52cb1-105">Les secrets d’application doivent être stockés dans un stockage sécurisé, tel qu’un [coffre-fort de clés Azure](https://docs.microsoft.com/azure/key-vault/general/overview).</span><span class="sxs-lookup"><span data-stu-id="52cb1-105">Application secrets should be stored in secure storage, such as [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview).</span></span>

## <a name="update-cors-setting-for-azure-function"></a><span data-ttu-id="52cb1-106">Mettre à jour le paramètre CORS pour la fonction Azure</span><span class="sxs-lookup"><span data-stu-id="52cb1-106">Update CORS setting for Azure Function</span></span>

<span data-ttu-id="52cb1-107">Dans cet exemple, nous avons configuré CORS dans **local.settings.json** pour permettre à l’application de test d’appeler la fonction.</span><span class="sxs-lookup"><span data-stu-id="52cb1-107">In this sample we configured CORS in **local.settings.json** to allow the test application to call the function.</span></span> <span data-ttu-id="52cb1-108">Vous devrez configurer votre fonction publiée pour autoriser toutes les applications SPA qui l’appellera.</span><span class="sxs-lookup"><span data-stu-id="52cb1-108">You'll need to configure your published function to allow any SPA apps that will call it.</span></span>

## <a name="update-app-registrations"></a><span data-ttu-id="52cb1-109">Mettre à jour les inscriptions des applications</span><span class="sxs-lookup"><span data-stu-id="52cb1-109">Update app registrations</span></span>

<span data-ttu-id="52cb1-110">La  `knownClientApplications` propriété dans le manifeste de l’enregistrement de la **fonction Azure de graphique** doit être mise à jour avec les ID d’application des applications qui appelleront la fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="52cb1-110">The  `knownClientApplications` property in the manifest for the **Graph Azure Function** app registration will need to be updated with the application IDs of any apps that will be calling the Azure Function.</span></span>

## <a name="recreate-existing-subscriptions"></a><span data-ttu-id="52cb1-111">Recréer des abonnements existants</span><span class="sxs-lookup"><span data-stu-id="52cb1-111">Recreate existing subscriptions</span></span>

<span data-ttu-id="52cb1-112">Tous les abonnements créés à l’aide de l’URL de webhook sur votre ordinateur local ou ngrok doivent être recréés à l’aide de l’URL de production de la `Notify` fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="52cb1-112">Any subscriptions created using the webhook URL on your local machine or ngrok should be recreated using the production URL of the `Notify` Azure Function.</span></span>
