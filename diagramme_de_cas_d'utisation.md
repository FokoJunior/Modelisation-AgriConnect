# Diagramme des Cas d'Utilisation

Ce diagramme illustre les interactions des différents acteurs (Acheteur, Producteur, Transporteur, et Administrateur) avec les fonctionnalités principales de la plateforme AgriConnect.

## 1. Diagramme 

```mermaid
graph LR
    %% Style pour les Acteurs (Simulés par des nœuds circulaires)
    classDef actor fill:#e1bee7,stroke:#8e24aa,stroke-width:2px,color:#000
    classDef usecase fill:#e3f2fd,stroke:#1e88e5,stroke-width:2px,rx:20px,ry:20px

    %% Déclaration des Acteurs
    Buyer((Acheteur)):::actor
    Producer((Producteur)):::actor
    Transporter((Transporteur)):::actor
    Admin((Administrateur)):::actor

    %% --- Frontière du Système (Fonctionnalités) ---
    subgraph AgriConnect System ["AgriConnect (Plateforme Mobile & Web)"]
        direction TB
        
        %% Fonctionnalités Communes
        UC_Auth(Création de compte & Auth Mobile)
        UC_Wallet(Gérer son Portefeuille & Retraits)
        
        %% Fonctionnalités Acheteur
        UC_Search(Rechercher & Filtrer les produits)
        UC_Order(Acheter / Passer Commande)
        UC_Negotiate(Négocier en P2P)
        UC_Confirm(Confirmer la Réception)
        
        %% Fonctionnalités Producteur
        UC_Publish(Créer/Publier un produit)
        UC_Manage(Accepter/Refuser les offres)
        UC_Prepare(Préparer la commande)
        
        %% Fonctionnalités Transporteur
        UC_Deliver(Livrer la marchandise)
        
        %% Fonctionnalités Administrateur
        UC_Mod(Modérer le catalogue & Utilisateurs)
        UC_KYC(Procéder à la vérification KYC)
        UC_Dispute(Résoudre les Litiges)
        UC_Refund(Forcer le remboursement / Déblocage)
    end

    %% --- Assignation des classes de style ---
    class UC_Auth,UC_Wallet,UC_Search,UC_Order,UC_Negotiate,UC_Confirm,UC_Publish,UC_Manage,UC_Prepare,UC_Deliver,UC_Mod,UC_KYC,UC_Dispute,UC_Refund usecase

    %% --- Liens Acheteur ---
    Buyer --> UC_Auth
    Buyer --> UC_Wallet
    Buyer --> UC_Search
    Buyer --> UC_Order
    Buyer --> UC_Negotiate
    Buyer --> UC_Confirm

    %% --- Liens Producteur ---
    Producer --> UC_Auth
    Producer --> UC_Wallet
    Producer --> UC_Publish
    Producer --> UC_Manage
    Producer --> UC_Prepare

    %% --- Liens Transporteur ---
    Transporter --> UC_Auth
    Transporter --> UC_Wallet
    Transporter --> UC_Deliver

    %% --- Liens Administrateur ---
    Admin --> UC_Mod
    Admin --> UC_KYC
    Admin --> UC_Dispute
    Admin --> UC_Refund
```

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
