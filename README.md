# GMAO PRO

Application de gestion de maintenance développée avec Laravel 13, Blade et Tailwind CSS 4.

## Fonctionnalités

- Connexion sécurisée par nom d’utilisateur, limitation des tentatives et sessions Laravel.
- Tableau de bord avec indicateurs du parc et maintenances à suivre.
- Suivi individuel des appareils : chaque unité créée reçoit son propre code unique et son numéro de série peut être renseigné.
- Affectation groupée de plusieurs appareils identifiés, avec une ligne d’historique par code et déduction du seul stock affecté.
- Maintenances rattachées à un appareil précis et à son technicien.
- Alertes du tableau de bord calculées sur le stock disponible par désignation, catégorie, marque et modèle.
- Module Rapports : inventaire, mouvements par période, seuils d’alerte et maintenances, export CSV et impression/enregistrement PDF depuis le navigateur.
- Gestion des comptes et consultation du journal réservées aux administrateurs.
- Modification du profil et de la photo, journalisation des actions et interface responsive en français.

## Prérequis

- PHP 8.3 ou supérieur, avec les extensions PDO MySQL, Mbstring, OpenSSL, Fileinfo et XML.
- Composer 2.
- Node.js et npm.
- MySQL ou MariaDB.

## Installation locale (WAMP)

1. Créez la base `gmao_biomedical` dans phpMyAdmin. Sauvegardez toute base existante avant d’y appliquer les migrations.
2. Copiez `.env.example` vers `.env` et renseignez les paramètres `DB_*` de votre serveur MySQL.
3. Installez les dépendances et générez la clé de l’application :

   ```powershell
   composer install
   Copy-Item .env.example .env
   php artisan key:generate
   npm install
   npm run build
   ```

4. Créez les tables :

   ```powershell
   php artisan migrate
   ```

   Les migrations reconnaissent les tables héritées du projet PHP, créent les tables absentes et ajoutent les colonnes de compatibilité manquantes. Les anciennes fiches multi-unités sans affectation ni maintenance sont automatiquement séparées en appareils unitaires avec de nouveaux codes. Les fiches ayant un historique sont conservées sans modification afin de ne pas réattribuer leurs opérations à de mauvais appareils; leur individualisation doit être faite après vérification de l’historique. Faites une sauvegarde avant toute migration de données existantes.

Lors d’une création en quantité, l’application génère une fiche par appareil. Pour les affectations, sélectionnez les codes des seuls appareils concernés; la disponibilité est recalculée à partir des états unitaires. Les seuils d’alerte s’appliquent au groupe de matériel (désignation, catégorie, marque et modèle), pas à chaque code isolément.

5. Pour créer le premier administrateur, définissez `ADMIN_USERNAME` et un `ADMIN_PASSWORD` d’au moins 12 caractères dans `.env`, puis lancez :

   ```powershell
   php artisan db:seed --class=AdminUserSeeder
   ```

   Le seeder ne remplace jamais le mot de passe d’un compte existant. Si la base contient déjà l’ancien compte `admin`, connectez-vous et changez son mot de passe depuis le profil.

6. Lancez l’application :

   ```powershell
   php artisan serve
   ```

   Ouvrez ensuite <http://127.0.0.1:8000>. Avec Apache/WAMP, configurez le DocumentRoot du VirtualHost vers le dossier `public`.

## Développement

```powershell
npm run dev
php artisan test
```

Les actions d’administration sont protégées par le rôle `admin`. Les nouveaux mots de passe sont hachés par Laravel et les formulaires utilisent la protection CSRF intégrée.
