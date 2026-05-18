# Diagramme des Cas d'Utilisation

Ce diagramme illustre les interactions des différents acteurs (Acheteur, Producteur, Transporteur, et Administrateur) avec les fonctionnalités principales de la plateforme AgriConnect.

## 1. Diagramme 

```mermaid
graph LR
    classDef actor fill:#e1bee7,stroke:#7b1fa2,stroke-width:2px,color:#000
    classDef uc fill:#e3f2fd,stroke:#1565c0,stroke-width:1.5px
    classDef ucSec fill:#fff9c4,stroke:#f9a825,stroke-width:1.5px

    %% ─── Acteurs ───
    Buyer((Acheteur)):::actor
    Producer((Producteur)):::actor
    Transporter((Transporteur)):::actor
    Admin((Administrateur)):::actor

    subgraph Système AgriConnect
        direction TB

        %% ─── Cas communs ───
        UC1(S'authentifier):::uc
        UC2(Gérer son portefeuille):::uc
        UC3(Consulter les notifications):::uc

        %% ─── Sous-cas communs ───
        UC1a(Vérifier OTP):::ucSec
        UC2a(Effectuer un retrait):::ucSec

        %% ─── Acheteur ───
        UC4(Parcourir le marché):::uc
        UC5(Passer une commande):::uc
        UC6(Négocier un prix):::uc
        UC7(Payer via Mobile Money):::uc
        UC8(Confirmer la réception):::uc
        UC9(Suivre la commande):::uc
        UC10(Ouvrir un litige):::ucSec

        %% ─── Producteur ───
        UC11(Publier un produit):::uc
        UC12(Gérer ses produits):::uc
        UC13(Répondre aux offres):::uc
        UC14(Valider la préparation):::uc

        %% ─── Transporteur ───
        UC15(Prendre en charge une livraison):::uc
        UC16(Marquer comme livré):::ucSec

        %% ─── Administrateur ───
        UC17(Modérer les annonces):::uc
        UC18(Gérer les utilisateurs):::uc
        UC19(Vérifier l'identité KYC):::uc
        UC20(Résoudre un litige):::uc
        UC21(Forcer un remboursement):::ucSec
    end

    %% ─── Relations Acheteur ───
    Buyer --> UC1
    Buyer --> UC2
    Buyer --> UC3
    Buyer --> UC4
    Buyer --> UC5
    Buyer --> UC6
    Buyer --> UC8
    Buyer --> UC9

    %% ─── Relations Producteur ───
    Producer --> UC1
    Producer --> UC2
    Producer --> UC3
    Producer --> UC11
    Producer --> UC12
    Producer --> UC13
    Producer --> UC14

    %% ─── Relations Transporteur ───
    Transporter --> UC1
    Transporter --> UC2
    Transporter --> UC3
    Transporter --> UC15

    %% ─── Relations Administrateur ───
    Admin --> UC17
    Admin --> UC18
    Admin --> UC19
    Admin --> UC20

    %% ─── <<include>> ───
    UC1 -- "«include»" --> UC1a
    UC2 -- "«include»" --> UC2a
    UC5 -- "«include»" --> UC7
    UC5 -- "«include»" --> UC1
    UC11 -- "«include»" --> UC1
    UC13 -- "«include»" --> UC1
    UC15 -- "«include»" --> UC1
    UC16 -- "«include»" --> UC15

    %% ─── <<extend>> ───
    UC8 -- "«extend»" --> UC10
    UC6 -- "«extend»" --> UC5
    UC9 -- "«extend»" --> UC5
    UC20 -- "«extend»" --> UC21
    UC19 -- "«extend»" --> UC18
```

---

## Relations clés expliquées

### Relations `«include»`
- **S'authentifier** inclut toujours **Vérifier OTP** (SMS obligatoire).
- **Gérer son portefeuille** inclut **Effectuer un retrait** (sous-flux bancaire obligatoire).
- **Passer une commande** inclut **Payer via Mobile Money** (paiement obligatoire) et **S'authentifier** (accès sécurisé requis).
- **Publier/Répondre à des offres/Prendre en charge une livraison** incluent tous **S'authentifier**.
- **Marquer comme livré** inclut **Prendre en charge une livraison** (pré-requis).

### Relations `«extend»`
- **Confirmer la réception** peut s'étendre vers **Ouvrir un litige** (si l'acheteur signale un problème).
- **Négocier un prix** étend **Passer une commande** (optionnel selon le producteur).
- **Suivre la commande** étend **Passer une commande** (fonctionnalité optionnelle de suivi).
- **Résoudre un litige** peut s'étendre vers **Forcer un remboursement** (décision admin).
- **Vérifier l'identité KYC** étend **Gérer les utilisateurs** (processus spécifique, non systématique).
---

## 2. Description des Cas d'Utilisation

###  L'Acheteur (Buyer)
C'est le demandeur principal sur le marché. Ses actions clés au sein du système sont :
- **Recherche & Filtrage** : Exploration libre ou via recherche par mots-clés du catalogue (Dashboard Marketplace).
- **Achat ou Négociation** : Décision d'acheter aux conditions listées ou d'engager une discussion via le chat P2P pour revoir le prix/les quantités en cas de transaction B2B.
- **Réception Commande** : Responsabilité de déclencher le déblocage des paiements (qui sont placés en compte séquestre lors de la validation), validant que la livraison s'est bien déroulée.

###  Le Producteur (Producer)
C'est l'offreur (souvent agricole). Ses interactions principales sont :
- **Catalogue Personnel** : Publication des offres, ajustement visuel des produits, définition de la quantité disponible et des contraintes (ex: Négociable ou non).
- **Gestion des offres** : Évaluation des propositions reçues via chat et conclusion sur le statut (Refus/Accord).
- **Logistique interne** : Notification du système de l'état d'avancement des préparations avant expédition.

###  Le Transporteur (Transporter)
Le maillon logistique vital à l'application. Ses capacités d'action :
- **Expédition** : Prise en responsabilité d'une commande `EN PRÉPARATION` pour la déplacer vers le statut `LIVRÉ`.
- (*Note : le système peut évoluer pour lui ajouter la consultation des Appels d'Offres de transport.*)

###  L'Administrateur (Admin)
Il assure le bon fonctionnement, la légalité et la modération du système global. 
- **Conformité & Modération** : Validation des profils sérieux (badges "Vérifié" / Processus KYC). Nettoyage des annonces frauduleuses.
- **Arbitrage des Litiges** : En cas de non-délivrance d'une marchandise validée par le client, l'administrateur prend la main pour décider du remboursement vers la balance de l'acheteur ou du déblocage forcé du compte séquestre vers le producteur. 
