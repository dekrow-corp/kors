# KORS

> Traduction française, non normative. Le texte de référence est la [version anglaise](../../README.md).

**Kernel for Operations and Resources System.** Un concept d'architecture pour les logiciels de production. Pas une technologie, pas un produit.

L'UNS répond à "comment je rassemble mes données". KORS répond à "comment je construis des outils de production qui n'ont jamais besoin d'être intégrés entre eux".

## Les cinq critères

Un système peut se dire KORS s'il coche les cinq. Pas quatre sur cinq.

1. **Communication par événements** : chaque changement d'état métier est publié par le service qui en est propriétaire, selon un contrat versionné.
2. **Vocabulaire commun aux frontières** : les échanges utilisent un vocabulaire commun dérivé d'ISA-95 ; chaque service reste libre de son modèle interne.
3. **Autonomie de chaque service** : chacun se déploie, évolue et tombe en panne seul.
4. **Modularité d'adoption** : chaque service vaut quelque chose seul, et plus avec les autres.
5. **Données ouvertes** : contrats documentés, export complet, hébergement au choix du client.

La définition complète, la liste de contrôle et les exemples sont dans la [spécification](SPEC.md).

## Ce que KORS n'est pas

- **Une technologie.** NATS, Kafka, MQTT, Dapr peuvent servir à en construire un. Ils sont interchangeables.
- **Un middleware.** KORS décrit comment des services sont construits pour se parler, pas comment relier après coup des logiciels existants.
- **Une marque.** N'importe qui peut construire un KORS et le dire, à condition de cocher les cinq critères.

## Origine

KORS a été conçu et nommé par Sean Lamet en 2026. Ce dépôt en est la première publication et la référence : la version 0.1 a été publiée le 18 novembre 2026. Il est publié par [Dekrow](https://dekrow.com) ([dekrow-corp](https://github.com/dekrow-corp) sur GitHub), qui en est le premier utilisateur, pas le propriétaire.

KORS ne prétend pas avoir inventé ses briques : chacune a une histoire, reconnue dans la [section 11 de la spécification](SPEC.md). Ce qui naît ici, c'est le nom, la définition de KORS comme réunion de ces cinq propriétés au niveau des applications de production, et les critères qui la rendent vérifiable.

## Licence et citation

Les textes de ce dépôt sont sous licence [Creative Commons Attribution 4.0 International](../../LICENSE) (CC BY 4.0). Vous pouvez les copier, les traduire et les adapter, y compris à des fins commerciales, à condition de créditer l'origine, de renvoyer à la licence et de signaler vos modifications :

> KORS, Kernel for Operations and Resources System. Sean Lamet / Dekrow, 2026. https://github.com/dekrow-corp/kors, CC BY 4.0.

Le reste du dépôt (conformité, gouvernance, contribution) est en anglais : voir le [README principal](../../README.md).

## Contact

contact@dekrow.com
