# Architecture & Diagramme de Classes (Mobile)

 le diagramme de classes pour l'application mobile AgriConnect.

## 1. Diagramme UML 

```mermaid
classDiagram
    %% Déclaration des Classes Principales
    class UserModel {
        +String id
        +String phone
        +String name
        +String role
        +String status
        +String? email
        +String? avatarUrl
        +String? bio
        +String? city
        +String? region
        +double trustScore
        +bool isVerified
        +bool isOnline
        +double walletBalance
    }

    class ProducerProfile {
        +String? farmName
        +double? farmSizeHa
        +List~String~ mainCrops
        +int totalSales
        +double totalRevenue
    }

    class BuyerProfile {
        +String? companyName
        +String? businessType
        +int totalPurchases
        +double totalSpent
    }

    class TransporterProfile {
        +String? vehicleType
        +double? capacityKg
        +int totalDeliveries
        +double totalRevenue
    }

    class ProductModel {
        +String id
        +String producerId
        +String categoryId
        +String name
        +String? variety
        +String? description
        +List~String~ images
        +double quantityKg
        +double unitPrice
        +String currency
        +double minOrderKg
        +String qualityGrade
        +String status
        +bool isNegotiable
        +bool isPremium
        +String? city
        +String? region
        +double? latitude
        +double? longitude
        +int viewsCount
        +int ordersCount
        +bool isFavorited
        +int favoritesCount
        +DateTime createdAt
    }

    class CategoryModel {
        +String id
        +String name
        +String emoji
        +String slug
    }

    class OrderModel {
        +String id
        +String orderNumber
        +String buyerId
        +String producerId
        +String productId
        +double quantityKg
        +double? agreedPrice
        +double? proposedPrice
        +double? totalAmount
        +double? commission
        +String status
        +String? deliveryAddress
        +String? notes
        +DateTime? deliveryDate
        +DateTime? completedAt
        +DateTime createdAt
        +DateTime updatedAt
    }

    class OrderStatusHistory {
        +String status
        +String? note
        +DateTime createdAt
    }

    class PaymentModel {
        +String id
        +String status
        +String operator
        +double amount
        +double commission
    }

    class MessageModel {
        +String id
        +String orderId
        +String senderId
        +String receiverId
        +String content
        +String type
        +Map offerData
        +bool isRead
        +DateTime createdAt
    }

    %% Relations entre les classes
    UserModel "1" *-- "0..1" ProducerProfile : Composition
    UserModel "1" *-- "0..1" BuyerProfile : Composition
    UserModel "1" *-- "0..1" TransporterProfile : Composition

    ProductModel "     *" --> "1" UserModel : appartient à (producer)
    ProductModel "*" --> "1" CategoryModel : appartient à

    OrderModel "*" --> "1" UserModel : a pour (buyer)
    OrderModel "*" --> "1" UserModel : a pour (producer)
    OrderModel "*" --> "1" ProductModel : concerne
    OrderModel "1" *-- "0..*" OrderStatusHistory : contient (historique)
    OrderModel "1" *-- "0..1" PaymentModel : inclut

    MessageModel "*" --> "1" UserModel : envoyé par (sender)
    MessageModel "*" --> "0..1" OrderModel : appartient à (négociation)
```

---

## 2. Description des Entités

###  Entité Utilisateur
Le cœur du système d'authentification et de rôles multicartes (Producteur, Acheteur, Transporteur). Il gère les données globales de l'utilisateur (nom, avatar, solde du portefeuille) et inclut par structure de composition 3 sous-profils spécifiques :
- **Profil Producteur** : Stocke les informations liées à son exploitation (surface, cultures, revenus).
- **Profil Acheteur** : Stocke sa typologie d'acheteur (type d'entreprise, total dépensé).
- **Profil Transporteur** : Stocke ses informations logistiques (type de véhicule, capacité, livraisons).

###  Entité Produit & Catégorie
Représente un produit physique mis en vente. 
Liaison clé : Il est lié à un Utilisateur (le producteur) et à une Catégorie (pour la structuration du catalogue).
Particularités : Gère les concepts de quantité minimale, de négociation (via son statut de négociabilité) et de grade de qualité (Trust Metrics).

###  Entité Commande
L'entité transactionnelle centrale.
- Lise un acheteur, un producteur et un produit.
- Est dotée d'une ligne de vie via son historique de statuts.
- Intègre explicitement un objet optionnel de Paiement si le cycle est facturé ou en séquestre.

###  Entité Message
Support de la communication directe ou contextuelle.
Particularité : Accepte des données d'offre optionnelles et un identifiant de commande optionnel. Si une négociation de gré-à-gré (pour une offre) aboutit, l'ordre de transaction complet qui en découle est généré tout en gardant cette trace.
