<template>
  <v-app>
    <v-main>
      <v-form v-model="form.valid" @submit.prevent validate-on="input">
        <v-container>
          <p class="text-h5">Pferd/Horse</p>
          <v-divider class="mb-4"></v-divider>

          <v-text-field label="Name" variant="outlined" v-model="form.horse.name" required :rules="rules"></v-text-field>
          <v-text-field label="Registrier-Nr." variant="outlined" v-model="form.horse.registernr"
            :rules="rules"></v-text-field>
          <v-radio-group label="Geschlecht" inline v-model="form.horse.gender" :rules="rules">
            <v-radio label="Hengst" value="Hengst"></v-radio>
            <v-radio label="Wallach" value="Wallach"></v-radio>
            <v-radio label="Stute" value="Stute"></v-radio>
          </v-radio-group>
          <v-text-field label="Farbe" variant="outlined" v-model="form.horse.color" :rules="rules"></v-text-field>
          <v-text-field label="Geburtsdatum" variant="outlined" v-model="form.horse.birthday"
            :rules="rules"></v-text-field>
          <v-textarea label="Eigentümer-Name und Adresse" variant="outlined" v-model="form.horse.owner"
            :rules="rules"></v-textarea>
        </v-container>

        <v-container class="form-select border-dark-100 m-4 p-4 border-2 rounded-lg" id="horse"
          v-for="(rider, index) in form.riders" :key="rider.id">
          <p class="text-h5">
            {{ index + 1 }}. Reiter/Vorsteller/Exhibitor
            <v-icon icon="mdi-close" v-if="index != 0" @click="removeRider(rider.id)"></v-icon>
          </p>
          <v-divider class="mb-4"></v-divider>

          <v-text-field label="Name" variant="outlined" v-model="rider.name" :rules="rules"></v-text-field>
          <v-text-field label="Straße/Haus Nr." variant="outlined" v-model="rider.street" :rules="rules"></v-text-field>
          <v-text-field label="PLZ/Ort" variant="outlined" v-model="rider.plz" :rules="rules"></v-text-field>
          <v-text-field label="Telefon/Fax/e-Mail" variant="outlined" v-model="rider.contact"
            :rules="rules"></v-text-field>

          <v-select class="" v-model="rider.categories" label="Kategorien" :items="categories" multiple chips
            :rules="rules">
          </v-select>
        </v-container>
        <v-container>
          <v-btn class="w-72 h-12 block m-4 p-4 border-2 rounded-lg border-dark-100 text-center content-center"
            v-on:click="addRider()">
            Weiteren Reiter hinzufügen
          </v-btn>
        </v-container>

        <v-container class="">
          <p class="text-h5">Allgemein</p>
          <v-divider class="mb-4"></v-divider>

          <v-text-field label="E-Mail für Bestätigung" variant="outlined" v-model="email.cc" required
            :rules="rules"></v-text-field>

          <p class="text-subtitle-1">
            Officecharge (1 Pferd x {{ form.riders.length }} Reiter):
            {{ form.riders.length * 20 }}€
          </p>
          <v-checkbox label="Videocharge [20€]" v-model="form.extras.videocharge"></v-checkbox>

          <p class="text-h6">Anzahl Boxen / No. Stables:</p>

          <v-container>
            <v-text-field v-model="form.extras.stableAC" type="number" style="width: 80px" density="compact" hide-details
              variant="outlined" class="float-left mr-4"></v-text-field>
            <p class="text-subtitle-1">
              Festbox / firm box stable A-C (230€)
            </p>
          </v-container>

          <v-container>
            <v-text-field v-model="form.extras.stableHI" type="number" style="width: 80px" density="compact" hide-details
              variant="outlined" class="float-left mr-4"></v-text-field>
            <p class="text-subtitle-1">
              Festbox / firm box stable H-I (170€)
            </p>
          </v-container>

          <v-container>
            <v-text-field v-model="form.extras.camper" type="number" style="width: 80px" density="compact" hide-details
              variant="outlined" class="float-left mr-4"></v-text-field>
            <p class="text-subtitle-1">Camper (100€)</p>
            <v-text-field v-model="form.extras.camperKennzeichen" style="width: 200px" density="compact" hide-details
              variant="outlined" class="" label="Kennzeichen" :rules="form.extras.camper ? rules : [true]"></v-text-field>
          </v-container>

          <v-checkbox label="Danke für das 50 Euro VWB-Jugendsponsering!"
            v-model="form.extras.jugendsponsoring"></v-checkbox>

          <p class="text-subtitle-1">Gesamtsumme: {{ totalPrice }}€</p>
        </v-container>
        <v-container>
          <v-btn type="submit" @click="sendEmail()">E-Mail Senden</v-btn>
        </v-container>
      </v-form>
    </v-main>
  </v-app>
</template>

<script>

