---
title: Liens vers la documentation pour l’enrichissement de l’IA
titleSuffix: Azure Cognitive Search
description: Liste annotée d’articles, de tutoriels, d’exemples et de billets de blog sur les charges de travail d’enrichissement de l’IA dans la recherche cognitive Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 5fb1050fed2ab7318ad5b4ecafec7a96a9324575
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792066"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Ressources de documentation pour l’enrichissement de l’IA dans la recherche cognitive Azure

L’enrichissement de l’IA est une fonctionnalité de l’indexation de la recherche cognitive Azure qui trouve des informations latentes dans des sources non textuelles et le texte indifférencié, et les transforme en contenu avec possibilité de recherche en texte intégral dans la recherche cognitive Azure.

Les articles suivants constituent la documentation complète de l’enrichissement de l’IA.

## <a name="getting-started"></a>Prise en main
+ [Présentation de l’enrichissement de l’IA dans la recherche cognitive Azure](cognitive-search-concept-intro.md)
+ [Démarrage rapide : Essayer l’enrichissement de l’IA dans le portail](cognitive-search-quickstart-blob.md)
+ [Tutoriel : Indexation enrichie avec l’IA](cognitive-search-tutorial-blob.md)
+ [Exemple : Création d’une compétence personnalisée pour l’enrichissement de l’IA](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Guides pratiques
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Guide pratique pour référencer des annotations dans un ensemble de compétences](cognitive-search-concept-annotations-syntax.md)
+ [Guide pratique pour mapper des champs sur un index](cognitive-search-output-field-mapping.md)
+ [Guide pratique pour traiter et extraire des informations à partir d’images](cognitive-search-concept-image-scenarios.md)
+ [Guide pratique pour régénérer un index de recherche cognitive Azure](search-howto-reindex.md)
+ [Guide pratique pour définir une interface de compétences personnalisées](cognitive-search-custom-skill-interface.md)
+ [Conseils de dépannage](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Informations de référence

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Compétences personnalisées
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Compétences dépréciées](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Créer un ensemble de compétences (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Créer un indexeur (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Voir aussi

+ [API REST de la recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexeurs dans la recherche cognitive Azure](search-indexer-overview.md)
+ [Qu’est-ce que la recherche cognitive Azure ?](search-what-is-azure-search.md)
