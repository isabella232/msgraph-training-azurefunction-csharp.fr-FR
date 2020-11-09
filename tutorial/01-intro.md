---
ms.openlocfilehash: 1e4267a5d8bdfe02dbc0170122113c35df822659
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822614"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ee2bc-101">Ce didacticiel vous apprend à créer une fonction Azure qui utilise l’API Microsoft Graph pour récupérer des informations de calendrier pour un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ee2bc-101">This tutorial teaches you how to build an Azure Function that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="ee2bc-102">Si vous préférez télécharger simplement le didacticiel terminé, vous pouvez télécharger ou cloner le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).</span><span class="sxs-lookup"><span data-stu-id="ee2bc-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).</span></span> <span data-ttu-id="ee2bc-103">Consultez le fichier Lisez-moi dans le dossier **Demo** pour obtenir des instructions sur la configuration de l’application avec un ID d’application et une clé secrète.</span><span class="sxs-lookup"><span data-stu-id="ee2bc-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ee2bc-104">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="ee2bc-104">Prerequisites</span></span>

<span data-ttu-id="ee2bc-105">Avant de commencer ce didacticiel, les outils suivants doivent être installés sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="ee2bc-105">Before you start this tutorial, you should have the following tools installed on your development machine.</span></span>

- [<span data-ttu-id="ee2bc-106">Kit de développement logiciel .NET Core</span><span class="sxs-lookup"><span data-stu-id="ee2bc-106">.NET Core SDK</span></span>](https://dotnet.microsoft.com/download)
- [<span data-ttu-id="ee2bc-107">Outils principaux des fonctions Azure</span><span class="sxs-lookup"><span data-stu-id="ee2bc-107">Azure Functions Core Tools</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-run-local)
- [<span data-ttu-id="ee2bc-108">Interface de commande de commande Azure</span><span class="sxs-lookup"><span data-stu-id="ee2bc-108">Azure CLI</span></span>](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [<span data-ttu-id="ee2bc-109">ngrok</span><span class="sxs-lookup"><span data-stu-id="ee2bc-109">ngrok</span></span>](https://ngrok.com/)

<span data-ttu-id="ee2bc-110">Vous devez également disposer d’un compte professionnel ou scolaire Microsoft, avec accès à un compte d’administrateur général au sein de la même organisation.</span><span class="sxs-lookup"><span data-stu-id="ee2bc-110">You should also have a Microsoft work or school account, with access to a global administrator account in the same organization.</span></span> <span data-ttu-id="ee2bc-111">Si vous n’avez pas de compte Microsoft, vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.</span><span class="sxs-lookup"><span data-stu-id="ee2bc-111">If you don't have a Microsoft account, you can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="ee2bc-112">Ce didacticiel a été écrit avec les versions suivantes des outils ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="ee2bc-112">This tutorial was written with the following versions of the above tools.</span></span> <span data-ttu-id="ee2bc-113">Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.</span><span class="sxs-lookup"><span data-stu-id="ee2bc-113">The steps in this guide may work with other versions, but that has not been tested.</span></span>
>
> - <span data-ttu-id="ee2bc-114">Kit de développement logiciel .NET 3.1.301</span><span class="sxs-lookup"><span data-stu-id="ee2bc-114">.NET Core SDK 3.1.301</span></span>
> - <span data-ttu-id="ee2bc-115">Outils principaux des fonctions Azure 3.0.2630</span><span class="sxs-lookup"><span data-stu-id="ee2bc-115">Azure Functions Core Tools 3.0.2630</span></span>
> - <span data-ttu-id="ee2bc-116">2.8.0 CLI Azure</span><span class="sxs-lookup"><span data-stu-id="ee2bc-116">Azure CLI 2.8.0</span></span>
> - <span data-ttu-id="ee2bc-117">ngrok 2.3.35</span><span class="sxs-lookup"><span data-stu-id="ee2bc-117">ngrok 2.3.35</span></span>

## <a name="feedback"></a><span data-ttu-id="ee2bc-118">Commentaires</span><span class="sxs-lookup"><span data-stu-id="ee2bc-118">Feedback</span></span>

<span data-ttu-id="ee2bc-119">Veuillez fournir des commentaires sur ce didacticiel dans le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).</span><span class="sxs-lookup"><span data-stu-id="ee2bc-119">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).</span></span>
