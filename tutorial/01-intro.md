---
ms.openlocfilehash: 1e4267a5d8bdfe02dbc0170122113c35df822659
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822614"
---
<!-- markdownlint-disable MD002 MD041 -->

Ce didacticiel vous apprend à créer une fonction Azure qui utilise l’API Microsoft Graph pour récupérer des informations de calendrier pour un utilisateur.

> [!TIP]
> Si vous préférez télécharger simplement le didacticiel terminé, vous pouvez télécharger ou cloner le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp). Consultez le fichier Lisez-moi dans le dossier **Demo** pour obtenir des instructions sur la configuration de l’application avec un ID d’application et une clé secrète.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, les outils suivants doivent être installés sur votre ordinateur de développement.

- [Kit de développement logiciel .NET Core](https://dotnet.microsoft.com/download)
- [Outils principaux des fonctions Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local)
- [Interface de commande de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [ngrok](https://ngrok.com/)

Vous devez également disposer d’un compte professionnel ou scolaire Microsoft, avec accès à un compte d’administrateur général au sein de la même organisation. Si vous n’avez pas de compte Microsoft, vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.

> [!NOTE]
> Ce didacticiel a été écrit avec les versions suivantes des outils ci-dessus. Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.
>
> - Kit de développement logiciel .NET 3.1.301
> - Outils principaux des fonctions Azure 3.0.2630
> - 2.8.0 CLI Azure
> - ngrok 2.3.35

## <a name="feedback"></a>Commentaires

Veuillez fournir des commentaires sur ce didacticiel dans le [référentiel GitHub](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).
