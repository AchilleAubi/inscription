<template>
  <div>
    <h1>{{ conferenceTitle }}</h1>
    <h2>{{ conferenceSubtitle }}</h2>

    <div v-if="showErrors" class="error">
      <p>Veuillez remplir les champs obligatoires :</p>
      <ul>
        <li v-if="!firstName">Prénom</li>
        <li v-if="!lastName">Nom</li>
        <li v-if="!email">Email</li>
        <li v-if="!gender">Sexe</li>
        <li v-if="!institution">Institution</li>
        <li v-if="!address">Adresse</li>
        <li v-if="!country">Pays</li>
        <li v-if="!city">Ville</li>
        <li v-if="!registrationType">Type d'inscription</li>
        <li v-if="!accommodation">Hébergement</li>
      </ul>
    </div>

    <form @submit.prevent="submitForm">
      <label>Prénom<strong>*</strong>:</label>
      <input type="text" v-model="firstName" required :disabled="showSummary" />

      <label>Nom<strong>*</strong>:</label>
      <input type="text" v-model="lastName" required :disabled="showSummary" />

      <label>Email<strong>*</strong>:</label>
      <input type="email" v-model="email" required :disabled="showSummary" />

      <label>Sexe:</label>
      <div>
        <input type="radio" v-model="gender" value="female" id="female" :disabled="showSummary" />
        <label for="female">Femme</label>
      </div>
      <div>
        <input type="radio" v-model="gender" value="male" id="male" :disabled="showSummary" />
        <label for="male">Homme</label>
      </div>

      <label>Institution<strong>*</strong>:</label>
      <input type="text" v-model="institution" required :disabled="showSummary" />

      <label>Adresse<strong>*</strong>:</label>
      <input type="text" v-model="address" required  :disabled="showSummary"/>

      <label>Pays<strong>*</strong>:</label>
      <input type="text" v-model="country" required :disabled="showSummary" />

      <label>Code Postal:</label>
      <input type="text" v-model="postalCode" :disabled="showSummary" />

      <label>Ville<strong>*</strong>:</label>
      <input type="text" v-model="city" required :disabled="showSummary" />

      <label>Page Web Personnelle:</label>
      <input type="text" v-model="personalWebsite" :disabled="showSummary" />

      <label>Page Web Institution:</label>
      <input type="text" v-model="institutionWebsite" :disabled="showSummary" />

      <hr />

      <h2>{{ registrationSubtitle }}</h2>

      <div>
        <input type="radio" v-model="registrationType" value="student" id="student" :disabled="showSummary" />
        <label for="student">Étudiant (150 Euros)</label>
      </div>
      <div>
        <input type="radio" v-model="registrationType" value="academic" id="academic" :disabled="showSummary" />
        <label for="academic">Académique (200 Euros)</label>
      </div>
      <div>
        <input type="radio" v-model="registrationType" value="business" id="business" :disabled="showSummary" />
        <label for="business">Entreprise (300 Euros)</label>
      </div>

      <hr />

      <h2>{{ accommodationSubtitle }}</h2>

      <div>
        <input type="radio" v-model="accommodation" value="withReservation" id="withReservation" :disabled="showSummary" />
        <label for="withReservation">Avec réservation (150 Euros)</label>
      </div>
      <div>
        <input type="radio" v-model="accommodation" value="withoutReservation" :disabled="showSummary" id="withoutReservation" />
        <label for="withoutReservation">Sans réservation (0 Euros)</label>
      </div>

      <hr />

      <button type="submit">Pré-valider</button>
    </form>

    <!-- Récapitulatif de l'inscription -->
    <div v-if="showSummary" class="summary">
      <h2>Récapitulatif de l'inscription :</h2>
      <p v-if="gender === 'male'">Bonjour Monsieur {{ firstName }} {{ lastName }},</p>
      <p v-else-if="gender === 'female'">Bonjour Madame {{ firstName }} {{ lastName }},</p>
      <p>
        Vous avez procédé à une inscription pour la conférence #MaConf2020.
      </p>

      <p>Le détail de votre enregistrement est le suivant :</p>
      <ul>
        <li><strong>Type d'inscription :</strong> {{ getRegistrationTypeLabel() }}</li>
        <li><strong>Type d'hébergement :</strong> {{ getAccommodationLabel() }}</li>
      </ul>

      <p><strong>Le montant total est de {{ calculateTotalAmount() }} €</strong></p>

      <p>
        Un email vous sera envoyé prochainement à l'adresse : "{{ email }}" pour la mise en paiement de votre inscription.
      </p>

      <p>
        Merci de consulter votre messagerie et de procéder au règlement de votre inscription.
      </p>

      <p>
        En vous remerciant de votre inscription, à très bientôt à la conférence #MaConf2020.
      </p>

      <!-- Boutons de confirmation et de modification -->
      <button @click="confirmRegistration">Confirmer</button>
      <button @click="editRegistration">Modifier</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      conferenceTitle: "Inscription pour #maConf2020",
      conferenceSubtitle: "Qui êtes-vous?",
      firstName: "",
      lastName: "",
      email: "",
      gender: "",
      institution: "",
      address: "",
      country: "",
      postalCode: "",
      city: "",
      personalWebsite: "",
      institutionWebsite: "",
      registrationSubtitle: "Quelle inscription souhaitez-vous?",
      registrationType: "",
      accommodationSubtitle: "Quel hébergement souhaitez-vous?",
      accommodation: "",
      showErrors: false,
      showSummary: false,
    };
  },
  methods: {
    submitForm() {
      // Vérifier si des informations obligatoires sont manquantes ou si l'adresse e-mail est invalide
      if (
        !this.firstName ||
        !this.lastName ||
        !this.email ||
        !this.gender ||
        !this.institution ||
        !this.address ||
        !this.country ||
        !this.city ||
        !this.registrationType ||
        !this.accommodation ||
        !this.isValidEmail(this.email)
      ) {
        this.showErrors = true;
      } else {
        // Réinitialiser l'affichage des erreurs après validation réussie
        this.showErrors = false;

        // Afficher le récapitulatif de l'inscription
        this.showSummary = true;
      }
    },
    isValidEmail(email) {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      return emailRegex.test(email);
    },
    calculateTotalAmount() {
      let registrationCost = 0;
      let accommodationCost = 0;

      if (this.registrationType === "student") {
        registrationCost = 150;
      } else if (this.registrationType === "academic") {
        registrationCost = 200;
      } else if (this.registrationType === "business") {
        registrationCost = 300;
      }

      if (this.accommodation === "withReservation") {
        accommodationCost = 150;
      } else if (this.accommodation === "withoutReservation") {
        accommodationCost = 0;
      }

      return registrationCost + accommodationCost;
    },
    confirmRegistration() {
      console.log("Inscription confirmée !");
      // Vous pouvez ajouter ici le traitement pour la confirmation de l'inscription,
      // par exemple, en envoyant un email de confirmation à l'utilisateur, etc.
    },
    editRegistration() {
      console.log("Modifier l'inscription");
      // Vous pouvez ajouter ici le traitement pour permettre à l'utilisateur de modifier son inscription,
      // par exemple, en masquant le récapitulatif et en réactivant les champs de saisie pour l'édition.
      this.showSummary = false; // Masquer le récapitulatif pour permettre la modification
    },
    getRegistrationTypeLabel() {
      if (this.registrationType === "student") {
        return "Étudiant (150 Euros)";
      } else if (this.registrationType === "academic") {
        return "Académique (200 Euros)";
      } else if (this.registrationType === "business") {
        return "Entreprise (300 Euros)";
      } else {
        return "";
      }
    },
    getAccommodationLabel() {
      if (this.accommodation === "withReservation") {
        return "Avec réservation (150 Euros)";
      } else if (this.accommodation === "withoutReservation") {
        return "Sans réservation (0 Euros)";
      } else {
        return "";
      }
    },
  },
};
</script>

<style>
/* Ajoutez vos styles CSS personnalisés ici */
.error {
  color: red;
  margin-bottom: 10px;
}

.summary {
  border: 1px solid #ccc;
  padding: 10px;
}
</style>
