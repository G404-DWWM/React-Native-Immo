# Utiliser React Native depuis notre application immobilière en express.js

## Introduction

Dans ce cour, nous allons intégrer des fonctionnalités de React Native dans une application immobilière construite avec Express.js. Nous allons nous concentrer sur l'intégration de l'appareil photo et de la géolocalisation, deux fonctionnalités utiles pour une application immobilière.

## Prérequis

- Avoir réalisé le cours sur la création d'une application immobilière avec Express.js : https://github.com/G404-DWWM/Nodejs-NoSQL
- Avoir des connaissances de base en React Native : https://github.com/G404-DWWM/react-native-intro

# Transformation du TP Express Immo en API REST

### Changement du rendu Mustache en JSON

Dans le TP Express Immo, nous avons utilisé le moteur de rendu Mustache pour afficher les données récupérées via Mangoose. Pour transformer l'application en API REST, nous devons modifier le rendu Mustache en JSON.

```js
// Ancien code
router.get("/annonces", (req, res) => {
  Annonce.find({}, (err, annonces) => {
    res.render("listeAnnonces", { annonces });
  });
});

// Nouveau code
router.get("/annonces", (req, res) => {
  Annonce.find({}, (err, annonces) => {
    if (err) {
      res.status(500).json({ message: "Une erreur est survenue" });
    } else {
      res.json(annonces);
    }
  });
});
```

### Sécurisation de l'API REST (optionnel)

**1. Authentification et Autorisation**
Utilise JWT (JSON Web Tokens) pour sécuriser l'API. JWT permet de vérifier l'identité de l'utilisateur et garantit que les requêtes API sont effectuées par des entités autorisées. Tu peux intégrer un système d'authentification où les utilisateurs reçoivent un token après la connexion, qu'ils devront ensuite inclure dans les en-têtes des requêtes API pour accéder aux ressources protégées.

pour faire simple pour le moment, un seul utilisateur est autorisé à accéder à l'API : la future application React Native.

`npm install jsonwebtoken`
https://www.npmjs.com/package/jsonwebtoken

```js
// Exemple de middleware d'authentification
const jwt = require("jsonwebtoken");

const authMiddleware = (req, res, next) => {
  try {
    const token = req.headers.authorization.split(" ")[1]; // Bearer <token>
    const decodedToken = jwt.verify(token, "SECRET_KEY");
    const userId = decodedToken.userId;
    if (req.body.userId && req.body.userId !== userId) {
      throw "User ID non valable";
    } else {
      next();
    }
  } catch {
    res.status(401).json({
      error: new Error("Invalid request!"),
    });
  }
};
```

Pour une approche plus simple de la logique d'authentification, tu peux utiliser le middleware `express-jwt` qui vérifie automatiquement les tokens JWT.

https://www.npmjs.com/package/express-jwt
`npm install express-jwt`

```js
const expressJwt = require("express-jwt");

app.use(
  expressJwt({ secret: "toto1234", algorithms: ["HS256"] }).unless({
    path: [
      "/chemin/sans/authentification",
      "/autre/chemin/sans/authentification",
    ],
  })
);
```

**2. Limitation des requêtes**

Pour éviter les attaques par force brute, tu peux limiter le nombre de requêtes par seconde à l'API. Tu peux utiliser le middleware `express-rate-limit` pour limiter le nombre de requêtes par IP.

https://www.npmjs.com/package/express-rate-limit
`npm install express-rate-limit`

```js
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limite chaque IP à 100 requêtes par windowMs
});

app.use(limiter);
```

## Test de l'API REST

Pour tester l'API REST, tu peux utiliser des outils comme Postman ou Insomnia. Ces outils te permettent de tester les requêtes HTTP et de vérifier les réponses de l'API.

A partir de ces modifications du GET des annonces, Tu dois maintenant être capable de récupérer les annonces en JSON en utilisant l'URL `http://localhost:3000/annonces`.

# Création de l'application React Native

[la suite ici](./react-native-immo.md)
