# Besoin : assistant des fiches support

## Questions et réponses retenues

| N° | Question | Réponse retenue |
|---|---|---|
| Q1 | Que faire quand une fiche n'existe pas ? | On crée un brouillon d'une nouvelle fiche en direct pendant l'appel. |
| Q2 | Convention de nommage des fiches et des brouillons ? | Utilisation d'un prefixe (BR-) + nomenclature des fiches existantes |
| Q3 | Comment sont validés les brouillons ? | Lors de l'appel le technicien enregistre son brouillon s'il est satisfait. Il peut revenir plus tard le valider et le transformer en fiche. |
| Q4 | L'assistant indique-t-il la/les fiches sources dans sa réponse ? | Oui, avec le titre exact de la/les fiches. |
| Q5 | Qui a accès aux fiches ? | Tous les techniciens ont accès à toute la liste des fiches. |
| Q6 | Comment trouve-t-on une fiche ? | Le technicien écrit des mots-clés et la bonne fiche sort. Il peut aussi afficher toutes les fiches. Les fiches sont des fichiers .md en snake-case. |
| Q7 | Quel délai pour répondre ? | 30 secondes max pr que le système dise s'il trouve une réponse ou non. |
| Q8 | Y a-t-il des besoins d'accessibilité ? | Oui : une technicienne est aveugle. Elle utilise un lecteur d'écran, donc elle entends la lecture écran. |
| Q9 | Peut-on supprimer une fiche ? | Non. Modification et ajout uniquement. |
| Q10 | Quelles limites techniques ? | 20 Ko maximum par fiche lue ; 60 000 tokens maximum par conversation (toute la session d'un appel) ; 10 appels d'outil maximum par appel téléphonique. |

## Parcours

### Parcours 1 : consulter une fiche

| Étape | Qui | Fait quoi |
|---|---|---|
| 1 | Technicien | Écrit des mots-clés en direct pendant l'appel du client. |
| 2 | Assistant | Cherche, dans le dossier des fiches, la/les fiches qui correspondent. |
| 3 | Assistant | Envoie la question et le contenu de la/les fiches au service d'IA via LiteLLM. |
| 4 | Service d'IA | Renvoie une réponse construite à partir de la ou des fiches. |
| 5 | Assistant | Affiche la réponse, puis le titre exact de la ou des fiches sources, en 30 secondes maximum. Si aucune fiche ne correspond, il le dit et propose de créer un brouillon. |
| 6 | Technicien | Lit ou écoute la réponse et la suit pendant l'appel. |

### Parcours 2 : enregistrer un brouillon pendant l’appel

| Étape | Qui | Fait quoi |
|---|---|---|
| 1 | Technicien | Pour un sujet sans fiche, il crée un brouillon. |
| 2 | Assistant | Envoie la demande au service d'IA via LiteLLM. |
| 3 | Service d'IA | Renvoie un texte de brouillon. |
| 4 | Assistant | Affiche le brouillon au technicien. |
| 5 | Technicien | Relit le brouillon. S'il est satisfait, demande de l'enregistrer. |
| 6 | Assistant | Propose un nom « BR-... » suivant la nomenclature des fiches (.md, snake-case). Si un fichier du même nom existe déjà, demande confirmation avant de l'écraser. |
| 7 | Technicien | Confirme ou refuse. |
| 8 | Assistant | Enregistre le brouillon dans le dossier des fiches et indique son nom. Sinon, il ne modifie rien. |

### Parcours 3 : valider un brouillon plus tard

| Étape | Qui | Fait quoi |
|---|---|---|
| 1 | Technicien | Ouvre un brouillon « BR-... » déjà enregistré. |
| 2 | Assistant | Affiche le contenu du brouillon. |
| 3 | Technicien | Valide le brouillon pour en faire une fiche. |
| 4 | Assistant | Prépare le nom définitif, sans le préfixe « BR- ». Si une fiche du même nom existe déjà, demande confirmation avant de l'écraser. |
| 5 | Technicien | Confirme ou refuse. |
| 6 | Assistant | Enregistre la fiche définitive dans le dossier des fiches et indique son nom. Sinon, il ne modifie rien. |

## User story

### Parcours 1
> US1 — **En tant** que technicien support de Batifact, **je veux** retrouver rapidement la bonne fiche de procédure pendant un appel, **afin** d'aider le client sans le faire attendre.

### Parcours 2
> US2 — **En tant** que technicien support de Batifact, **je veux** faire rédiger un brouillon de fiche quand aucune procédure n'existe sur le sujet, **afin** de garder une trace réutilisable et gagner du temps pour les prochaines fois. 

### Parcours 3
> US3 — **En tant** que technicien support de Batifact, **je veux** reprendre un brouillon plus tard et le transformer en fiche, **afin** que l'équipe dispose d'une procédure fiable et validée.


## Critères d'acceptation

### US1 — Retrouver la bonne fiche

| N° | Étant donné… | Quand… | Alors… | Origine |
|---|---|---|---|---|
| CA-1 | Le dossier contient la fiche « Facture non reçue par le client final » | Le technicien saisit « facture non reçue » | L'assistant affiche une réponse et le titre exact de la fiche source | Q4, Q6 |
| CA-2 | Aucune fiche ne traite d'imprimante | Le technicien saisit « imprimante » | Un message indique qu'aucune fiche ne correspond et propose de créer un brouillon | Q1 |
| CA-3 | Le dossier contient des fiches | Le technicien envoie une question | En 30 secondes maximum, une réponse avec le titre de la fiche, ou un message « aucune fiche », est affiché | Q7 |
| CA-4 | Le dossier contient des fiches | Le technicien demande d'afficher toutes les fiches | La liste des titres de toutes les fiches s'affiche | Q5, Q6 |
| CA-5 | L'assistant doit interroger le service d'IA | Le technicien pose une question | La demande apparaît dans le journal de la passerelle LiteLLM et n'est envoyée à aucun autre service | Mail |
| CA-6 | Une limite est atteinte (fiche de plus de 20 Ko, 60 000 tokens, ou 10 appels d'outil) | Le technicien poursuit | Un message indique la limite atteinte et l'action concernée n'est pas exécutée | Q10 |

### US2 — Enregistrer un brouillon

| N° | Étant donné… | Quand… | Alors… | Origine |
|---|---|---|---|---|
| CA-7 | Aucune fiche ne traite de « Imprimante bloquée » | Le technicien demande un brouillon pour ce sujet | Le brouillon s'affiche et aucun fichier n'est encore créé | Q1 |
| CA-8 | Un brouillon convient au technicien | Le technicien demande de l'enregistrer | Un fichier « BR-imprimante_bloquee.md » existe dans le dossier et un message indique son nom | Q2, Q3 |

### US3 — Valider un brouillon plus tard

| N° | Étant donné… | Quand… | Alors… | Origine |
|---|---|---|---|---|
| CA-9 | Le dossier contient « BR-imprimante_bloquee.md » et aucune fiche de ce nom | Le technicien valide ce brouillon | Un fichier « imprimante_bloquee.md » existe, sans le préfixe « BR- », et un message indique son nom | Q2, Q3 |
| CA-10 | Le dossier contient « BR-modifier_le_taux_de_tva.md » et déjà une fiche « modifier_le_taux_de_tva.md » | Le technicien valide ce brouillon | L'assistant demande confirmation avant de remplacer ; tant que le technicien n'a pas confirmé, la fiche n'est pas modifiée | Mail |
| CA-11 | La confirmation est affichée | Le technicien confirme | La fiche est remplacée par le contenu du brouillon et un message indique son nom | Mail |

### Règles d'accès au dossier

| N° | Étant donné… | Quand… | Alors… | Origine |
|---|---|---|---|---|
| CA-12 | Un fichier existe hors du dossier des fiches | Le technicien demande de le lire ou de le modifier | Un message indique que l'accès est refusé et le fichier n'est ni lu ni modifié | Mail |
| CA-13 | Le dossier contient la fiche « Facture non reçue par le client final » | Le technicien demande de la supprimer | Un message indique que la suppression n'est pas possible et la fiche est toujours là | Q9 |

## Mémo d’accessibilité
