<template>
  <section >
          <div>
               <!-- <span>{{windowWidth}}</span> -->
               <h4> {{total}}  {{ type == 'hospital'? `Hospitals` : `Doctor` }} available  </h4>
               <span>
                 <a href="javascript:;" @click="prev" v-show="page > 1"> prev </a>        
               </span>
               <span>&nbsp;&nbsp;</span>
               <span>
                 <a href="javascript:;" @click="next" v-show="page < last_page"> next  </a>
               </span>
          </div>
        <!-- <div class="container"> -->
            <div class="search-result-container">
                <div v-for="(item,idx) in item_list" :key="idx" @click="gotoDetail(item)" >
                    <div>
                        <h3> {{item.name}} </h3><br>
                        <h4>{{  type == 'hospital' ? item.address : item.hospital_name}}<span class="skills"></span></h4>
                    </div>    
                </div>
            </div>
            <!-- <div >
            <div class="row">
                <div class="col-md-3 col-sm-6" v-for="(item,idx) in item_list" :key="idx" @click="gotoDetail(item)">
                <div class="team_member">
                    <img src="/assets/images/team/team-1.jpg" alt="team 1">
                    <div class="team_details">
                    <h3>{{item.name}}<span class="skills"></span></h3>
                    <h4>{{``}}<span class="skills"></span></h4>
                    <ul class="team_socials">
                        <h4>{{  type == 'hospital' ? item.address : item.hospital_name}}<span class="skills"></span></h4>
                    </ul>
                    </div>
                </div>
                </div>
            </div>

            </div> -->
  </section>
</template>

<script>
    import axios from "axios"
    import {mapGetters} from "vuex"
    export default {
      name: "Team",
      data(){
        return {
          type : 'Hospital' ,
          item_list : [],
          page : 1 ,
          limit : 100 ,
          last_page : 1 ,
          current_page : 1 ,
          total : 0 ,
          windowWidth: 0 , 
          txt : ``,
          division_id : `` ,
          district_id : `` ,
          upazila_id  : `` ,
        }
      },
      mounted(){
        // 
        this.getItems(),
        this.$nextTick(() => {
            window.addEventListener('resize', this.onResize);
        })
      },

      beforeDestroy() { 
          window.removeEventListener('resize', this.onResize); 
      },
      computed:{
        ...mapGetters('search',['search_info']),
      },
      watch: {
        windowWidth(newWidth, oldWidth) {
        this.txt = `it changed to ${newWidth} from ${oldWidth}`;
        },
        search_info(val){
          this.respondToSearchFilterChange()
        }
      },

      methods:{
        gotoDetail({id}){
            if (this.type == 'doctor'){
                 this.$router.push(`/doctor-detail/${id}`)
            }else { // hospital
                this.$router.push(`/hospital-detail/${id}`)
            }
        },
        onResize() {
          this.windowWidth = window.innerWidth
        },
        prev(){
          this.page = this.page > 0 ? (this.page - 1 ) : this.page 
          this.getItems(this.page)
        },
        next(){
          this.page = this.page < this.last_page ? (this.page + 1) : this.page 
          this.getItems(this.page)
        },

        getItems(page=1){
          this.$axios.get(`api/frontend/hospital/?page=${page}&limit=${this.limit}&type=${this.type}`,{
            params : {
              'division_id' : this.division_id ,
              'district_id' : this.district_id ,
              'upazila_id'  : this.upazila_id  ,
            }
          }).then(response=>{
             this.item_list = response.data?.data
             this.total     = response.data?.meta?.total 
             this.last_page     = response.data?.meta?.last_page 
             this.current_page     = response.data?.meta?.current_page 
             this.page  = this.current_page
          }).catch(error=>{
            alert(`error in hospial`)
          })
        },
        respondToSearchFilterChange(){
          this.type        = this.search_info.selected_type 
          this.division_id = this.search_info.selected_division
          this.district_id = this.search_info.selected_district
          this.upazila_id  = this.search_info.selected_upazila
          this.getItems()

        }
      }
    }
</script>

<style scoped>
.search-result-container{
    display:flex ;
    flex-wrap: wrap ;
    /* justify-content: space; */
}
.search-result-container > div{
    padding : 20px;
    margin : 20px;
    background : lightgrey ;
}

</style>
