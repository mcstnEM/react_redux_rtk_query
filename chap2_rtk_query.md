# RTK Query 

## Motivation de l'utilisation de createSlice et createApi

Dans Redux Toolkit, un "slice" est une partie de votre état global Redux qui est gérée par un ensemble de reducers, actions et éventuellement des sélecteurs. 

Le terme "slice" fait référence à la découpe ou à la portion spécifique de l'état global que le slice gère.


La fonction createApi de Redux Toolkit a été introduite dans la version 1.5.0 de Redux Toolkit. Aujourd'hui en février 2024 on en est à la version 2.1

Avant l'introduction de **createApi**, la configuration de l'intégration d'une API avec Redux impliquait souvent la création manuelle de plusieurs slices, reducers et actions, ce qui pouvait conduire à un code répétitif et difficile à maintenir. 

>[!NOTE]
> **createApi** vise à simplifier ce processus en abstrayant la configuration sous une forme plus concise et déclarative.

1. Réduction de la boilerplate (réduction du code redondant et verbeux)
1. Approche déclarative. (plus intuitif et lisible)
1. Intégration transparente avec les slices. `createApi` génère automatiquement des slices, des reducers et des actions 
1. Réutilisabilité et maintenabilité 

## Rappel de la notion de createSlice

Dans Redux Toolkit, un "slice" est une partie de notre état global Redux qui est gérée par un ensemble de reducers, actions et éventuellement des sélecteurs.

Définition du slice, reducers et actions à partir d'un exemple.

```js
import { createSlice } from '@reduxjs/toolkit';

// Création du slice
const counterSlice = createSlice({
  name: 'counter',
  initialState: 0,
  // le reducer
  reducers: {
    // Action pour incrémenter le compteur
    increment: (state) => {
      return state + 1;
    },
    // Action pour décrémenter le compteur
    decrement: (state) => {
      return state - 1;
    },
  },
});

// Exportation des actions
export const { increment, decrement } = counterSlice.actions;

// Exportation du reducer généré automatiquement
export default counterSlice.reducer;

```

Rappelons ce qu'est un reducer en soi :

>[!NOTE]
> Un reducer est **une fonction pure** qui prend l'état actuel de l'application et une action comme arguments, puis retourne un nouveau state.

Une fonction pure est déterministe, elle retournera pour une même entrée, toujours la même sortie. 

Nous rappelons avec un exemple ce qu'est une fonction pure / non pure

```js
// Fonction impure (avec effet secondaire)
let globalVariable = 10;

const impureFunction = (x) => {
  globalVariable += x; // Modification d'une variable globale (effet secondaire)
  return globalVariable;
};

console.log(impureFunction(5)); // Sortie : 15
console.log(impureFunction(3)); // Sortie : 18

// Fonction pure (sans effet secondaire)
const pureFunction = (x, y) => {
  return x + y; // Aucune modification d'état externe
};

console.log(pureFunction(5, 3)); // Sortie : 8
console.log(pureFunction(2, 7)); // Sortie : 9
```

## API Pokemon

### Organisation structure des dossiers et fichiers

```txt
src
|-- components
|   |-- PokemonDetails
|       |-- PokemonDetails.jsx  // Composant React utilisant l'API
|-- services
|   |-- api
|       |-- pokemonApi.jsx     // Configuration de l'API avec createApi
|       |-- endpoints
|           |-- pokemon.jsx    // Module pour l'endpoint Pokemon
|           |-- ability.jsx    // Module pour l'endpoint Ability
|           |-- move.jsx       // Module pour l'endpoint Move
|           |-- ...
|   |-- store
|       |-- store.jsx          // Configuration du store Redux
|       |-- rootReducer.jsx    // Combine tous les reducers
|-- index.jsx                  // Point d'entrée de l'application
|-- App.jsx                    // Composant principal de l'application

```

L'API Pokémon officielle offre plusieurs endpoints pour accéder aux informations sur les Pokémon. Voici quelques exemples d'endpoints couramment utilisés :

1. **Liste de tous les Pokémon :**
   - Endpoint : `https://pokeapi.co/api/v2/pokemon`
   - Méthode : GET
   - Description : Récupère une liste de tous les Pokémon avec leurs noms et identifiants.

2. **Détails d'un Pokémon spécifique par son ID ou son nom :**
   - Endpoint : `https://pokeapi.co/api/v2/pokemon/{id or name}`
   - Méthode : GET
   - Description : Récupère les détails d'un Pokémon spécifique en fonction de son identifiant (ID) ou de son nom.

