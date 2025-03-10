<template>
  <header>
    <img src="/images/logo-les-bonnes-pieces.png" alt="LOGO">
    <h1>Les Bonnes Pièces</h1>
  </header>

  <main>
    <!-- Filtres -->
    <section class="filtres">
      <h3>Filtres</h3>
      <input type="text" v-model="recherche" placeholder="Rechercher une pièce...">
      
      <select v-model="categorie">
        <option value="">Toutes les catégories</option>
        <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
      </select>

      <label>
        <input type="checkbox" v-model="stockSeulement"> Uniquement en stock
      </label>

      <button @click="trierParPrix">Trier par prix ↑↓</button>
    </section>

    <!-- Liste des pièces -->
    <section class="fiches">
      <div v-for="piece in piecesFiltres" :key="piece.id" class="fiche">
        <h2>{{ piece.nom }}</h2>
        <img :src="piece.image" alt="Image de la pièce">
        <p>Catégorie : {{ piece.categorie }}</p>
        <p>Prix : {{ piece.prix }} €</p>
        <p v-if="piece.disponible">✅ En stock</p>
        <p v-else>❌ Rupture de stock</p>
        <button @click="ajouterAuPanier(piece)">Ajouter au panier 🛒</button>
      </div>
    </section>

    <!-- Panier -->
    <section class="panier">
      <h3>🛒 Panier</h3>
      <ul>
        <li v-for="(item, index) in panier" :key="index">
          {{ item.nom }} - {{ item.prix }} €
          <button @click="supprimerDuPanier(index)">❌ Retirer</button>
        </li>
      </ul>
      <p v-if="panier.length === 0">Votre panier est vide.</p>
      <p v-else><strong>Total :</strong> {{ totalPanier }} €</p>
    </section>
  </main>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      pieces: [], // Stocke les pièces récupérées
      panier: [], // Stocke les pièces ajoutées au panier
      recherche: "",
      categorie: "",
      stockSeulement: false,
      triCroissant: true // Permet d'alterner le tri des prix
    };
  },
  mounted() {
    // Récupérer les données depuis JSON Server
    fetch("http://localhost:3000/pieces")
      .then(response => response.json())
      .then(data => {
        this.pieces = data;
      })
      .catch(error => console.error("Erreur de chargement :", error));
  },
  computed: {
    // Filtrage dynamique
    piecesFiltres() {
      return this.pieces.filter(piece => {
        return (
          piece.nom.toLowerCase().includes(this.recherche.toLowerCase()) &&
          (this.categorie === "" || piece.categorie === this.categorie) &&
          (!this.stockSeulement || piece.disponible)
        );
      });
    },
    // Liste unique des catégories
    categories() {
      return [...new Set(this.pieces.map(piece => piece.categorie))];
    },
    // Calcul du total du panier
    totalPanier() {
      return this.panier.reduce((total, item) => total + item.prix, 0);
    }
  },
  methods: {
    // Ajouter un article au panier
    ajouterAuPanier(piece) {
      this.panier.push(piece);
      alert(`${piece.nom} ajouté au panier ! 🛒`);
    },
    // Supprimer un article du panier
    supprimerDuPanier(index) {
      this.panier.splice(index, 1);
    },
    // Trier les pièces par prix (croissant/décroissant)
    trierParPrix() {
      this.pieces.sort((a, b) => this.triCroissant ? a.prix - b.prix : b.prix - a.prix);
      this.triCroissant = !this.triCroissant;
    }
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat&display=swap');

body {
  max-width: 1200px;
  margin: auto;
  padding: 16px;
  font-family: 'Montserrat', sans-serif;
}

header {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin-bottom: 16px;
  padding: 8px;
  background-color: #7451eb;
  text-align: center;
  color: white;
}

header img {
  height: 60px;
  margin-left: 1rem;
}

header h1 {
  flex-grow: 1;
}

main {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

/* Filtres */
.filtres {
  border-radius: 4px;
  box-shadow: 0px 0px 4px gray;
  margin: 8px;
  padding: 8px;
  min-width: 300px;
  min-height: 400px;
  background: #f8f8f8;
}

.filtres input, .filtres select {
  width: 100%;
  margin: 5px 0;
  padding: 8px;
}

.filtres h3 {
  text-align: center;
}

/* Fiches */
.fiches {
  flex: 1;
  margin: 8px;
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.fiche {
  border: 1px solid #ddd;
  padding: 10px;
  width: 250px;
  box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
  border-radius: 5px;
  background: white;
  text-align: center;
}

.fiche img {
  max-width: 200px;
}

.fiche p {
  margin-top: 4px;
  margin-bottom: 4px;
}

button {
  background-color: #7451eb;
  color: white;
  padding: 5px 10px;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #5a3cc8;
}

/* Panier */
.panier {
  width: 300px;
  border-radius: 4px;
  box-shadow: 0px 0px 4px gray;
  padding: 8px;
  background: #f8f8f8;
}

.panier ul {
  list-style: none;
  padding: 0;
}

.panier li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 5px 0;
  padding: 5px;
  border-bottom: 1px solid #ddd;
}

.panier button {
  background-color: red;
}
</style>
