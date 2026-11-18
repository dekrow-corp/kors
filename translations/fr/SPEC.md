# Spécification KORS

> Traduction française, non normative. Le texte de référence est la [version anglaise](../../SPEC.md) ; en cas d'écart, elle prévaut.

| | |
|---|---|
| **Version** | 0.1 (premier brouillon public) |
| **Date** | 18 novembre 2026 |
| **Auteur** | Sean Lamet, pour Dekrow |
| **Licence** | [CC BY 4.0](../../LICENSE) |
| **Source de référence** | https://github.com/dekrow-corp/kors |

## Statut de ce document

Ce document est la première version publique de la spécification KORS. C'est un brouillon : les cinq critères sont stables, leurs exigences détaillées peuvent encore évoluer avant la version 1.0. Chaque changement est tracé dans le [CHANGELOG](../../CHANGELOG.md). Les retours passent par les issues du dépôt de référence ; voir [CONTRIBUTING.md](../../CONTRIBUTING.md).

## 1. Objet et périmètre

KORS (Kernel for Operations and Resources System) désigne une propriété d'architecture d'un ensemble d'applications de gestion de production : MES, QMS, CMMS, DMS, PLM et toute application du niveau 3 ISA-95. Ce n'est ni un produit, ni une technologie, ni une marque.

Un système peut se dire KORS s'il satisfait les cinq critères de la section 4. Les cinq, pas quatre sur cinq.

KORS contraint la façon dont les applications sont construites, pour qu'elles n'aient jamais besoin d'être intégrées entre elles. Il ne décrit pas comment relier après coup des logiciels existants, comment collecter les données des machines, ni comment organiser un espace de données à l'échelle de l'usine. Ces problèmes ont leurs propres réponses, dont plusieurs sont citées en section 11.

Les sections 2 à 7 sont normatives. Les sections 8 à 13 sont informatives.

## 2. Langage de conformité

Les mots DOIT, NE DOIT PAS, DEVRAIT, NE DEVRAIT PAS et PEUT s'entendent au sens du BCP 14 ([RFC 2119], [RFC 8174]) (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY), lorsqu'ils apparaissent en capitales et seulement dans ce cas.

Chaque exigence porte un identifiant stable (C1.1, C3.4...). Les identifiants ne sont jamais réutilisés : une exigence retirée laisse un trou.

## 3. Définitions

- **Système** : l'ensemble des services, exploités ensemble pour une organisation, sur lequel porte une déclaration de conformité KORS.
- **Service** : unité déployable indépendamment qui porte une capacité métier (suivi des OF, gestion des non-conformités, maintenance...).
- **Propriétaire** : le seul service autorisé à modifier une donnée. Les autres la lisent ou la reçoivent.
- **Événement** : fait métier passé et immuable, publié par le service propriétaire ("OF démarré", "pièce rebutée", "machine à l'arrêt").
- **Contrat** : description publiée et versionnée d'un échange : schéma, sémantique et règles de compatibilité.
- **Vocabulaire commun** : ensemble des types et identifiants utilisés dans les contrats pour les entités partagées entre services.
- **Extension** : ajout nommé au vocabulaire commun, en général pour un secteur, qui ne modifie pas le socle.
- **Frontière** : point où un système qui n'est pas KORS échange avec un KORS.
- **Adaptateur** : service placé sur une frontière, qui traduit entre un système externe et le vocabulaire commun.
- **Client** : l'organisation dont le système gère la production et détient les données.
- **Éditeur** : l'organisation qui fournit un ou plusieurs services. Un système peut avoir plusieurs éditeurs, et le client peut être son propre éditeur.

## 4. Les cinq critères

### C1. Communication par événements

- **C1.1** Tout changement d'état métier utile hors du service DOIT être publié comme événement par son propriétaire.
- **C1.2** Chaque type d'événement DOIT être décrit par un contrat publié et versionné.
- **C1.3** Les lectures synchrones entre services sont permises. Un service NE DOIT PAS dépendre de la disponibilité d'un autre pour assurer sa fonction principale.
- **C1.4** La synchronisation par fichiers en lot entre services NE DOIT PAS être utilisée.
- **C1.5** Le transport n'est pas imposé (MQTT, NATS, Kafka, AMQP ou autre).
- **C1.6** Chaque événement DOIT porter au minimum : un identifiant unique, son type, la version de son contrat, l'heure à laquelle le fait s'est produit et l'identité du service qui le publie.
- **C1.7** Les consommateurs DEVRAIENT tolérer les événements dupliqués ou reçus dans le désordre. Les garanties de livraison offertes par un service DEVRAIENT figurer dans ses contrats.