3. **Liste de tous les types de Pokémon :**
   - Endpoint : `https://pokeapi.co/api/v2/type`
   - Méthode : GET
   - Description : Récupère une liste de tous les types de Pokémon.

4. **Détails d'un type spécifique par son ID ou son nom :**
   - Endpoint : `https://pokeapi.co/api/v2/type/{id or name}`
   - Méthode : GET
   - Description : Récupère les détails d'un type spécifique en fonction de son identifiant (ID) ou de son nom.

5. **Liste de toutes les capacités de Pokémon :**
   - Endpoint : `https://pokeapi.co/api/v2/ability`
   - Méthode : GET
   - Description : Récupère une liste de toutes les capacités de Pokémon.

6. **Détails d'une capacité spécifique par son ID ou son nom :**
   - Endpoint : `https://pokeapi.co/api/v2/ability/{id or name}`
   - Méthode : GET
   - Description : Récupère les détails d'une capacité spécifique en fonction de son identifiant (ID) ou de son nom.

Voir également [Documentation API Pokémon](https://pokeapi.co/docs/v2).


## Utilisation de createApi

Voici un exemple détaillé avec fetchBaseQuery, détaillons les endpoints utilisés 

1. `getAllPokemon`: Récupère la liste de tous les Pokémon.
1. `getAbilityById`: Récupère les détails d'une capacité (ability) spécifique.
1. `getMoveById`: Récupère les détails d'une attaque (move) spécifique.
1. `getPokemonSpeciesById`: Récupère les détails d'une espèce de Pokémon spécifique.
   

- Le service

```js
export const pokemonApi = createApi({
  reducerPath: 'pokemonApi',
  baseQuery: fetchBaseQuery({ baseUrl: 'https://pokeapi.co/api/v2/' }),
  endpoints: (builder) => ({
    getPokemonById: builder.query({
      query: (id) => `pokemon/${id}`,
    }),
    getAllPokemon: builder.query({
      query: () => 'pokemon',
    }),
    getAbilityById: builder.query({
      query: (id) => `ability/${id}`,
    }),
    getMoveById: builder.query({
      query: (id) => `move/${id}`,
    }),
    getPokemonSpeciesById: builder.query({
      query: (id) => `pokemon-species/${id}`,
    }),
  }),
});

export const {
  useGetPokemonByIdQuery,
  useGetAllPokemonQuery,
  useGetAbilityByIdQuery,
  useGetMoveByIdQuery,
  useGetPokemonSpeciesByIdQuery,
} = pokemonApi;

// Utilisation dans un composant React
const PokemonDetails = ({ pokemonId }) => {
  const { data: pokemon, isLoading, isError } = useGetPokemonByIdQuery(pokemonId);

  if (isLoading) {
    return <p>Chargement en cours...</p>;
  }

  if (isError) {
    return <p>Une erreur s'est produite lors du chargement des données Pokémon.</p>;
  }

  if (!pokemon) {
    return null;
  }

  // Afficher les détails du Pokémon
  console.log(`Nom: ${pokemon.name}`);
  console.log(`ID: ${pokemon.id}`);
  console.log('Types:', pokemon.types.map((type) => type.type.name).join(', '));
  console.log('Capacités:', pokemon.abilities.map((ability) => ability.ability.name).join(', '));

  return (
    <div>
      <h2>{pokemon.name}</h2>
      <p>ID: {pokemon.id}</p>
      <p>Types: {pokemon.types.map((type) => type.type.name).join(', ')}</p>
      <p>Capacités: {pokemon.abilities.map((ability) => ability.ability.name).join(', ')}</p>
    </div>
  );
};

export default PokemonDetails;
```

Dans cet exemple:

- Nous utilisons `createApi` pour créer une API contenant un point d'extrémité (`getPokemonById`) qui utilise `fetchBaseQuery` pour effectuer la requête vers l'API PokéAPI.

- Les actions générées automatiquement, telles que `useGetPokemonByIdQuery`, sont exportées et utilisées dans le composant React (`PokemonDetails`). 

>[!NOTE]  
>Ces hooks sont générés pour chaque point d'extrémité que vous définissez.

- Nous utilisons le hook `useGetPokemonByIdQuery` pour récupérer les données du Pokémon avec son ID spécifié. Le hook gère automatiquement le cycle de vie de la requête et fournit les résultats dans les propriétés `data`, `isLoading`, et `isError`.

- En fonction de l'état de la requête, nous affichons le contenu approprié dans le composant React.

Cette approche simplifie la gestion des requêtes API dans une application Redux en générant automatiquement les actions et les reducers nécessaires.