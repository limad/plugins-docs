# Comment installer ce plugin ?

1. Rendez-vous sur le market Jeedom et installez le plugin **MyVaillant**.
![install1](https://limad.github.io/plugins-docs/plugin-MyVaillant/images/MyVaillant_doc1.PNG)

2. Une fois le plugin activé, renseignez vos informations de connexion dans l'onglet **Configuration** :

   - **Utilisateur** : le nom d'utilisateur de votre compte Vaillant (à ne pas confondre avec l'adresse e-mail).
   - **Mot de passe** : le mot de passe associé à ce compte.
   - **Emplacement du dispositif** : le pays de votre installation, identique à celui renseigné dans l'app mobile.
   - **Application utilisée** : **myVAILLANT** ou **MiGo Link**, selon l'application que vous utilisez sur votre smartphone.
   - **Objets par défaut** : l'objet Jeedom auquel seront rattachés les nouveaux équipements créés.

   *(*) Un compte Vaillant est indispensable pour accéder à l'API. Il est recommandé d'utiliser un compte dédié, différent de celui de votre smartphone (invitation possible depuis l'application mobile).*

3. Cliquez sur **Synchroniser** : le plugin se connecte à votre compte Vaillant et découvre automatiquement vos équipements (Home, Zones, ECS...).

   *En cas de simple perte de connexion (sans changement de mot de passe), utilisez plutôt **Synchroniser sans reconnexion** : elle réutilise la session existante sans redemander d'authentification complète.*

   ![install3](https://limad.github.io/plugins-docs/plugin-MyVaillant/images/MyVaillant_doc2.PNG)

4. En cas de besoin (changement de mot de passe, compte à réinitialiser...), le bouton **Se déconnecter** supprime les informations d'authentification stockées ; la prochaine synchronisation effectuera une connexion complète.
