<template>
  <div>
    <div class="container" style="margin-top: 8px; margin-left: 8px; padding: 8px">
      <div @mouseover="showEdit=true" @mouseout="showEdit=false" class="align-items-center row text-white bg-dark">
        <div class="col">
          <div>
            <div v-for="(src, index) in imgs" :key="index" class="pictures" @click="() => showImg(index)">
              <img v-if="hasPicture" class="img-thumbnail icon-button border-0"
                   style="max-height: 200px; max-width: 200px" :src="src.src" alt="Kalapilt"/>

              <img v-else src="../assets/images.png" class="img-thumbnail"
                   width="200" height="200" alt="Kalapilt">
            </div>
          </div>
          <vue-easy-lightbox
              :visible="visible"
              :imgs="imgs"
              :index="index"
              @hide="handleHide"
          ></vue-easy-lightbox>
        </div>
        <div class="col">
          <div class="row justify-content-center">
            <div class="col">
              Kalaliik: {{ fish.speciesName }}
            </div>
          </div>
          <div class="row">
            <div class="col">
              Pikkus: {{ fish.length }} cm
            </div>
            <div class="col">
              kaal: {{ fish.weight }} g
            </div>
          </div>
          <div class="row">
            <div class="col">
              Kuupäev: {{ fish.date }}
            </div>
          </div>
          <div class="row justify-content-center">
            Püügikoht: {{ fish.locationName }}
          </div>
          <div v-if="fish.released" class="row justify-content-center">
            Vabastatud
          </div>
        </div>
        <div class="col-4" maxlength="1000" style="overflow-y: auto; height:100px">
          Kommentaar: {{ fish.comment }}
        </div>
        <div v-if="activeUsername===fish.userName" class="col">
          <div v-show="!showEdit" style="color:#0d6efc">{{ fish.userName }}</div>


          <div class="row">
            <div v-show="showEdit" class="col">
              <span>Muuda      </span>
              <router-link :to="{name: 'fishRoute', query: {fishId: fish.fishId }}">
                <font-awesome-icon class="fa-2xl" icon="fa-regular fa-pen-to-square"/>
              </router-link>
            </div>
            <div v-show="showEdit" class="col">
              <span>Kustuta      </span>
              <font-awesome-icon v-on:click="askDeleteFish" class="fa-2xl icon-button" icon="fa-regular fa-trash-can"
                                 style="color: crimson"/>
            </div>
          </div>

        </div>

        <div v-else class="col">
          {{ fish.userName }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'FishDetails',

  props: {
    fish: {
      comment: '',
      date: "2023-02-24",
      fishId: Number,
      isPublic: Boolean,
      length: Number,
      locationName: '',
      picture: '',
      released: Boolean,
      speciesName: '',
      userName: '',
      weight: Number,
    }
  },

  data: function () {
    return {

      hasPicture: !(this.fish.picture === '' || this.fish.picture == null),
      visible: false,

      index: 0,
      imgs: [
        {
          src: this.fish.picture,
          title: this.fish.speciesName + ": " + this.fish.locationName + ", " + this.fish.date
        }
      ],

      activeUsername: sessionStorage.getItem('userName'),
      showEdit: false
    }
  },

  methods: {

    askDeleteFish: function () {
      if (confirm('Oled sa kindel, et soovid kala kustutada?')) {
        this.deleteFish()
      } else {
      }
    },

    deleteFish: function () {
      this.$http.delete("/fish", {
            params: {
              fishId: this.fish.fishId
            }
          }
      ).then(response => {
        this.messageSuccess = 'Kala kustutatud!'
        setTimeout(() => {
          this.$parent.getFishies()
        }, 2000)
      }).catch(error => {
        console.log(error)
      })
    },

    showImg(index) {
      this.index = index;
      if (this.hasPicture) {
        this.visible = true;
      }

    },
    handleHide() {
      this.visible = false;
    },
  },


}
</script>