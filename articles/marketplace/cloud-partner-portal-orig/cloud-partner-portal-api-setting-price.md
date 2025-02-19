---
title: Tarification des offres de machine virtuelle| Place de marché Azure
description: Décrit les trois méthodes disponibles la tarification des offres de machine virtuelle.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: e398b43e679fb6420c2256e77d34359ae537ac1c
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868740"
---
<a name="pricing-for-virtual-machine-offers"></a>Tarification des offres de machine virtuelle
==================================

Trois méthodes sont disponibles la tarification des offres de machine virtuelle : la tarification de cœur personnalisée, la tarification par cœur et la tarification par feuille de calcul.


<a name="customized-core-pricing"></a>Tarification de cœur personnalisée
-----------------------

La tarification est spécifique à chaque combinaison de région et de cœur. Chacune des régions de la liste doit être spécifiée dans la section **virtualMachinePricing**/**regionPrices** de la définition.  Dans votre requête, utilisez les codes devise correspondant à chaque [région](#regions).  L’exemple suivant illustre ces exigences :

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Tarification par cœur
----------------

Dans ce cas, les éditeurs spécifient un prix en dollars américains pour leur référence SKU, et tous les autres prix sont générés automatiquement. Le prix par cœur est spécifié dans le paramètre **single** de la requête.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Tarification par feuille de calcul
-------------------

L'éditeur peut également charger sa feuille de calcul de tarification sur un emplacement de stockage temporaire, puis inclure l’URI dans la requête comme pour d'autres artefacts de fichier. La feuille de calcul est ensuite chargée et traduite pour évaluer la grille de prix spécifiée. Puis l'offre est mise à jour avec les informations de tarification. Les requêtes GET suivantes relatives à l’offre renverront l’URI de la feuille de calcul et les évaluations de prix correspondant à la région.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="new-core-sizes-added-on-722019"></a>Nouvelles tailles de cœurs ajoutées le 02/07/2019
---------------------------

Les éditeurs de machines virtuelles ont été avertis le 2 juillet 2019 de l’ajout de nouveaux tarifs pour les nouvelles tailles de machines virtuelles Azure (en fonction du nombre de cœurs).  Les nouveaux tarifs sont adaptés aux tailles de cœurs suivantes : 10, 44, 48, 60, 120, 208 et 416.  Pour les machines virtuelles existantes, de nouveaux tarifs pour ces tailles de cœurs ont été calculés automatiquement en fonction des tarifs actuels.  Les éditeurs ont jusqu’au 1er août 2019 pour passer en revue les prix supplémentaires et apporter les modifications souhaitées.  Après cette date, s’ils n’ont pas encore été republiés par l’éditeur, les prix automatiquement calculés pour ces nouvelles tailles de cœurs prendront effet.


<a name="regions"></a>Régions
-------

Le tableau suivant présente les différentes régions que vous pouvez spécifier pour la tarification de cœur personnalisée, ainsi que les codes devise correspondants.

| **Région** | **Nom**             | **Code devise** |
|------------|----------------------|-------------------|
| DZ         | Algérie              | DZD               |
| AR         | Argentine            | ARS               |
| AU         | Australie            | AUD               |
| AT         | Autriche              | EUR               |
| BH         | Bahreïn              | BHD               |
| BY         | Bélarus              | RUB               |
| BE         | Belgique              | EUR               |
| BR         | Brésil               | USD               |
| BG         | Bulgarie             | BGN               |
| CA         | Canada               | CAD               |
| CL         | Chili                | CLP               |
| CO         | Colombie             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Croatie              | HRK               |
| CY         | Chypre               | EUR               |
| CZ         | République tchèque       | CZK               |
| DK         | Danemark              | DKK               |
| DO         | République dominicaine   | USD               |
| EC         | Équateur              | USD               |
| EG         | Égypte                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estonie              | EUR               |
| FI         | Finlande              | EUR               |
| FR         | France               | EUR               |
| DE         | Allemagne              | EUR               |
| GR         | Grèce               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hong Kong (R.A.S.)        | HKD               |
| HU         | Hongrie              | HUF               |
| IS         | Islande              | ISK               |
| IN         | Inde                | INR               |
| id         | Indonésie            | IDR               |
| IE         | Irlande              | EUR               |
| IL         | Israël               | ILS               |
| IT         | Italie                | EUR               |
| JP         | Japon                | JPY               |
| JO         | Jordanie               | JOD               |
| KZ         | Kazakhstan           | KZT               |
| KE         | Kenya                | KES               |
| KR         | Corée du Sud                | KRW               |
| KW         | Koweït               | KWD               |
| LV         | Lettonie               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Lituanie            | EUR               |
| LU         | Luxembourg           | EUR               |
| MK         | Macédoine du Nord      | MKD               |
| MY         | Malaisie             | MYR               |
| MT         | Malte                | EUR               |
| MX         | Mexique               | MXN               |
| ME         | Monténégro           | EUR               |
| MA         | Maroc              | MAD               |
| NL         | Pays-bas          | EUR               |
| NZ         | Nouvelle-Zélande          | NZD               |
| NG         | Nigeria              | NGN               |
| NON         | Norvège               | NOK               |
| OM         | Oman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Pérou                 | PEN               |
| PH         | Philippines          | PHP               |
| PL         | Pologne               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Porto Rico          | USD               |
| QA         | Qatar                | QAR               |
| RO         | Roumanie              | RON               |
| RU         | Russie               | RUB               |
| SA         | Arabie Saoudite         | SAR               |
| RS         | Serbie               | RSD               |
| SG         | Singapour            | SGD               |
| SK         | Slovaquie             | EUR               |
| SI         | Slovénie             | EUR               |
| ZA         | Afrique du Sud         | ZAR               |
| ES         | Espagne                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Suède               | SEK               |
| CH         | Suisse          | CHF               |
| TW         | Taïwan               | TWD               |
| MJ         | Thaïlande             | THB               |
| TT         | Trinité-et-Tobago  | TTD               |
| TN         | Tunisie              | TND               |
| TR         | Turquie               | TRY               |
| UA         | Ukraine              | UAH               |
| AE         | Émirats Arabes Unis | EUR               |
| GB         | Royaume-Uni       | GBP               |
| FR         | États-Unis        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