export default {

  data() {
    return {
      form: {
        valid: false,
        horse: {
          name: "",
          registernr: "",
          gender: "",
          color: "",
          birthday: "",
          owner: "",
        },
        riders: [
          {
            id: 1,
            name: "",
            street: "",
            plz: "",
            contact: "",
            categories: [],
          },
        ],
        extras: {
          videocharge: false,
          jugendsponsoring: false,
          stableAC: 0,
          stableHI: 0,
          camper: 0,
          camperKennzeichen: "",
        },
      },
      categories: [
        {
          value: "5050",
          title: "Trail Open [90€]",
          category: "VWB Golden Series (8.000 Euro)",
          price: 90,
        },
        {
          value: "5051",
          title: "Pleasure Youth/Amateur [90€]",
          category: "VWB Golden Series (8.000 Euro)",
          price: 90,
        },
        {
          value: "5052",
          title: "Showmanship Open [90€]",
          category: "VWB Golden Series (8.000 Euro)",
          price: 90,
        },
        {
          value: "5053",
          title: "Horsemanship Open [90€]",
          category: "VWB Golden Series (8.000 Euro)",
          price: 90,
        },
        {
          value: "5054",
          title: "Ranch Riding [90€]",
          category: "VWB Golden Series (8.000 Euro)",
          price: 90,
        },
        {
          value: "5055",
          title: "Reining Youth/Amateur [90€]",
          category: "VWB Golden Series (8.000 Euro)",
          price: 90,
        },
        {
          value: "5056",
          title: "Limited Trail Series [90€]",
          category: "VWB Golden Series (8.000 Euro)",
          price: 90,
        },
        {
          value: "5057",
          title: "Western Riding Series [90€]",
          category: "VWB Golden Series (8.000 Euro)",
          price: 90,
        },
      ],
      selection: { horse: {} },
      mailtoUrl: "",
      email: {
        address: "test@example.com", // HIER EMAIL EINFÜGEN AN DIE DIE EMAIL GESENDET WERDEN SOLL
        subject:
          "VWB-Nennformular | Turnier: Bavarian Spring Classic 19.-24.April 2022",
        cc: "",
        body: "",
      },
      rules: [
        (value) => {
          if (value) return true;

          return "Pflichtfeld";
        },
      ],
    };
  },
  methods: {
    addRider: function () {
      this.form.riders.push({
        id: this.form.riders[this.form.riders.length - 1].id + 1,
        categories: [],
        name: "",
        street: "",
        plz: "",
        contact: "",
      });
    },
    removeRider: function (riderId) {
      this.form.riders = this.form.riders.filter((rider) => {
        console.log(rider.id);
        console.log(riderId);
        return rider.id !== riderId;
      });
    },
    sendEmail: function () {
      if (!this.form.valid) {
        return;
      }
      let riderString = "";

      console.log(this.form);

      this.form.riders.forEach((rider, index) => {
        riderString += index + 1 + ". Reiter/Vorsteller/Exhibitor\n";
        riderString += "    Name: " + rider.name + "\n";
        riderString += "    Straße/Haus Nr.: " + rider.street + "\n";
        riderString += "    PLZ/Ort: " + rider.plz + "\n";
        riderString += "    Telefon/Fax/e-Mail: " + rider.contact + "\n";
        riderString +=
          "    Kategorien: " +
          rider.categories
            .map(
              (id) =>
                this.categories.find((category) => category.value === id)
                  .title
            )
            .join(", ") +
          "\n";
        riderString += "\n";
      });

      this.email.body = `Pferd
    Name: ${this.form.horse.name}
    Registrier-Nr.: ${this.form.horse.registernr}
    Geschlecht: ${this.form.horse.gender}
    Farbe: ${this.form.horse.color}
    Geburtsdatum: ${this.form.horse.birthday}
    Eigentümer-Name und Adresse: ${this.form.horse.owner.replace(
        /(\r\n|\n|\r)/gm,
        ", "
      )}

${riderString}
Allgemein:
    Officecharge: ${this.form.riders.length * 20}€
    Videocharge: ${this.form.extras.videocharge ? "Ja" : "Nein"} [${this.form.extras.videocharge ? 20 : 0
        }€]
    Anzahl Boxen / No. Stables:
        ${this.form.extras.stableAC} x Festbox / firm box stable A-C: [${this.form.extras.stableAC * 230
        }€]
        ${this.form.extras.stableHI} x Festbox / firm box stable H-I: [${this.form.extras.stableHI * 170
        }€]
        ${this.form.extras.camper} x Camper: [${this.form.extras.camper * 100}€]
        Kennzeichen Camper: ${this.form.extras.camperKennzeichen}

    VWM-Jugendsponsoring: ${this.form.extras.jugendsponsoring ? "Ja" : "Nein"
        } [${this.form.extras.jugendsponsoring ? 50 : 0}€]

Gesamtsumme: ${this.totalPrice}€
        `;

      this.mailtoUrl = "mailto:" + this.email.address;
      const keys = ["subject", "cc", "body"];
      const filteredKeys = keys.filter(
        (key) => this.email[key].trim().length > 0
      );
      if (filteredKeys.length > 0) {
        this.mailtoUrl += "?";
      }
      this.mailtoUrl += filteredKeys
        .map((key) => `${key}=${encodeURI(this.email[key].trim())}`)
        .join("&");
      window.open(this.mailtoUrl, "_blank");
    },
  },
  computed: {
    totalPrice() {
      let totalCategories = 0;
      this.form.riders.forEach((rider) => {
        rider.categories.forEach((category) => {
          totalCategories += this.categories.find(
            (c) => c.value === category
          ).price;
        });
      });

      const officeCharge = this.form.riders.length * 20;
      const videocharge = this.form.extras.videocharge ? 20 : 0;
      const jugendsponsoring = this.form.extras.jugendsponsoring ? 50 : 0;

      const stableAC = this.form.extras.stableAC * 230;
      const stableHI = this.form.extras.stableHI * 170;
      const camper = this.form.extras.camper * 100;

      return (
        totalCategories +
        officeCharge +
        videocharge +
        jugendsponsoring +
        stableAC +
        stableHI +
        camper
      );
    },
  },
}
</script>
