# Enregistrement des annonces avec photo et géolocalisation

Pour enrichir tes annonces, l'intégration de fonctionnalités telles que l'ajout de photos et la géolocalisation offre une valeur ajoutée significative. Tu peux utiliser les API et bibliothèques natives de React Native pour accéder à la caméra et obtenir la position GPS :

```js
import * as ImagePicker from "expo-image-picker";
import * as Location from "expo-location";

// Exemple de fonction pour sélectionner une photo
const choisirPhoto = async () => {
  let resultat = await ImagePicker.launchImageLibraryAsync({
    mediaTypes: ImagePicker.MediaTypeOptions.All,
    allowsEditing: true,
    aspect: [4, 3],
    quality: 1,
  });

  console.log(resultat);
};

// Exemple de fonction pour obtenir la géolocalisation
const obtenirPosition = async () => {
  let { status } = await Location.requestForegroundPermissionsAsync();
  if (status !== "granted") {
    console.error("Permission de localisation refusée");
    return;
  }

  let location = await Location.getCurrentPositionAsync({});
  console.log(location);
};
```

### Intégration du formulaire d'ajout d'annonce

Pour permettre aux utilisateurs d'ajouter des annonces avec photo et géolocalisation, nous allons modifier notre modèle mangoose pour inclure ces nouvelles données. Ensuite, nous allons créer un formulaire d'ajout d'annonce dans notre application React Native.

1. **Modification du modèle Mongoose**

Votre serveur Express.js n'a pas encore de gestion de la photo et de la géolocalisation. Modifie ton modèle d'annonce dans Mongoose pour inclure des champs pour la photo et la géolocalisation. Un exemple de modèle pourrait ressembler à ceci :

```js
const annonceSchema = new mongoose.Schema({
  titre: { type: String, required: true },
  description: { type: String, required: true },
  photo: { type: String, required: false },
  localisation: {
    latitude: { type: Number, required: false },
    longitude: { type: Number, required: false },
  },
  // autres champs
});
```

2. **Création du formulaire d'ajout d'annonce**

créer un formulaire permettant aux utilisateurs d'entrer les informations de l'annonce, de prendre une photo via la caméra ou la galerie, et d'obtenir leur position actuelle.

```js
import React, { useState } from "react";
import { Button, TextInput, View } from "react-native";
// Importe les fonctions choisirPhoto et obtenirPosition créées précédemment

const FormAjoutAnnonce = () => {
  const [titre, setTitre] = useState("");
  const [description, setDescription] = useState("");
  const [photo, setPhoto] = useState(null);
  const [position, setPosition] = useState(null);

  // Fonctions pour gérer les évènements du formulaire ici

  return (
    <View>
      <TextInput
        placeholder="Titre de l'annonce"
        value={titre}
        onChangeText={setTitre}
      />
      <TextInput
        placeholder='Description'
        value={description}
        onChangeText={setDescription}
      />
      <Button
        title='Choisir une photo'
        onPress={() => {
          /* Appelle choisirPhoto ici */
        }}
      />
      <Button
        title='Utiliser ma position'
        onPress={() => {
          /* Appelle obtenirPosition ici */
        }}
      />
      /* Lorsque tout est prêt, ajoute un bouton pour soumettre le formulaire */
    </View>
  );
};
```

Il faut modifier les fonctions choisirPhoto et obtenirPosition pour
qu'elles return le résultat et le stockent dans les states photo et
position

3. **Envoi des données au serveur**

Lorsqu'un utilisateur soumet le formulaire, tu vas récupérer les données (titre, description, photo, localisation) et les envoyer à ton serveur Express.js via une requête POST sur la route du serveur express.js dédiée.

```js
import { Alert } from "react-native";

const soumettreAnnonce = async () => {
  // Assure-toi que les données nécessaires sont disponibles
  if (!titre || !description || !photo || !position) {
    Alert.alert("Erreur", "Tous les champs sont requis");
    return;
  }

  // Prépare les données pour l'envoi
  const formData = new FormData();
  formData.append("titre", titre);
  formData.append("description", description);
  formData.append("photo", {
    uri: photo.uri,
    type: "image/jpeg",
    name: "photo.jpg",
  });
  formData.append("localisation[latitude]", position.latitude.toString());
  formData.append("localisation[longitude]", position.longitude.toString());

  try {
    // Remplace 'http://exemple.com/annonces' par l'URL de ton API
    const response = await fetch("http://exemple.com/annonces", {
      method: "POST",
      body: formData,
      headers: {
        "Content-Type": "multipart/form-data",
      },
    });

    const json = await response.json();

    if (response.ok) {
      Alert.alert("Succès", "Annonce ajoutée avec succès");
      // Réinitialisation des états ou navigation vers un autre écran ici
    } else {
      Alert.alert("Erreur", json.message || "Quelque chose s'est mal passé");
    }
  } catch (error) {
    console.error(error);
    Alert.alert("Erreur", "Impossible de soumettre l'annonce");
  }
};
```

Voilà il n'y a plus qu'à bien assembler tout ça et vérifier que tout fonctionne coté serveur et coté client. La logique de gestion des erreurs et des succès est à adapter selon les besoins de ton application.
