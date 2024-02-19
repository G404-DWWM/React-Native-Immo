# Détails d'une annonce

Une fois l'enregistrement des annonces effectué, nous allons ajouter un écran de détails pour chaque annonce.

Voici un exemple de composant React Native qui affiche les détails d'une annonce récupérée depuis l'API REST, il est à adapter selon les données de votre Schema Mongoose et du front-end que vous avez choisi.

Il vous faut aussi ajouter dans la liste des annonces un bouton pour accéder à cet écran de détails avec l'ID de l'annonce en paramètre.

```js
import React, { useState, useEffect } from "react";
import { View, Text, Image, ScrollView, StyleSheet } from "react-native";

const DetailAnnonceScreen = ({ route }) => {
  const { annonceId } = route.params; // Récupère l'ID de l'annonce à partir des paramètres de navigation
  const [annonce, setAnnonce] = useState(null);

  useEffect(() => {
    // Récupère les détails de l'annonce depuis l'API
    const fetchAnnonceDetails = async () => {
      try {
        // Remplace 'http://exemple.com/annonces' par l'URL de ton API
        const response = await fetch(
          `http://exemple.com/annonces/${annonceId}`
        );
        const data = await response.json();
        if (response.ok) {
          setAnnonce(data);
        } else {
          // Gère le cas où la requête n'a pas réussi
          console.log(
            "Erreur lors de la récupération des détails de l'annonce"
          );
        }
      } catch (error) {
        console.error(error);
      }
    };

    fetchAnnonceDetails();
  }, [annonceId]);

  if (!annonce) {
    // Affiche un message de chargement tant que les données ne sont pas prêtes
    return <Text>Chargement...</Text>;
  }

  return (
    <ScrollView style={styles.container}>
      {annonce.photo && (
        <Image source={{ uri: annonce.photo }} style={styles.image} />
      )}
      <Text style={styles.titre}>{annonce.titre}</Text>
      <Text style={styles.description}>{annonce.description}</Text>
      {/* Tu peux ajouter d'autres détails ici, comme la localisation, en formatant les données comme souhaité */}
    </ScrollView>
  );
};

export default DetailAnnonceScreen;
```

Ce composant est incomplet, il te faudra ajouter le style et les détails de l'annonce que tu souhaites afficher.

**Dans le composant Annonces, tu peux ajouter un bouton pour naviguer vers cet écran de détails.**
Tu peux utiliser le composant `TouchableOpacity` de React Native pour créer un bouton dans la liste des annonces :

```js
import { TouchableOpacity } from "react-native";

<TouchableOpacity
  onPress={() => {
    navigation.navigate("DetailAnnonce", { annonceId: annonce._id });
  }}>
  <Text>{annonce.titre}</Text>
</TouchableOpacity>;
```
