# Ajout de la logique d'authentification JWT

pour intégrer la gestion du JWT dans ton application React Native lors de la récupération des annonces, tu dois ajuster ta fonction fetch pour inclure le token JWT dans les en-têtes de ta requête. Cela permet de s'assurer que ta requête est bien authentifiée. Voici comment tu pourrais le faire, en supposant que tu as déjà le token JWT stocké quelque part (par exemple, dans le stockage local ou contexte global de l'application) :

```js
import React, { useEffect, useState } from "react";
import { View, Text } from "react-native";
// Assume que AsyncStorage ou une méthode similaire est utilisée pour stocker le JWT
import AsyncStorage from "@react-native-async-storage/async-storage";
//

const Annonces = () => {
  const [annonces, setAnnonces] = useState([]);

  useEffect(() => {
    // Récupère le token JWT stocké
    AsyncStorage.getItem("jwtToken")
      .then((token) => {
        // Intègre le token JWT dans les en-têtes de la requête
        return fetch("http://localhost:3000/annonces", {
          method: "GET",
          headers: {
            Authorization: `Bearer ${token}`,
          },
        });
      })
      .then((response) => response.json())
      .then((data) => setAnnonces(data))
      .catch((error) =>
        console.error("Erreur lors de la récupération des annonces:", error)
      );
  }, []);

  return (
    <View>
      {annonces.map((annonce) => (
        <Text key={annonce._id}>{annonce.titre}</Text>
      ))}
    </View>
  );
};

export default Annonces;
```

Dans le développement d'applications mobiles, sécuriser les échanges d'informations entre l'appareil de l'utilisateur et le serveur est primordial. Le JWT (JSON Web Token) est un standard ouvert qui définit une manière compacte et autonome d'échanger des informations de manière sécurisée entre parties.

Dans le contexte de notre application React Native, nous utilisons le JWT pour authentifier les requêtes envoyées par l'utilisateur (l'appli React-Native) vers notre serveur (express.js). Cela signifie que chaque fois que l'utilisateur tente d'accéder à des ressources protégées, le serveur peut vérifier l'identité de l'utilisateur grâce au token JWT.

Pour que ce mécanisme fonctionne, le token JWT doit être stocké de manière sécurisée et persistante sur l'appareil de l'utilisateur après qu'il se soit authentifié.
**Dans notre cas, nous n'avons pas d'authentification utilisateur, nous stockons le token JWT directement dans l'application React Native.**
C'est là qu'intervient AsyncStorage, une instance de stockage clé-valeur asynchrone et persistante qui fait partie de l’écosystème de React Native. Grâce à AsyncStorage, nous pouvons stocker le token JWT de manière sécurisée sur l'appareil, le récupérer pour l'attacher à chaque requête et ainsi confirmer l'authenticité de ces demandes.

https://react-native-async-storage.github.io/async-storage/
`npm install @react-native-async-storage/async-storage`

```js
AsyncStorage.setItem("jwtToken", "tonJwtTokenIci").catch((error) =>
  console.error("Erreur lors de l'enregistrement du token:", error)
);
```

Avec cette logique d'authentification JWT, tu devrais être capable de sécuriser l'accès à l'API REST depuis ton application React Native.