Note : C1.4 s'applique entre services. Un adaptateur (section 5) peut lire ou écrire des fichiers quand c'est la seule interface qu'offre un système externe ; ce qu'il échange avec les autres services, ce sont des événements.

### C2. Vocabulaire commun aux frontières

- **C2.1** Les contrats DOIVENT exprimer les entités partagées (équipement, matière et lot, personnel, ordre de fabrication, segment de process, non-conformité...) dans un vocabulaire commun.
- **C2.2** Ce vocabulaire DEVRAIT dériver d'ISA-95 / IEC 62264 (parties 2 et 4). Tout écart DOIT être documenté.
- **C2.3** Chaque service reste libre de son modèle interne et de sa persistance. Il NE DOIT PAS exister de base de données partagée entre services.
- **C2.4** Les identifiants des entités partagées DOIVENT être stables et non ambigus d'un service à l'autre.
- **C2.5** Des extensions sectorielles (EN 9100, IATF 16949...) PEUVENT enrichir le vocabulaire sous forme d'extensions nommées, sans modifier le socle.

### C3. Autonomie de chaque service

- **C3.1** Chaque service DOIT pouvoir être déployé, mis à jour et redémarré sans redéployer les autres.
- **C3.2** La panne d'un service NE DOIT PAS arrêter les autres. Le comportement dégradé DEVRAIT être documenté.
- **C3.3** Chaque service DOIT être propriétaire de ses données.
- **C3.4** Un changement incompatible de contrat DOIT donner lieu à une nouvelle version. L'ancienne DOIT rester servie pendant une période documentée.
- **C3.5** Les consommateurs DEVRAIENT ignorer les champs qu'ils ne connaissent pas, pour qu'un ajout compatible à un contrat ne les casse pas.

### C4. Modularité d'adoption

- **C4.1** Chaque service DOIT apporter une valeur utilisable seul.
- **C4.2** Ajouter un service NE DOIT PAS exiger de modifier les services existants : il s'abonne aux événements dont il a besoin.
- **C4.3** Retirer un service NE DOIT PAS casser les autres au-delà des fonctions qui en dépendent explicitement et de façon documentée.

### C5. Données ouvertes

- **C5.1** Le vocabulaire et les contrats DOIVENT être documentés et accessibles au client sans condition.
- **C5.2** Le client DOIT pouvoir exporter l'intégralité de ses données dans un format documenté, sans intervention de l'éditeur.
- **C5.3** Une API d'accès documentée DOIT exister. Elle DEVRAIT suivre un standard ouvert quand il couvre le besoin (par exemple i3X pour l'accès aux données contextualisées).
- **C5.4** Le système DOIT pouvoir être hébergé sur une infrastructure choisie par le client.
- **C5.5** La fin de la relation contractuelle, quelle qu'en soit la cause, NE DOIT PAS priver le client de ses données. L'export de C5.2 DOIT rester accessible après la fin du contrat : pendant une période documentée quand l'éditeur héberge le système, et sans limite de durée quand le client l'héberge.

## 5. La frontière

Un KORS vit rarement seul : l'usine a un ERP, des machines, parfois un historian ou un PLM existant. Ces systèmes ne sont pas KORS et n'ont pas à l'être.

- Un système externe s'intègre par un **adaptateur** dédié, qui traduit ses données vers le vocabulaire commun et inversement (couche anti-corruption).
- L'adaptateur est un service comme un autre : il DOIT respecter C1 à C5.
- Un UNS existant PEUT servir de transport ou de source. Un KORS PEUT publier vers un UNS.
- La promesse exacte : **rien à intégrer entre services KORS, une seule frontière à maintenir par système externe.**

## 6. Ce que KORS n'exige pas

- Une technologie, un broker, un langage, un cloud, un fournisseur d'identité ou un orchestrateur particulier.
- Un éditeur unique : deux services de deux éditeurs différents peuvent former un KORS.
- Que le code des services soit open source. Seules les données, le vocabulaire et les contrats doivent être ouverts.
- La présence d'un UNS.
- Un format particulier de description des contrats. CloudEvents, AsyncAPI, Protocol Buffers, JSON Schema ou tout autre format documenté convient.

