---
ms.openlocfilehash: fbd43b4708cd1aeaad93b70fcf151869bd9e543e
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822627"
---
<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous découvrirez les modifications nécessaires à l’exemple de fonction Azure pour préparer la [publication à une application de fonctions Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local#publish).

## <a name="update-code"></a>Mise à jour du code

La configuration est lue à partir du magasin secret d’utilisateur, qui s’applique uniquement à votre ordinateur de développement. Avant de publier sur Azure, vous devez modifier l’emplacement de stockage de votre configuration et mettre à jour le code dans **Startup.cs** en conséquence.

Les secrets d’application doivent être stockés dans un stockage sécurisé, tel qu’un [coffre-fort de clés Azure](https://docs.microsoft.com/azure/key-vault/general/overview).

## <a name="update-cors-setting-for-azure-function"></a>Mettre à jour le paramètre CORS pour la fonction Azure

Dans cet exemple, nous avons configuré CORS dans **local.settings.json** pour permettre à l’application de test d’appeler la fonction. Vous devrez configurer votre fonction publiée pour autoriser toutes les applications SPA qui l’appellera.

## <a name="update-app-registrations"></a>Mettre à jour les inscriptions des applications

La  `knownClientApplications` propriété dans le manifeste de l’enregistrement de la **fonction Azure de graphique** doit être mise à jour avec les ID d’application des applications qui appelleront la fonction Azure.

## <a name="recreate-existing-subscriptions"></a>Recréer des abonnements existants

Tous les abonnements créés à l’aide de l’URL de webhook sur votre ordinateur local ou ngrok doivent être recréés à l’aide de l’URL de production de la `Notify` fonction Azure.
