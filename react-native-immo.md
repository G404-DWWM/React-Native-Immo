# Création de l'application React Native

Pour créer l'application React Native, nous allons utiliser Expo, comme pour les cours précédents.

```bash
npm install -g expo-cli
expo init React-Native-Immo
cd React-Native-Immo
```

**Vous pouvez faire l'application avec ou sans la sécurisation si cela vous semble trop compliqué pour le moment.**

Le but est de récupérer les annonces depuis l'API REST et de les afficher dans l'application React Native.

## Récupération des annonces en React Native

Pour récupérer les annonces depuis l'API REST (sans sécurisation), nous allons utiliser la fonction `fetch` de JavaScript.

```js
// Ce composant affiche la liste des annonces récupérées depuis une API.
import React, { useEffect, useState } from "react";
import { View, Text } from "react-native";

const Annonces = () => {
  const [annonces, setAnnonces] = useState([]);

  useEffect(() => {
    // Envoie une requête GET à l'API
    fetch("http://localhost:3000/annonces")
      .then((response) => response.json())
      .then((data) => setAnnonces(data));
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

à partir de ce code, tu devrais être capable d'afficher les annonces récupérées depuis l'API REST dans ton application React Native.

## Sécurisation de l'API REST

Ajoute la gestion du [JsonWebToken](./authentification-JWT.md) qui sécurise l'accès à l'API (optionnel)

## Intégration de Frameworks Front-End pour Enrichir l’UI

Après avoir récupéré les annonces depuis l'API REST, il est temps de les afficher de manière plus interactive et attrayante. Pour ce faire, tu peux te tourner vers plusieurs bibliothèques UI populaires dans l'écosystème React Native.

### Utilisation de React Navigation

[React Navigation](https://reactnavigation.org/)

Pour naviguer entre différentes vues de ton application, React Navigation offre une solution robuste et flexible. Tu peux par exemple créer un stack navigator pour afficher un écran détaillé lorsque l'utilisateur clique sur une annonce :

```js
import { createStackNavigator } from "@react-navigation/stack";
import { NavigationContainer } from "@react-navigation/native";
const Stack = createStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name='Home' component={Annonces} />
        // Ajoute tes autres écrans ici
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

### Amélioration de l’UI avec NativeBase ou React Native Elements

Pour embellir les annonces et rendre l'interface plus conviviale, tu peux utiliser NativeBase ou React Native Elements. Ces bibliothèques offrent une multitude de composants pré-construits et personnalisables :

[NativeBase](https://nativebase.io/)

```js
// Exemple avec NativeBase
import { Box, Text } from "native-base";

return (
  <Box>
    {annonces.map((annonce) => (
      <Text key={annonce._id}>{annonce.titre}</Text>
    ))}
  </Box>
);
```

Ou :
[React Native Elements](https://reactnativeelements.com/)

```js
// Exemple avec React Native Elements
import { ListItem } from "react-native-elements";

return (
  <View>
    {annonces.map((annonce, i) => (
      <ListItem key={i} bottomDivider>
        <ListItem.Content>
          <ListItem.Title>{annonce.titre}</ListItem.Title>
          // Ajoute d’autres détails ici
        </ListItem.Content>
      </ListItem>
    ))}
  </View>
);
```

# Enregistrement des annonces

[enregistrement des annonces avec photo et géolocalisation](./enregistrement-annonce.md)

# Affichage des détails d'une annonce

[affichage des détails d'une annonce](./details-annonce.md)

# Conclusion

Avec ces étapes, tu devrais être capable de créer une application React Native qui communique avec une API REST pour afficher des annonces immobilières. Tu as également appris à sécuriser l'API REST avec un token JWT et à enrichir l'interface utilisateur avec des bibliothèques populaires.

Pour aller plus loin, tu peux ajouter des fonctionnalités telles que la recherche d'annonces, la gestion des favoris, ou la mise en place de notifications push pour informer les utilisateurs des nouvelles annonces.

Bon développement !