## 7. Conformité

- **7.1** Un système est conforme à une version donnée de cette spécification s'il satisfait toutes les exigences DOIT et NE DOIT PAS des sections 4 et 5. Il n'y a pas de conformité partielle : un système qui satisfait quatre critères sur cinq n'est pas KORS.
- **7.2** Une exigence exprimée avec DEVRAIT ne peut rester non satisfaite que si l'écart et sa raison sont documentés.
- **7.3** Une déclaration de conformité DOIT indiquer la version de la spécification, le périmètre de la déclaration (quels services, quel déploiement) et l'endroit où consulter les preuves de la section 8.
- **7.4** La conformité est déclarative. Cette spécification ne définit ni organisme de certification ni label. Une déclaration NE DOIT PAS affirmer ou laisser entendre qu'elle a été vérifiée ou approuvée par les auteurs de cette spécification.
- **7.5** La conformité est une propriété d'un système déployé, pas d'un produit isolé. Un produit seul peut être dit "conçu pour KORS" s'il satisfait toutes les exigences qui s'appliquent à un service individuel.

Un modèle de déclaration de conformité se trouve dans [CONFORMANCE.md](../../CONFORMANCE.md).

## 8. Liste de contrôle

| # | Question | Preuve attendue |
|---|---|---|
| C1 | Les changements d'état sont-ils publiés par leur propriétaire ? | Liste des contrats d'événements publiés et versionnés |
| C1 | Un service fonctionne-t-il quand un autre est arrêté ? | Test : arrêter un service, vérifier que les autres assurent leur fonction principale |
| C1 | Peut-on identifier un événement et remonter à sa source ? | Exemples d'événements montrant identifiant, type, version de contrat, heure du fait et service émetteur |
| C2 | Les contrats référencent-ils un vocabulaire commun documenté ? | Vocabulaire publié, écarts à ISA-95 listés |
| C2 | Existe-t-il une base partagée entre services ? | Schéma de déploiement : une base par service |
| C3 | Peut-on mettre à jour un service seul ? | Procédure de mise à jour d'un service sans redéploiement des autres |
| C3 | Que se passe-t-il quand un contrat change ? | Politique de versionnement et durée de support |
| C4 | Chaque service est-il utilisable seul ? | Démonstration d'un service déployé isolément |
| C4 | Ajouter un service oblige-t-il à modifier les autres ? | Ajout d'un service par simple abonnement |
| C5 | Le client peut-il tout exporter seul ? | Export complet documenté, exécutable sans l'éditeur |
| C5 | Le client choisit-il l'hébergement ? | Installation documentée sur une infrastructure tierce |
| C5 | Que deviennent les données quand le client part ? | Clauses contractuelles et procédure d'export qui restent valables après la fin du contrat, avec la période documentée quand l'éditeur héberge |

## 9. Exemples

- **Coche les cinq** : un MES et un QMS de deux éditeurs différents. Chacun a sa base, publie ses événements selon des contrats versionnés en vocabulaire ISA-95, et propose un export complet. Le QMS réagit à "pièce rebutée" sans que le MES le connaisse. L'ERP est raccordé par un adaptateur.
- **Ne coche pas (2 sur 5 au mieux)** : un MES monolithique branché sur un UNS. La donnée circule (C1 partiel, C5 possible), mais le vocabulaire se limite à une hiérarchie de topics (C2 non), et les fonctions ne se déploient pas séparément (C3 et C4 non).
- **Ne coche pas** : une suite "modulaire" d'un seul éditeur dont les modules partagent une base de données. Modularité commerciale (C4 en apparence), mais C2.3 et C3 violés.

## 10. Justification

### 10.1 KORS et l'Unified Namespace

Un UNS est un espace de noms unique, en général un broker MQTT organisé selon la hiérarchie ISA-95, où chaque système de l'usine publie et lit. C'est devenu la référence pour connecter machines, capteurs et systèmes existants. Il ne dit rien sur la façon dont les applications sont faites : on peut y brancher un ERP monolithique, un MES fermé et un tableur. La donnée circulera ; les applications resteront ce qu'elles sont.

KORS pose la contrainte un cran plus haut, sur les applications elles-mêmes. Un UNS peut servir de transport à un KORS. L'inverse n'a pas de sens.

> L'UNS répond à "comment je rassemble mes données". KORS répond à "comment je construis des outils de production qui n'ont jamais besoin d'être intégrés entre eux".

