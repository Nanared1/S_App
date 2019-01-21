<template>
  <div class="index">
    <h1 class="title">{{ title }}</h1>
    <div class="container">

      <!-- Search box and button -->
      <form class="seachBox">
        <div class="row">
          <div class="col-sm-10 col-xs-6">
            <input class="form-control" type="text" placeholder="item" name="searchItem" v-model="searchItem" @keydown.enter.prevent="getSearch(searchItem)">
          </div>
          <div class="col-sm-2 col-xs-6">
            <span id="searchBtn">
              <i class="fas fa-search" @click="getSearch(searchItem)"></i>
            </span>
          </div>
        </div>
      </form>

      <!-- Search Title and Body -->
      <div class="row info" v-for="(c, index) in searchTitle.length-1" :key="index" >

        <div class="col-sm-4">
          <!--  Title-->
          <div class="row">
            <div class="col-sm-1">
              <i class="far fa-star star" v-bind:class="{star_green: isActive}" @click="addFavourite(searchTitle[c], searchBody[c])"></i>
            </div>
            <div class="col-sm-10">
              <span>{{ searchTitle[c] }}</span>
            </div>
          </div>
        </div>

        <div class="col-sm-7">
          <!--  Body -->

          <ul>
            <span class="instruct">
              <span v-html='searchBody[c]'></span>
            </span>
          </ul>
        </div>
      </div>

    </div>

    <!-- Favourites -->
    <div class="favs">
      <div class="container">
        <h3 id="favourites">Favourites</h3>


        <div class="row" v-for="(i, index) in favourites.length-1" :key="index">

          <div class="col-sm-4">


            <div class="row">

              <div class="col-sm-1">

                <i class="far fa-star" v-bind:class="{star_green: isActive}" @click="removeFavourite(favourites[i])"></i>

              </div>
              <div class="col-sm-10">
                <span>{{ favourites[i][0] }}</span>
              </div>

            </div>


          </div>

          <div class="col-sm-7">
            <ul>
              <span class="instruct">
                <span v-html='favourites[i][1]'></span>
              </span>
            </ul>
          </div>

        </div>


      </div>
    </div>


  </div>

</template>

<script>
// import Json file from data folder
import db from '@/data/index'

export default {
  name: 'Index',
  data () {
    return {
      title: 'Toronto Waste Lookup',
      searchItem: null, // value to retrieve search from html
      favourites: [[]],
      searchTitle: [0],
      searchBody : [0],
      jsonData : db,
      isActive: false, // check if the item is a Favourite. {{ not working well at the moment}}
    }
  },
  methods:{
    // search Json file title and keywords to find appropriate data
    getSearch(searchWord){
      this.searchTitle = [0];
      this.searchBody = [0];
      var category = ''; // variable to ensure a category is used once
      for(let d in this.jsonData){
        // keywords to search for item
        let keys = this.jsonData[d]['title'] + ' ' + this.jsonData[d]['keywords'];
        // search for item in keys
        let n = keys.search(searchWord);

        if(n > -1){
          // if item is found, ensure that category has not already been used
          if(category.search(this.jsonData[d]['category']) == -1){
            // save data to be displayed on html component
            this.searchTitle.push(this.jsonData[d]['title']);
            this.searchBody.push( $('<textarea />').html(this.jsonData[d]['body']).text());
            category += (' '+this.jsonData[d]['category']);
          }
        }
      }
    },
    // add trash to favourites
    addFavourite(sd, str){
      this.favourites.push([sd, str]);
      this.isActive = true;
    },
    // remove trash from favourites
    removeFavourite(lst){
      this.favourites = this.favourites.filter( list => {
        return list !== lst;
      });
      this.isActive = false;
    }
  }

  // Fix
  // - Favourite green * to be assigned to only favourites
  // - change cursor to pointor when hovered over search button
  // - item should not be in the Favourite twice or more
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div .title{
  padding: 30px 0px;
  background: #34e89e;  /* fallback for old browsers */
  background: -webkit-linear-gradient(to left, #34e89e, #0f3443); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(to left, #34e89e, #0f3443); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  text-align: center;
  color: white;
  width: 100%;
  margin-bottom: 20px;
}
.form-control{
  border-radius: 0px;
  height: 3.0em;
}
.seachBox{
  margin-bottom: 40px;
}
#searchBtn{
  font-size: 2.0em;
  padding: 0px 8px 0px 8px;
  color: white;
  background-color: #40A975;
  position: absolute;
}
.info{
  margin-bottom: 15px;

}
.favs{
  background-color: #F9FAFA;
}
#favourites{
  text-align: left;
  color: #40A975;
  font-weight: bold;
}

.bins{
  font-size: 17px;
  list-style: none;
  text-align: left;
}
.instruct{
  margin-bottom: 10px;
}
.star_green{
  color: green;
}
</style>