### 10.2 Pourquoi un nom

Tous les éditeurs disent déjà "modulaire" et "ouvert", et ces mots n'engagent à rien. Un nom précis, avec cinq critères qu'un tiers peut vérifier, engage : un système les coche ou ne les coche pas.

N'importe qui peut construire un KORS et le dire. C'est voulu. Le nom a vocation à devenir une référence partagée, comme "UNS" ou "MES composable" le sont devenus, pas une marque qu'on défend devant un tribunal.

## 11. Travaux voisins et ce que KORS leur doit

Aucune des propriétés exigées par KORS n'est nouvelle prise isolément. Chacune a un nom et une communauté qui la porte depuis des années.

- **ISA-95 / IEC 62264** [IEC62264] : le vocabulaire (parties 2 et 4) et le modèle d'échanges pub/sub et requête/réponse (partie 6, Messaging Service Model). B2MML en est l'implémentation XML.
- **UNS (Unified Namespace)**, terme popularisé par Walker Reynolds : la diffusion d'événements à toute l'entreprise, et la preuve qu'un concept sans produit peut s'imposer.
- **Asset Administration Shell et VDI/VDE 2193** [IEC63278] [VDI2193] : l'idée d'un langage commun fait d'un vocabulaire, d'une structure de message et de protocoles d'interaction.
- **Eclipse Arrowhead** [ARROWHEAD] : le socle minimal obligatoire par lequel des systèmes autonomes se trouvent et coopèrent.
- **PROSA et les architectures holoniques** [PROSA] : des entités de production autonomes qui coopèrent par messages.
- **Composable MES et Packaged Business Capabilities** : la décomposition d'une application de production en capacités déployables séparément, sur un modèle de données canonique.
- **Domain-Driven Design** [DDD] : le langage publié aux frontières, le contexte borné et la couche anti-corruption.
- **Self-Contained Systems et microservices** [SCS] : des unités autonomes sans base partagée, qui privilégient la communication asynchrone.
- **MACH** [MACH] : un label d'architecture défini par une courte liste de principes plutôt que par un produit.
- **O-PAS et i3X** [OPAS] [I3X] : la méthode, avec des exigences normatives et une conformité vérifiable.
- **CloudEvents et AsyncAPI** [CLOUDEVENTS] [ASYNCAPI] : des façons neutres de décrire l'enveloppe des événements et les contrats événementiels.
- **Règlement (UE) 2023/2854 (Data Act)** [DATAACT] : l'expression légale du droit du client à accéder à ses données, à les exporter et à les déplacer.

Ce que KORS ajoute : l'obligation de réunir ces propriétés au niveau des applications de production, un nom pour cette réunion, et une définition qu'un tiers peut vérifier.

## 12. Sécurité et sûreté

Cette spécification ne définit pas de mécanisme de sécurité. Ouvrir les contrats et les données (C5) ne veut pas dire ouvrir l'accès : l'authentification, l'autorisation par contrat, la segmentation réseau entre IT et OT et la protection des données personnelles restent à la charge de chaque implémentation et des réglementations qui s'y appliquent. Un KORS ne remplace pas les fonctions de sécurité des machines et des systèmes de contrôle-commande.

## 13. Questions ouvertes

- **Socle minimal du vocabulaire** : quelles entités ISA-95 sont obligatoires ? Proposition : équipement, matière et lot, personnel, ordre de fabrication, segment de process, non-conformité.
- **Usage d'ISA-95** : la spécification cite les concepts et les parties de la norme sans en reproduire le texte.
- **Outil de conformité** : liste de contrôle seule, ou suite de tests automatisable à terme.
- **Registre des extensions** : où publier les extensions sectorielles nommées (C2.5) et comment garantir l'unicité de leurs noms.
- **Gouvernance** : voir [GOVERNANCE.md](../../GOVERNANCE.md).

## 14. Références

Voir la [section 14 de la version anglaise](../../SPEC.md#14-references).

## 15. Attribution

© 2026 Sean Lamet et les contributeurs KORS. Ce document est sous licence [Creative Commons Attribution 4.0 International](../../LICENSE). Pour le réutiliser, créditer :

> KORS, Kernel for Operations and Resources System. Sean Lamet / Dekrow, 2026. https://github.com/dekrow-corp/kors, CC BY 4.0.

Si vous modifiez le texte, indiquez-le, et ne présentez pas le texte modifié comme la spécification KORS.
